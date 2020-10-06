---
title: Obecný hostitel .NET
author: rick-anderson
description: Seznamte se s obecným hostitelem .NET Core, který je zodpovědný za spouštění a správu životního cyklu aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: 59cfae9ff619f8de894686c4b773d66e5cbe10ad
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754733"
---
# <a name="net-generic-host"></a><span data-ttu-id="5a3bd-103">Obecný hostitel .NET</span><span class="sxs-lookup"><span data-stu-id="5a3bd-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5a3bd-104">Šablony ASP.NET Core vytvoří obecného hostitele .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="5a3bd-105">Definice hostitele</span><span class="sxs-lookup"><span data-stu-id="5a3bd-105">Host definition</span></span>

<span data-ttu-id="5a3bd-106">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="5a3bd-107">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="5a3bd-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="5a3bd-108">protokolování</span><span class="sxs-lookup"><span data-stu-id="5a3bd-108">Logging</span></span>
* <span data-ttu-id="5a3bd-109">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="5a3bd-109">Configuration</span></span>
* <span data-ttu-id="5a3bd-110">`IHostedService` implementaci</span><span class="sxs-lookup"><span data-stu-id="5a3bd-110">`IHostedService` implementations</span></span>

<span data-ttu-id="5a3bd-111">Při spuštění hostitele zavolá <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> každou implementaci <xref:Microsoft.Extensions.Hosting.IHostedService> zaregistrovanou v kolekci hostovaných služeb kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="5a3bd-112">V rámci webové aplikace je jednou z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="5a3bd-113">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5a3bd-114">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="5a3bd-114">Set up a host</span></span>

<span data-ttu-id="5a3bd-115">Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve `Program` třídě.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="5a3bd-116">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-116">The `Main` method:</span></span>

* <span data-ttu-id="5a3bd-117">Volá `CreateHostBuilder` metodu pro vytvoření a konfiguraci objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="5a3bd-118">Volání `Build` a `Run` metody objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="5a3bd-119">Webové šablony ASP.NET Core generují následující kód pro vytvoření hostitele:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="5a3bd-120">Následující kód vytvoří úlohu jiného typu než HTTP s `IHostedService` implementací přidanou do kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-120">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="5a3bd-121">Pro úlohy HTTP `Main` je metoda stejná, ale `CreateHostBuilder` volání `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="5a3bd-122">Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="5a3bd-123">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že budou nalezeny `CreateHostBuilder` metody, které nakonfigurují hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="5a3bd-124">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="5a3bd-125">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="5a3bd-125">Default builder settings</span></span>

<span data-ttu-id="5a3bd-126"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="5a3bd-127">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="5a3bd-128">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="5a3bd-129">Proměnné prostředí s předponou `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="5a3bd-130">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-130">Command-line arguments.</span></span>
* <span data-ttu-id="5a3bd-131">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="5a3bd-132">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="5a3bd-133">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="5a3bd-134">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="5a3bd-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="5a3bd-135">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-135">Environment variables.</span></span>
  * <span data-ttu-id="5a3bd-136">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-136">Command-line arguments.</span></span>
* <span data-ttu-id="5a3bd-137">Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="5a3bd-138">Konzola</span><span class="sxs-lookup"><span data-stu-id="5a3bd-138">Console</span></span>
  * <span data-ttu-id="5a3bd-139">Ladit</span><span class="sxs-lookup"><span data-stu-id="5a3bd-139">Debug</span></span>
  * <span data-ttu-id="5a3bd-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="5a3bd-140">EventSource</span></span>
  * <span data-ttu-id="5a3bd-141">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="5a3bd-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="5a3bd-142">Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="5a3bd-143">`ConfigureWebHostDefaults`Metoda:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="5a3bd-144">Načte konfiguraci hostitele z proměnných prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="5a3bd-145">Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="5a3bd-146">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="5a3bd-147">Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="5a3bd-148">Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , pokud je rovno `true` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="5a3bd-149">Povolí integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-149">Enables IIS integration.</span></span> <span data-ttu-id="5a3bd-150">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="5a3bd-151">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5a3bd-152">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="5a3bd-152">Framework-provided services</span></span>

<span data-ttu-id="5a3bd-153">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="5a3bd-154">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="5a3bd-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="5a3bd-155">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5a3bd-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="5a3bd-156">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5a3bd-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="5a3bd-157">Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="5a3bd-158">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="5a3bd-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="5a3bd-159">Vložení <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> služby (dříve `IApplicationLifetime` ) do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="5a3bd-160">Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="5a3bd-161">Rozhraní obsahuje také `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="5a3bd-162">Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="5a3bd-163">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5a3bd-163">IHostLifetime</span></span>

<span data-ttu-id="5a3bd-164"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementace určuje, kdy se hostitel spustí a kdy se zastaví.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="5a3bd-165">Použije se Poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-165">The last implementation registered is used.</span></span>

<span data-ttu-id="5a3bd-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je výchozí `IHostLifetime` implementací.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="5a3bd-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="5a3bd-168">Naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="5a3bd-169">Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="5a3bd-170">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5a3bd-170">IHostEnvironment</span></span>

<span data-ttu-id="5a3bd-171">Vložením <xref:Microsoft.Extensions.Hosting.IHostEnvironment> služby do třídy získáte informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="5a3bd-172">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5a3bd-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="5a3bd-173">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="5a3bd-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="5a3bd-174">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="5a3bd-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="5a3bd-175">Webové aplikace implementují `IWebHostEnvironment` rozhraní, které dědí `IHostEnvironment` a přidává rozhraní [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5a3bd-176">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="5a3bd-176">Host configuration</span></span>

<span data-ttu-id="5a3bd-177">Konfigurace hostitele se používá pro vlastnosti <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="5a3bd-178">Konfigurace hostitele je k dispozici z [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="5a3bd-179">Po `ConfigureAppConfiguration` nahrazení se v `HostBuilderContext.Configuration` konfiguraci aplikace nahradí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="5a3bd-180">Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> na `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5a3bd-181">`ConfigureHostConfiguration` dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5a3bd-182">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5a3bd-183">Zprostředkovatel proměnné prostředí s argumenty předpony `DOTNET_` a příkazového řádku jsou obsaženy v `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5a3bd-184">U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="5a3bd-185">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5a3bd-186">Například hodnota proměnné prostředí pro se nastaví jako `ASPNETCORE_ENVIRONMENT` hodnota konfigurace hostitele pro `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5a3bd-187">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="5a3bd-188">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="5a3bd-188">App configuration</span></span>

<span data-ttu-id="5a3bd-189">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> metody `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5a3bd-190">`ConfigureAppConfiguration` dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5a3bd-191">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="5a3bd-192">Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="5a3bd-193">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="5a3bd-194">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="5a3bd-195">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="5a3bd-195">Settings for all app types</span></span>

<span data-ttu-id="5a3bd-196">V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="5a3bd-197">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="5a3bd-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5a3bd-198">ApplicationName</span></span>

<span data-ttu-id="5a3bd-199">Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="5a3bd-200">**Klíč**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5a3bd-201">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-201">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-202">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="5a3bd-203">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="5a3bd-204">K nastavení této hodnoty použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="5a3bd-205">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="5a3bd-205">ContentRoot</span></span>

<span data-ttu-id="5a3bd-206">Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="5a3bd-207">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="5a3bd-208">**Klíč**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5a3bd-209">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-209">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-210">**Výchozí**: složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="5a3bd-211">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="5a3bd-212">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseContentRoot` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="5a3bd-213">Další informace naleznete v tématech:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-213">For more information, see:</span></span>

* [<span data-ttu-id="5a3bd-214">Základy: kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="5a3bd-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="5a3bd-215">WebRoot</span><span class="sxs-lookup"><span data-stu-id="5a3bd-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="5a3bd-216">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="5a3bd-216">EnvironmentName</span></span>

<span data-ttu-id="5a3bd-217">Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="5a3bd-218">Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5a3bd-219">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="5a3bd-220">**Klíč**: `environment`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-220">**Key**: `environment`</span></span>  
<span data-ttu-id="5a3bd-221">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-221">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-222">**Výchozí**: `Production`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-222">**Default**: `Production`</span></span>  
<span data-ttu-id="5a3bd-223">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="5a3bd-224">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseEnvironment` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="5a3bd-225">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="5a3bd-225">ShutdownTimeout</span></span>

<span data-ttu-id="5a3bd-226">[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5a3bd-227">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-227">The default value is five seconds.</span></span>  <span data-ttu-id="5a3bd-228">Po uplynutí časového limitu hostitel:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="5a3bd-229">Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="5a3bd-230">Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="5a3bd-231">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="5a3bd-232">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="5a3bd-233">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="5a3bd-234">**Klíč**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="5a3bd-235">**Zadejte**: `int`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-235">**Type**: `int`</span></span>  
<span data-ttu-id="5a3bd-236">**Výchozí hodnota**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="5a3bd-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="5a3bd-237">**Proměnná prostředí**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="5a3bd-238">K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="5a3bd-239">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="5a3bd-240">Zakázat opětovné načtení konfigurace aplikace při změně</span><span class="sxs-lookup"><span data-stu-id="5a3bd-240">Disable app configuration reload on change</span></span>

<span data-ttu-id="5a3bd-241">Ve [výchozím nastavení](xref:fundamentals/configuration/index#default) *appsettings.jsv* a *appSettings. { Prostředí}. JSON* se při změně souboru znovu načte.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-241">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="5a3bd-242">Chcete-li zakázat toto opakované načítání v ASP.NET Core 5,0 nebo novějším, nastavte `hostBuilder:reloadConfigOnChange` klíč na `false` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-242">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="5a3bd-243">**Klíč**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-243">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="5a3bd-244">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-244">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5a3bd-245">**Výchozí**: `true`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-245">**Default**: `true`</span></span>  
<span data-ttu-id="5a3bd-246">**Argument příkazového řádku**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-246">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="5a3bd-247">**Proměnná prostředí**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-247">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="5a3bd-248">Oddělovač dvojtečky ( `:` ) nefunguje s hierarchickými klíči proměnné prostředí na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-248">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="5a3bd-249">Další informace naleznete v tématu [proměnné prostředí](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-249">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="5a3bd-250">Nastavení pro Web Apps</span><span class="sxs-lookup"><span data-stu-id="5a3bd-250">Settings for web apps</span></span>

<span data-ttu-id="5a3bd-251">Některá nastavení hostitele se vztahují jenom na úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-251">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="5a3bd-252">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-252">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="5a3bd-253">`IWebHostBuilder`K dispozici jsou metody rozšíření pro tato nastavení.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-253">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="5a3bd-254">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` , je instancí `IWebHostBuilder` , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-254">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="5a3bd-255">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="5a3bd-255">CaptureStartupErrors</span></span>

<span data-ttu-id="5a3bd-256">`false`V důsledku dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-256">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="5a3bd-257">Když `true` hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-257">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="5a3bd-258">**Klíč**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-258">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="5a3bd-259">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-259">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5a3bd-260">**Výchozí**: výchozí nastavení `false` , pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-260">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="5a3bd-261">**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-261">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="5a3bd-262">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-262">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="5a3bd-263">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="5a3bd-263">DetailedErrors</span></span>

<span data-ttu-id="5a3bd-264">Pokud je povoleno nebo když je prostředí `Development` , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-264">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="5a3bd-265">**Klíč**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-265">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="5a3bd-266">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-266">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5a3bd-267">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-267">**Default**: `false`</span></span>  
<span data-ttu-id="5a3bd-268">**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-268">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="5a3bd-269">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-269">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="5a3bd-270">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="5a3bd-270">HostingStartupAssemblies</span></span>

<span data-ttu-id="5a3bd-271">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-271">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="5a3bd-272">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-272">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="5a3bd-273">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-273">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="5a3bd-274">**Klíč**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-274">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="5a3bd-275">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-275">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-276">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="5a3bd-276">**Default**: Empty string</span></span>  
<span data-ttu-id="5a3bd-277">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-277">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="5a3bd-278">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-278">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="5a3bd-279">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="5a3bd-279">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="5a3bd-280">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-280">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="5a3bd-281">**Klíč**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-281">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="5a3bd-282">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-282">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-283">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="5a3bd-283">**Default**: Empty string</span></span>  
<span data-ttu-id="5a3bd-284">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-284">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="5a3bd-285">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="5a3bd-286">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="5a3bd-286">HTTPS_Port</span></span>

<span data-ttu-id="5a3bd-287">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-287">The HTTPS redirect port.</span></span> <span data-ttu-id="5a3bd-288">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-288">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="5a3bd-289">**Klíč**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-289">**Key**: `https_port`</span></span>  
<span data-ttu-id="5a3bd-290">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-290">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-291">**Výchozí**hodnota: výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-291">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="5a3bd-292">**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-292">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="5a3bd-293">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-293">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="5a3bd-294">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="5a3bd-294">PreferHostingUrls</span></span>

<span data-ttu-id="5a3bd-295">Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným pomocí a `IWebHostBuilder` místo adres URL nakonfigurovaných `IServer` implementací.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-295">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="5a3bd-296">**Klíč**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-296">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="5a3bd-297">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5a3bd-298">**Výchozí**: `true`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-298">**Default**: `true`</span></span>  
<span data-ttu-id="5a3bd-299">**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-299">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="5a3bd-300">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-300">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="5a3bd-301">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="5a3bd-301">PreventHostingStartup</span></span>

<span data-ttu-id="5a3bd-302">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-302">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="5a3bd-303">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-303">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="5a3bd-304">**Klíč**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-304">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="5a3bd-305">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-305">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5a3bd-306">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-306">**Default**: `false`</span></span>  
<span data-ttu-id="5a3bd-307">**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-307">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="5a3bd-308">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-308">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="5a3bd-309">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="5a3bd-309">StartupAssembly</span></span>

<span data-ttu-id="5a3bd-310">Sestavení, ve kterém se má hledat `Startup` Třída</span><span class="sxs-lookup"><span data-stu-id="5a3bd-310">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="5a3bd-311">**Klíč**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-311">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="5a3bd-312">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-312">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-313">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="5a3bd-313">**Default**: The app's assembly</span></span>  
<span data-ttu-id="5a3bd-314">**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-314">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="5a3bd-315">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-315">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="5a3bd-316">`UseStartup` může převzít název sestavení ( `string` ) nebo typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-316">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="5a3bd-317">Pokud `UseStartup` je voláno více metod, má poslední z nich přednost.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-317">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="5a3bd-318">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="5a3bd-318">URLs</span></span>

<span data-ttu-id="5a3bd-319">Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-319">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="5a3bd-320">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-320">For example, `http://localhost:123`.</span></span> <span data-ttu-id="5a3bd-321">Pomocí příkazu " \* " určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-321">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="5a3bd-322">Protokol ( `http://` nebo `https://` ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-322">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="5a3bd-323">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-323">Supported formats vary among servers.</span></span>

<span data-ttu-id="5a3bd-324">**Klíč**: `urls`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-324">**Key**: `urls`</span></span>  
<span data-ttu-id="5a3bd-325">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-325">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-326">**Výchozí**: `http://localhost:5000` a `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-326">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="5a3bd-327">**Proměnná prostředí**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-327">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="5a3bd-328">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-328">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="5a3bd-329">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-329">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="5a3bd-330">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-330">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="5a3bd-331">WebRoot</span><span class="sxs-lookup"><span data-stu-id="5a3bd-331">WebRoot</span></span>

<span data-ttu-id="5a3bd-332">Vlastnost [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) Určuje relativní cestu ke statickým assetům aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-332">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="5a3bd-333">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-333">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="5a3bd-334">**Klíč**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="5a3bd-335">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-335">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-336">**Výchozí**: výchozí hodnota je `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="5a3bd-337">Cesta k *obsahu {root}/wwwroot* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-337">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="5a3bd-338">**Proměnná prostředí**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-338">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="5a3bd-339">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseWebRoot` na `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-339">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="5a3bd-340">Další informace naleznete v tématech:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-340">For more information, see:</span></span>

* [<span data-ttu-id="5a3bd-341">Základy: web root</span><span class="sxs-lookup"><span data-stu-id="5a3bd-341">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="5a3bd-342">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="5a3bd-342">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="5a3bd-343">Správa životnosti hostitele</span><span class="sxs-lookup"><span data-stu-id="5a3bd-343">Manage the host lifetime</span></span>

<span data-ttu-id="5a3bd-344">Voláním metod v sestavené <xref:Microsoft.Extensions.Hosting.IHost> implementaci spustíte a zastavíte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-344">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="5a3bd-345">Tyto metody ovlivňují všechny  <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-345">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5a3bd-346">Spustit</span><span class="sxs-lookup"><span data-stu-id="5a3bd-346">Run</span></span>

<span data-ttu-id="5a3bd-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="5a3bd-348">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-348">RunAsync</span></span>

<span data-ttu-id="5a3bd-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="5a3bd-350">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-350">RunConsoleAsync</span></span>

<span data-ttu-id="5a3bd-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="5a3bd-352">Spustit</span><span class="sxs-lookup"><span data-stu-id="5a3bd-352">Start</span></span>

<span data-ttu-id="5a3bd-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="5a3bd-354">StartAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-354">StartAsync</span></span>

<span data-ttu-id="5a3bd-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí hostitele a vrátí <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="5a3bd-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> je volána na začátku `StartAsync` , což čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5a3bd-357">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-357">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="5a3bd-358">StopAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-358">StopAsync</span></span>

<span data-ttu-id="5a3bd-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusy o zastavení hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="5a3bd-360">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="5a3bd-360">WaitForShutdown</span></span>

<span data-ttu-id="5a3bd-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například pomocí <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="5a3bd-362">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-362">WaitForShutdownAsync</span></span>

<span data-ttu-id="5a3bd-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="5a3bd-364">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="5a3bd-364">External control</span></span>

<span data-ttu-id="5a3bd-365">Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-365">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="5a3bd-366">Šablony ASP.NET Core vytvoří obecného hostitele .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-366">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="5a3bd-367">Definice hostitele</span><span class="sxs-lookup"><span data-stu-id="5a3bd-367">Host definition</span></span>

<span data-ttu-id="5a3bd-368">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-368">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="5a3bd-369">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="5a3bd-369">Dependency injection (DI)</span></span>
* <span data-ttu-id="5a3bd-370">protokolování</span><span class="sxs-lookup"><span data-stu-id="5a3bd-370">Logging</span></span>
* <span data-ttu-id="5a3bd-371">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="5a3bd-371">Configuration</span></span>
* <span data-ttu-id="5a3bd-372">`IHostedService` implementaci</span><span class="sxs-lookup"><span data-stu-id="5a3bd-372">`IHostedService` implementations</span></span>

<span data-ttu-id="5a3bd-373">Při spuštění hostitele zavolá <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> každou implementaci <xref:Microsoft.Extensions.Hosting.IHostedService> zaregistrovanou v kolekci hostovaných služeb kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-373">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="5a3bd-374">V rámci webové aplikace je jednou z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-374">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="5a3bd-375">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-375">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5a3bd-376">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="5a3bd-376">Set up a host</span></span>

<span data-ttu-id="5a3bd-377">Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve `Program` třídě.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-377">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="5a3bd-378">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-378">The `Main` method:</span></span>

* <span data-ttu-id="5a3bd-379">Volá `CreateHostBuilder` metodu pro vytvoření a konfiguraci objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-379">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="5a3bd-380">Volání `Build` a `Run` metody objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-380">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="5a3bd-381">Webové šablony ASP.NET Core generují následující kód pro vytvoření obecného hostitele:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-381">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="5a3bd-382">Následující kód vytvoří obecného hostitele pomocí úlohy jiného typu než HTTP.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-382">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="5a3bd-383">`IHostedService`Implementace je přidána do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-383">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="5a3bd-384">Pro úlohy HTTP `Main` je metoda stejná, ale `CreateHostBuilder` volání `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-384">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="5a3bd-385">Předchozí kód je vygenerován šablonami ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-385">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="5a3bd-386">Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="5a3bd-387">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že budou nalezeny `CreateHostBuilder` metody, které nakonfigurují hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="5a3bd-388">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="5a3bd-389">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="5a3bd-389">Default builder settings</span></span>

<span data-ttu-id="5a3bd-390"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>Metoda:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="5a3bd-391">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="5a3bd-392">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="5a3bd-393">Proměnné prostředí s předponou `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="5a3bd-394">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-394">Command-line arguments.</span></span>
* <span data-ttu-id="5a3bd-395">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="5a3bd-396">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="5a3bd-397">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="5a3bd-398">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="5a3bd-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="5a3bd-399">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-399">Environment variables.</span></span>
  * <span data-ttu-id="5a3bd-400">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-400">Command-line arguments.</span></span>
* <span data-ttu-id="5a3bd-401">Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="5a3bd-402">Konzola</span><span class="sxs-lookup"><span data-stu-id="5a3bd-402">Console</span></span>
  * <span data-ttu-id="5a3bd-403">Ladit</span><span class="sxs-lookup"><span data-stu-id="5a3bd-403">Debug</span></span>
  * <span data-ttu-id="5a3bd-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="5a3bd-404">EventSource</span></span>
  * <span data-ttu-id="5a3bd-405">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="5a3bd-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="5a3bd-406">Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="5a3bd-407">`ConfigureWebHostDefaults`Metoda:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="5a3bd-408">Načte konfiguraci hostitele z proměnných prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="5a3bd-409">Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="5a3bd-410">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="5a3bd-411">Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="5a3bd-412">Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , pokud je rovno `true` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="5a3bd-413">Povolí integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-413">Enables IIS integration.</span></span> <span data-ttu-id="5a3bd-414">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="5a3bd-415">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5a3bd-416">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="5a3bd-416">Framework-provided services</span></span>

<span data-ttu-id="5a3bd-417">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="5a3bd-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="5a3bd-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="5a3bd-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5a3bd-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="5a3bd-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5a3bd-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="5a3bd-421">Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="5a3bd-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="5a3bd-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="5a3bd-423">Vložení <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> služby (dříve `IApplicationLifetime` ) do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="5a3bd-424">Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="5a3bd-425">Rozhraní obsahuje také `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="5a3bd-426">Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="5a3bd-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="5a3bd-427">IHostLifetime</span></span>

<span data-ttu-id="5a3bd-428"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementace určuje, kdy se hostitel spustí a kdy se zastaví.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="5a3bd-429">Použije se Poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-429">The last implementation registered is used.</span></span>

<span data-ttu-id="5a3bd-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je výchozí `IHostLifetime` implementací.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="5a3bd-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="5a3bd-432">Naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="5a3bd-433">Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="5a3bd-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="5a3bd-434">IHostEnvironment</span></span>

<span data-ttu-id="5a3bd-435">Vložením <xref:Microsoft.Extensions.Hosting.IHostEnvironment> služby do třídy získáte informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="5a3bd-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5a3bd-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="5a3bd-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="5a3bd-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="5a3bd-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="5a3bd-438">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="5a3bd-439">Webové aplikace implementují `IWebHostEnvironment` rozhraní, které dědí `IHostEnvironment` a přidává rozhraní [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5a3bd-440">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="5a3bd-440">Host configuration</span></span>

<span data-ttu-id="5a3bd-441">Konfigurace hostitele se používá pro vlastnosti <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="5a3bd-442">Konfigurace hostitele je k dispozici z [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="5a3bd-443">Po `ConfigureAppConfiguration` nahrazení se v `HostBuilderContext.Configuration` konfiguraci aplikace nahradí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="5a3bd-444">Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> na `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="5a3bd-445">`ConfigureHostConfiguration` dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5a3bd-446">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5a3bd-447">Zprostředkovatel proměnné prostředí s argumenty předpony `DOTNET_` a příkazového řádku jsou obsaženy v `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5a3bd-448">U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="5a3bd-449">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5a3bd-450">Například hodnota proměnné prostředí pro se nastaví jako `ASPNETCORE_ENVIRONMENT` hodnota konfigurace hostitele pro `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5a3bd-451">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="5a3bd-452">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="5a3bd-452">App configuration</span></span>

<span data-ttu-id="5a3bd-453">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> metody `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="5a3bd-454">`ConfigureAppConfiguration` dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="5a3bd-455">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="5a3bd-456">Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="5a3bd-457">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="5a3bd-458">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="5a3bd-459">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="5a3bd-459">Settings for all app types</span></span>

<span data-ttu-id="5a3bd-460">V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="5a3bd-461">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="5a3bd-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5a3bd-462">ApplicationName</span></span>

<span data-ttu-id="5a3bd-463">Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="5a3bd-464">**Klíč**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5a3bd-465">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-465">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-466">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="5a3bd-467">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="5a3bd-468">K nastavení této hodnoty použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-468">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="5a3bd-469">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="5a3bd-469">ContentRoot</span></span>

<span data-ttu-id="5a3bd-470">Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="5a3bd-471">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="5a3bd-472">**Klíč**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5a3bd-473">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-473">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-474">**Výchozí**: složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="5a3bd-475">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="5a3bd-476">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseContentRoot` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="5a3bd-477">Další informace naleznete v tématech:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-477">For more information, see:</span></span>

* [<span data-ttu-id="5a3bd-478">Základy: kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="5a3bd-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="5a3bd-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="5a3bd-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="5a3bd-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="5a3bd-480">EnvironmentName</span></span>

<span data-ttu-id="5a3bd-481">Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="5a3bd-482">Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5a3bd-483">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="5a3bd-484">**Klíč**: `environment`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-484">**Key**: `environment`</span></span>  
<span data-ttu-id="5a3bd-485">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-485">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-486">**Výchozí**: `Production`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-486">**Default**: `Production`</span></span>  
<span data-ttu-id="5a3bd-487">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="5a3bd-488">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseEnvironment` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="5a3bd-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="5a3bd-489">ShutdownTimeout</span></span>

<span data-ttu-id="5a3bd-490">[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5a3bd-491">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-491">The default value is five seconds.</span></span>  <span data-ttu-id="5a3bd-492">Po uplynutí časového limitu hostitel:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="5a3bd-493">Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="5a3bd-494">Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="5a3bd-495">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="5a3bd-496">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="5a3bd-497">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="5a3bd-498">**Klíč**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="5a3bd-499">**Zadejte**: `int`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-499">**Type**: `int`</span></span>  
<span data-ttu-id="5a3bd-500">**Výchozí hodnota**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="5a3bd-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="5a3bd-501">**Proměnná prostředí**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="5a3bd-502">K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="5a3bd-503">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="5a3bd-504">Nastavení pro Web Apps</span><span class="sxs-lookup"><span data-stu-id="5a3bd-504">Settings for web apps</span></span>

<span data-ttu-id="5a3bd-505">Některá nastavení hostitele se vztahují jenom na úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="5a3bd-506">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="5a3bd-507">`IWebHostBuilder`K dispozici jsou metody rozšíření pro tato nastavení.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="5a3bd-508">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` , je instancí `IWebHostBuilder` , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="5a3bd-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="5a3bd-509">CaptureStartupErrors</span></span>

<span data-ttu-id="5a3bd-510">`false`V důsledku dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="5a3bd-511">Když `true` hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="5a3bd-512">**Klíč**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="5a3bd-513">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5a3bd-514">**Výchozí**: výchozí nastavení `false` , pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="5a3bd-515">**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="5a3bd-516">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="5a3bd-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="5a3bd-517">DetailedErrors</span></span>

<span data-ttu-id="5a3bd-518">Pokud je povoleno nebo když je prostředí `Development` , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="5a3bd-519">**Klíč**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="5a3bd-520">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5a3bd-521">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-521">**Default**: `false`</span></span>  
<span data-ttu-id="5a3bd-522">**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="5a3bd-523">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="5a3bd-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="5a3bd-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="5a3bd-525">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="5a3bd-526">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="5a3bd-527">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="5a3bd-528">**Klíč**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="5a3bd-529">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-529">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-530">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="5a3bd-530">**Default**: Empty string</span></span>  
<span data-ttu-id="5a3bd-531">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="5a3bd-532">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="5a3bd-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="5a3bd-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="5a3bd-534">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="5a3bd-535">**Klíč**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="5a3bd-536">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-536">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-537">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="5a3bd-537">**Default**: Empty string</span></span>  
<span data-ttu-id="5a3bd-538">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="5a3bd-539">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="5a3bd-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="5a3bd-540">HTTPS_Port</span></span>

<span data-ttu-id="5a3bd-541">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-541">The HTTPS redirect port.</span></span> <span data-ttu-id="5a3bd-542">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="5a3bd-543">**Klíč**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="5a3bd-544">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-544">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-545">**Výchozí**hodnota: výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="5a3bd-546">**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="5a3bd-547">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="5a3bd-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="5a3bd-548">PreferHostingUrls</span></span>

<span data-ttu-id="5a3bd-549">Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným pomocí a `IWebHostBuilder` místo adres URL nakonfigurovaných `IServer` implementací.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="5a3bd-550">**Klíč**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="5a3bd-551">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5a3bd-552">**Výchozí**: `true`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-552">**Default**: `true`</span></span>  
<span data-ttu-id="5a3bd-553">**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="5a3bd-554">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="5a3bd-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="5a3bd-555">PreventHostingStartup</span></span>

<span data-ttu-id="5a3bd-556">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="5a3bd-557">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="5a3bd-558">**Klíč**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="5a3bd-559">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="5a3bd-560">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-560">**Default**: `false`</span></span>  
<span data-ttu-id="5a3bd-561">**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="5a3bd-562">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="5a3bd-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="5a3bd-563">StartupAssembly</span></span>

<span data-ttu-id="5a3bd-564">Sestavení, ve kterém se má hledat `Startup` Třída</span><span class="sxs-lookup"><span data-stu-id="5a3bd-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="5a3bd-565">**Klíč**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="5a3bd-566">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-566">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-567">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="5a3bd-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="5a3bd-568">**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="5a3bd-569">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="5a3bd-570">`UseStartup` může převzít název sestavení ( `string` ) nebo typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="5a3bd-571">Pokud `UseStartup` je voláno více metod, má poslední z nich přednost.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="5a3bd-572">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="5a3bd-572">URLs</span></span>

<span data-ttu-id="5a3bd-573">Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="5a3bd-574">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="5a3bd-575">Pomocí příkazu " \* " určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="5a3bd-576">Protokol ( `http://` nebo `https://` ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="5a3bd-577">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="5a3bd-578">**Klíč**: `urls`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-578">**Key**: `urls`</span></span>  
<span data-ttu-id="5a3bd-579">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-579">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-580">**Výchozí**: `http://localhost:5000` a `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="5a3bd-581">**Proměnná prostředí**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="5a3bd-582">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="5a3bd-583">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="5a3bd-584">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="5a3bd-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="5a3bd-585">WebRoot</span></span>

<span data-ttu-id="5a3bd-586">Vlastnost [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) Určuje relativní cestu ke statickým assetům aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-586">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="5a3bd-587">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-587">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="5a3bd-588">**Klíč**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-588">**Key**: `webroot`</span></span>  
<span data-ttu-id="5a3bd-589">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-589">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-590">**Výchozí**: výchozí hodnota je `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-590">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="5a3bd-591">Cesta k *obsahu {root}/wwwroot* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-591">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="5a3bd-592">**Proměnná prostředí**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="5a3bd-593">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseWebRoot` na `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-593">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="5a3bd-594">Další informace naleznete v tématech:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-594">For more information, see:</span></span>

* [<span data-ttu-id="5a3bd-595">Základy: web root</span><span class="sxs-lookup"><span data-stu-id="5a3bd-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="5a3bd-596">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="5a3bd-596">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="5a3bd-597">Správa životnosti hostitele</span><span class="sxs-lookup"><span data-stu-id="5a3bd-597">Manage the host lifetime</span></span>

<span data-ttu-id="5a3bd-598">Voláním metod v sestavené <xref:Microsoft.Extensions.Hosting.IHost> implementaci spustíte a zastavíte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="5a3bd-599">Tyto metody ovlivňují všechny  <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5a3bd-600">Spustit</span><span class="sxs-lookup"><span data-stu-id="5a3bd-600">Run</span></span>

<span data-ttu-id="5a3bd-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="5a3bd-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-602">RunAsync</span></span>

<span data-ttu-id="5a3bd-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="5a3bd-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-604">RunConsoleAsync</span></span>

<span data-ttu-id="5a3bd-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="5a3bd-606">Spustit</span><span class="sxs-lookup"><span data-stu-id="5a3bd-606">Start</span></span>

<span data-ttu-id="5a3bd-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="5a3bd-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-608">StartAsync</span></span>

<span data-ttu-id="5a3bd-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí hostitele a vrátí <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="5a3bd-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> je volána na začátku `StartAsync` , což čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5a3bd-611">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="5a3bd-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-612">StopAsync</span></span>

<span data-ttu-id="5a3bd-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusy o zastavení hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="5a3bd-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="5a3bd-614">WaitForShutdown</span></span>

<span data-ttu-id="5a3bd-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například pomocí <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="5a3bd-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="5a3bd-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="5a3bd-618">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="5a3bd-618">External control</span></span>

<span data-ttu-id="5a3bd-619">Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5a3bd-620">ASP.NET Core aplikace nakonfigurují a spouštějí hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="5a3bd-621">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="5a3bd-622">Tento článek se zabývá ASP.NET Core obecný hostitel ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ), který se používá pro aplikace, které nezpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="5a3bd-623">Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele, aby bylo možné rozšířit pole hostitelských scénářů.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="5a3bd-624">Zasílání zpráv, úloh na pozadí a dalších úloh nevyužívajících HTTP na základě výhody obecného hostitele z různých možností, jako je konfigurace, vkládání závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="5a3bd-625">Obecný hostitel je v ASP.NET Core 2,1 novinkou a není vhodný pro scénáře hostování webů.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="5a3bd-626">U scénářů pro hostování webů použijte [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="5a3bd-627">Obecný hostitel nahradí webového hostitele v budoucí verzi a bude fungovat jako primární rozhraní API v rámci scénářů HTTP i non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="5a3bd-628">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5a3bd-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5a3bd-629">Při spuštění ukázkové aplikace v [Visual Studio Code](https://code.visualstudio.com/)použijte *externí nebo integrovaný terminál*.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="5a3bd-630">Nespouštějte ukázku v `internalConsole` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="5a3bd-631">Nastavení konzoly v Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="5a3bd-632">Otevřete soubor *. VSCode/launch.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="5a3bd-633">V konfiguraci **spuštění .NET Core (konzola)** vyhledejte položku **Konzola** .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="5a3bd-634">Nastavte hodnotu buď `externalTerminal` nebo `integratedTerminal` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="5a3bd-635">Úvod</span><span class="sxs-lookup"><span data-stu-id="5a3bd-635">Introduction</span></span>

<span data-ttu-id="5a3bd-636">Knihovna obecných hostitelů je k dispozici v <xref:Microsoft.Extensions.Hosting> oboru názvů a poskytuje balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="5a3bd-637">`Microsoft.Extensions.Hosting`Balíček je součástí [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="5a3bd-638"><xref:Microsoft.Extensions.Hosting.IHostedService> je vstupním bodem pro provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="5a3bd-639">Každá <xref:Microsoft.Extensions.Hosting.IHostedService> implementace se spustí v pořadí [Registrace služby v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="5a3bd-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> je volána při <xref:Microsoft.Extensions.Hosting.IHostedService> spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> je volána v pořadí zpětné registrace při bezproblémovém ukončení hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="5a3bd-641">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="5a3bd-641">Set up a host</span></span>

<span data-ttu-id="5a3bd-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> je hlavní součástí, kterou knihovny a aplikace používají k inicializaci, sestavování a spouštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="5a3bd-643">Možnosti</span><span class="sxs-lookup"><span data-stu-id="5a3bd-643">Options</span></span>

<span data-ttu-id="5a3bd-644"><xref:Microsoft.Extensions.Hosting.HostOptions> nakonfigurujte možnosti pro <xref:Microsoft.Extensions.Hosting.IHost> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="5a3bd-645">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="5a3bd-645">Shutdown timeout</span></span>

<span data-ttu-id="5a3bd-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> Nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="5a3bd-647">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-647">The default value is five seconds.</span></span>

<span data-ttu-id="5a3bd-648">Následující konfigurace možnosti v nástroji `Program.Main` zvyšuje výchozí časový limit pro vypnutí v pěti sekundách na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="5a3bd-649">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="5a3bd-649">Default services</span></span>

<span data-ttu-id="5a3bd-650">Během inicializace hostitele jsou registrovány následující služby:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="5a3bd-651">[Prostředí](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="5a3bd-652">[Konfigurace](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="5a3bd-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="5a3bd-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="5a3bd-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="5a3bd-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="5a3bd-655">[Možnosti](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="5a3bd-656">[Protokolování](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="5a3bd-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="5a3bd-657">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="5a3bd-657">Host configuration</span></span>

<span data-ttu-id="5a3bd-658">Konfiguraci hostitele vytvořil:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-658">Host configuration is created by:</span></span>

* <span data-ttu-id="5a3bd-659">Voláním metod rozšíření pro <xref:Microsoft.Extensions.Hosting.IHostBuilder> nastavte [kořen obsahu](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="5a3bd-660">Čtení konfigurace od zprostředkovatelů konfigurace v nástroji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="5a3bd-661">Metody rozšíření</span><span class="sxs-lookup"><span data-stu-id="5a3bd-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="5a3bd-662">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="5a3bd-662">Application key (name)</span></span>

<span data-ttu-id="5a3bd-663">Vlastnost [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="5a3bd-664">Chcete-li nastavit hodnotu explicitně, použijte [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="5a3bd-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="5a3bd-665">**Klíč**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="5a3bd-666">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-666">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-667">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="5a3bd-668">**Nastavit pomocí**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="5a3bd-669">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5a3bd-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="5a3bd-670">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="5a3bd-670">Content root</span></span>

<span data-ttu-id="5a3bd-671">Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="5a3bd-672">**Klíč**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="5a3bd-673">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-673">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-674">**Výchozí**: výchozí nastavení složky, kde se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="5a3bd-675">**Nastavit pomocí**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="5a3bd-676">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5a3bd-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="5a3bd-677">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="5a3bd-678">Další informace najdete v tématu [základy: kořen obsahu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="5a3bd-679">Prostředí</span><span class="sxs-lookup"><span data-stu-id="5a3bd-679">Environment</span></span>

<span data-ttu-id="5a3bd-680">Nastaví [prostředí](xref:fundamentals/environments)aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5a3bd-681">**Klíč**: `environment`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-681">**Key**: `environment`</span></span>  
<span data-ttu-id="5a3bd-682">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-682">**Type**: `string`</span></span>  
<span data-ttu-id="5a3bd-683">**Výchozí**: `Production`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-683">**Default**: `Production`</span></span>  
<span data-ttu-id="5a3bd-684">**Nastavit pomocí**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="5a3bd-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="5a3bd-685">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="5a3bd-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="5a3bd-686">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-686">The environment can be set to any value.</span></span> <span data-ttu-id="5a3bd-687">Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5a3bd-688">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="5a3bd-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="5a3bd-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="5a3bd-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="5a3bd-691">Konfigurace hostitele slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="5a3bd-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="5a3bd-693">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="5a3bd-694">Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-694">No providers are included by default.</span></span> <span data-ttu-id="5a3bd-695">Musíte explicitně určit, v jakém poskytovateli konfigurace aplikace vyžaduje <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , včetně:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="5a3bd-696">Konfigurace souboru (například z *hostsettings.js* souboru).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="5a3bd-697">Konfigurace proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-697">Environment variable configuration.</span></span>
* <span data-ttu-id="5a3bd-698">Konfigurace argumentů příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="5a3bd-699">Všichni další povinní poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-699">Any other required configuration providers.</span></span>

<span data-ttu-id="5a3bd-700">Konfigurace souborů hostitele je povolená zadáním základní cesty aplikace `SetBasePath` a následným voláním jednoho z [poskytovatelů konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="5a3bd-701">Ukázková aplikace používá soubor JSON, *hostsettings.jszapnutý*, a volá <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> k využívání nastavení konfigurace hostitele souboru.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="5a3bd-702">Chcete-li přidat [konfiguraci proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="5a3bd-703">`AddEnvironmentVariables` přijme volitelnou uživatelem definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="5a3bd-704">Ukázková aplikace používá předponu `PREFIX_` .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="5a3bd-705">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="5a3bd-706">Když je hostitel ukázkové aplikace nakonfigurovaný, hodnota proměnné prostředí `PREFIX_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro tento `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="5a3bd-707">Během vývoje při používání sady [Visual Studio](https://visualstudio.microsoft.com) nebo při spuštění aplikace s se `dotnet run` mohou proměnné prostředí nastavit v souboru *Properties/launchSettings.js* .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="5a3bd-708">V [Visual Studio Code](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *. VSCode/launch.js* při vývoji.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="5a3bd-709">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5a3bd-710">[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="5a3bd-711">K povolení argumentů příkazového řádku pro přepsání konfigurace poskytované předchozími poskytovateli konfigurace se přidá poslední konfigurace příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="5a3bd-712">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-712">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="5a3bd-713">Další konfiguraci lze poskytnout pomocí klíčů [ApplicationName](#application-key-name) a [contentRoot](#content-root) .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="5a3bd-714">Příklad `HostBuilder` Konfigurace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="5a3bd-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="5a3bd-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="5a3bd-716">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="5a3bd-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="5a3bd-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="5a3bd-719">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="5a3bd-720">Konfigurace vytvořená nástrojem <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="5a3bd-721">Konfigurace aplikace automaticky přijímá konfiguraci hostitele, kterou poskytuje [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="5a3bd-722">Příklad konfigurace aplikace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="5a3bd-723">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-723">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="5a3bd-724">*appsettings.Development.js*:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-724">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="5a3bd-725">*appsettings.Production.js*:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-725">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="5a3bd-726">Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="5a3bd-727">Ukázková aplikace přesune své soubory nastavení aplikace JSON a *hostsettings.js* s touto `<Content>` položkou:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="5a3bd-728">Metody rozšíření konfigurace, například <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, například [Microsoft.Extensions.Configuration.Jsv](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft.Extensions.Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="5a3bd-729">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), tyto balíčky musí být kromě balíčku Core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) přidány do projektu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="5a3bd-730">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="5a3bd-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="5a3bd-731">ConfigureServices</span></span>

<span data-ttu-id="5a3bd-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> Přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5a3bd-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="5a3bd-734">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="5a3bd-735">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="5a3bd-736">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá `AddHostedService` metodu rozšíření k přidání služby pro události života, `LifetimeEventsHostedService` a časově omezená úloha na pozadí, `TimedHostedService` do aplikace:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="5a3bd-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="5a3bd-737">ConfigureLogging</span></span>

<span data-ttu-id="5a3bd-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> Přidá delegáta pro konfiguraci poskytnutého <xref:Microsoft.Extensions.Logging.ILoggingBuilder> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="5a3bd-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> může být voláno vícekrát s přísadou výsledků.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="5a3bd-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="5a3bd-740">UseConsoleLifetime</span></span>

<span data-ttu-id="5a3bd-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="5a3bd-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="5a3bd-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je předem registrován jako výchozí implementace životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="5a3bd-744">Je použita poslední registrovaná doba života.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="5a3bd-745">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="5a3bd-745">Container configuration</span></span>

<span data-ttu-id="5a3bd-746">Pro podporu zapojení do jiných kontejnerů může hostitel přijmout <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="5a3bd-747">Poskytnutí továrny není součástí registrace DI Container, ale je místo toho hostitel, který se používá k vytvoření konkrétního kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="5a3bd-748">[UseServiceProviderFactory (IServiceProviderFactory &lt; TContainerBuilder &gt; )](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí továrnu, která byla použita k vytvoření poskytovatele služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="5a3bd-749">Vlastní konfigurace kontejneru je spravovaná <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metodou.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="5a3bd-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> poskytuje silné typové prostředí pro konfiguraci kontejneru nad podkladovým rozhraním API hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="5a3bd-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="5a3bd-752">Vytvoření kontejneru služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="5a3bd-753">Poskytněte objekt pro vytváření kontejnerů služby:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="5a3bd-754">Použijte objekt pro vytváření a nakonfigurujte vlastní kontejner služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="5a3bd-755">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="5a3bd-755">Extensibility</span></span>

<span data-ttu-id="5a3bd-756">Rozšiřitelnost hostitelů se provádí s metodami rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="5a3bd-757">Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementaci s příkladem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) znázorněným v <xref:fundamentals/host/hosted-services> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="5a3bd-758">Aplikace vytváří `UseHostedService` metodu rozšíření pro registraci hostované služby, která byla předána `T` :</span><span class="sxs-lookup"><span data-stu-id="5a3bd-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="5a3bd-759">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="5a3bd-759">Manage the host</span></span>

<span data-ttu-id="5a3bd-760"><xref:Microsoft.Extensions.Hosting.IHost>Implementace zodpovídá za spouštění a zastavování <xref:Microsoft.Extensions.Hosting.IHostedService> implementací, které jsou zaregistrované v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="5a3bd-761">Spustit</span><span class="sxs-lookup"><span data-stu-id="5a3bd-761">Run</span></span>

<span data-ttu-id="5a3bd-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud není hostitel vypnutý:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="5a3bd-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-763">RunAsync</span></span>

<span data-ttu-id="5a3bd-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="5a3bd-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-765">RunConsoleAsync</span></span>

<span data-ttu-id="5a3bd-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="5a3bd-767">Spustit a StopAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-767">Start and StopAsync</span></span>

<span data-ttu-id="5a3bd-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="5a3bd-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusy o zastavení hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="5a3bd-770">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="5a3bd-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="5a3bd-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> ukončí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="5a3bd-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="5a3bd-773">WaitForShutdown</span></span>

<span data-ttu-id="5a3bd-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>je aktivována prostřednictvím <xref:Microsoft.Extensions.Hosting.IHostLifetime> , například `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (naslouchá pro <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="5a3bd-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="5a3bd-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="5a3bd-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="5a3bd-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="5a3bd-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="5a3bd-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="5a3bd-778">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="5a3bd-778">External control</span></span>

<span data-ttu-id="5a3bd-779">Externí kontrolu hostitele lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-779">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="5a3bd-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> je volána na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> , což čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="5a3bd-781">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="5a3bd-782">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="5a3bd-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="5a3bd-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> poskytuje informace o hostitelském prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="5a3bd-784">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání, aby <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="5a3bd-785">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="5a3bd-786">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="5a3bd-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="5a3bd-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> umožňuje aktivity po spuštění a vypnutí, včetně řádných požadavků na vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="5a3bd-788">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="5a3bd-789">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="5a3bd-789">Cancellation Token</span></span> | <span data-ttu-id="5a3bd-790">Aktivované při&#8230;</span><span class="sxs-lookup"><span data-stu-id="5a3bd-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="5a3bd-791">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="5a3bd-792">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="5a3bd-793">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-793">All requests should be processed.</span></span> <span data-ttu-id="5a3bd-794">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="5a3bd-795">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="5a3bd-796">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-796">Requests may still be processing.</span></span> <span data-ttu-id="5a3bd-797">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="5a3bd-798">Konstruktor – vloží <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> službu do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="5a3bd-799">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání konstruktoru do `LifetimeEventsHostedService` třídy ( <xref:Microsoft.Extensions.Hosting.IHostedService> implementace) pro registraci událostí.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="5a3bd-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="5a3bd-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> požaduje ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a3bd-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="5a3bd-802">Následující třída používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> k bezproblémovému vypnutí aplikace při `Shutdown` volání metody třídy:</span><span class="sxs-lookup"><span data-stu-id="5a3bd-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5a3bd-803">Další materiály</span><span class="sxs-lookup"><span data-stu-id="5a3bd-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
