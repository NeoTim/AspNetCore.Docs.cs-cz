---
title: Obecný hostitel .NET
author: tdykstra
description: Seznamte se s obecným hostitelem .NET Core, který je zodpovědný za spouštění a správu životního cyklu aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 9f5ecc7840fc7ffd9432a3bb67d0418efb7e8fd6
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975620"
---
# <a name="net-generic-host"></a><span data-ttu-id="ef180-103">Obecný hostitel .NET</span><span class="sxs-lookup"><span data-stu-id="ef180-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ef180-104">Tento článek představuje obecného hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny k jeho použití.</span><span class="sxs-lookup"><span data-stu-id="ef180-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="ef180-105">Co je hostitel?</span><span class="sxs-lookup"><span data-stu-id="ef180-105">What's a host?</span></span>

<span data-ttu-id="ef180-106">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="ef180-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="ef180-107">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="ef180-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="ef180-108">Protokolování</span><span class="sxs-lookup"><span data-stu-id="ef180-108">Logging</span></span>
* <span data-ttu-id="ef180-109">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="ef180-109">Configuration</span></span>
* <span data-ttu-id="ef180-110">`IHostedService`implementaci</span><span class="sxs-lookup"><span data-stu-id="ef180-110">`IHostedService` implementations</span></span>

<span data-ttu-id="ef180-111">Při spuštění hostitele volá `IHostedService.StartAsync` každou <xref:Microsoft.Extensions.Hosting.IHostedService> implementaci, kterou najde v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="ef180-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="ef180-112">V rámci webové aplikace je jednou z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="ef180-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="ef180-113">Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="ef180-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="ef180-114">Ve verzích ASP.NET Core starších než 3,0 se [webový hostitel](xref:fundamentals/host/web-host) používá pro úlohy http.</span><span class="sxs-lookup"><span data-stu-id="ef180-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="ef180-115">Webový hostitel již není doporučen pro webové aplikace a zůstává dostupný pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="ef180-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="ef180-116">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="ef180-116">Set up a host</span></span>

<span data-ttu-id="ef180-117">Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve `Program` třídě.</span><span class="sxs-lookup"><span data-stu-id="ef180-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="ef180-118">`Main` Metody:</span><span class="sxs-lookup"><span data-stu-id="ef180-118">The `Main` method:</span></span>

* <span data-ttu-id="ef180-119">`CreateHostBuilder` Volá metodu pro vytvoření a konfiguraci objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="ef180-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="ef180-120">Volání `Build` a`Run` metody objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="ef180-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="ef180-121">Zde je *program.cs* kód pro úlohu jiného typu než HTTP a jedna `IHostedService` implementace se přidala do kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="ef180-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="ef180-122">Pro úlohy http je `Main` metoda stejná, ale `CreateHostBuilder` volání `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="ef180-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="ef180-123">Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="ef180-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="ef180-124">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že budou nalezeny `CreateHostBuilder` metody, které nakonfigurují hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="ef180-125">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="ef180-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="ef180-126">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="ef180-126">Default builder settings</span></span> 

<span data-ttu-id="ef180-127"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Metody:</span><span class="sxs-lookup"><span data-stu-id="ef180-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="ef180-128">Nastaví kořen obsahu na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="ef180-128">Sets the content root to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="ef180-129">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="ef180-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="ef180-130">Proměnné prostředí s předponou "DOTNET_".</span><span class="sxs-lookup"><span data-stu-id="ef180-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="ef180-131">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="ef180-131">Command-line arguments.</span></span>
* <span data-ttu-id="ef180-132">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="ef180-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="ef180-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ef180-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="ef180-134">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ef180-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="ef180-135">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="ef180-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="ef180-136">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ef180-136">Environment variables.</span></span>
  * <span data-ttu-id="ef180-137">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="ef180-137">Command-line arguments.</span></span>
* <span data-ttu-id="ef180-138">Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="ef180-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="ef180-139">Konzola</span><span class="sxs-lookup"><span data-stu-id="ef180-139">Console</span></span>
  * <span data-ttu-id="ef180-140">Ladění</span><span class="sxs-lookup"><span data-stu-id="ef180-140">Debug</span></span>
  * <span data-ttu-id="ef180-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="ef180-141">EventSource</span></span>
  * <span data-ttu-id="ef180-142">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="ef180-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="ef180-143">Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.</span><span class="sxs-lookup"><span data-stu-id="ef180-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="ef180-144">`ConfigureWebHostDefaults` Metody:</span><span class="sxs-lookup"><span data-stu-id="ef180-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="ef180-145">Načte konfiguraci hostitele z proměnných prostředí s předponou "ASPNETCORE_".</span><span class="sxs-lookup"><span data-stu-id="ef180-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="ef180-146">Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="ef180-147">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="ef180-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="ef180-148">Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ef180-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="ef180-149">Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , pokud ASPNETCORE_FORWARDEDHEADERS_ENABLED = true.</span><span class="sxs-lookup"><span data-stu-id="ef180-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="ef180-150">Povolí integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="ef180-150">Enables IIS integration.</span></span> <span data-ttu-id="ef180-151">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="ef180-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="ef180-152">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="ef180-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ef180-153">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="ef180-153">Framework-provided services</span></span>

<span data-ttu-id="ef180-154">Mezi služby, které jsou zaregistrovány automaticky, patří následující:</span><span class="sxs-lookup"><span data-stu-id="ef180-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="ef180-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ef180-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="ef180-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ef180-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="ef180-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ef180-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="ef180-158">Seznam všech služeb poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="ef180-158">For a list of all framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="ef180-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ef180-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="ef180-160">Vložení služby `IApplicationLifetime`(dříve) do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí. <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime></span><span class="sxs-lookup"><span data-stu-id="ef180-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="ef180-161">Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="ef180-162">Rozhraní obsahuje `StopApplication` také metodu.</span><span class="sxs-lookup"><span data-stu-id="ef180-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="ef180-163">Následující příklad je `IHostedService` implementace, která `IApplicationLifetime` registruje události:</span><span class="sxs-lookup"><span data-stu-id="ef180-163">The following example is an `IHostedService` implementation that registers the `IApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="ef180-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ef180-164">IHostLifetime</span></span>

<span data-ttu-id="ef180-165"><xref:Microsoft.Extensions.Hosting.IHostLifetime> Implementace určuje, kdy se hostitel spustí a kdy se zastaví.</span><span class="sxs-lookup"><span data-stu-id="ef180-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="ef180-166">Použije se Poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="ef180-166">The last implementation registered is used.</span></span>

<span data-ttu-id="ef180-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>je výchozí `IHostLifetime` implementací.</span><span class="sxs-lookup"><span data-stu-id="ef180-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="ef180-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="ef180-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="ef180-169">naslouchá klávesám CTRL + C/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pro spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="ef180-169">listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="ef180-170">Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="ef180-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="ef180-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ef180-171">IHostEnvironment</span></span>

<span data-ttu-id="ef180-172">Vložením <xref:Microsoft.Extensions.Hosting.IHostEnvironment> služby do třídy získáte informace o následujícím:</span><span class="sxs-lookup"><span data-stu-id="ef180-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="ef180-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ef180-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="ef180-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ef180-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="ef180-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="ef180-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="ef180-176">Webové aplikace implementují `IWebHostEnvironment` rozhraní, které dědí `IHostEnvironment` a přidává:</span><span class="sxs-lookup"><span data-stu-id="ef180-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds:</span></span>

* [<span data-ttu-id="ef180-177">WebRootPath</span><span class="sxs-lookup"><span data-stu-id="ef180-177">WebRootPath</span></span>](#webroot)

## <a name="host-configuration"></a><span data-ttu-id="ef180-178">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="ef180-178">Host configuration</span></span>

<span data-ttu-id="ef180-179">Konfigurace hostitele se používá pro vlastnosti <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementace.</span><span class="sxs-lookup"><span data-stu-id="ef180-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="ef180-180">Konfigurace hostitele je k dispozici z [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="ef180-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="ef180-181">Po `ConfigureAppConfiguration`nahrazenísev konfiguraciaplikacenahradí.`HostBuilderContext.Configuration`</span><span class="sxs-lookup"><span data-stu-id="ef180-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="ef180-182">Chcete-li přidat konfiguraci hostitele <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , `IHostBuilder`zavolejte na.</span><span class="sxs-lookup"><span data-stu-id="ef180-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ef180-183">`ConfigureHostConfiguration`dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="ef180-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ef180-184">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="ef180-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="ef180-185">Zprostředkovatel proměnné prostředí s argumenty předpony `DOTNET_` a příkazového řádku jsou součástí CreateDefaultBuilder.</span><span class="sxs-lookup"><span data-stu-id="ef180-185">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="ef180-186">U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="ef180-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="ef180-187">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="ef180-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="ef180-188">Například hodnota proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se nastaví jako hodnota konfigurace hostitele `environment` pro klíč.</span><span class="sxs-lookup"><span data-stu-id="ef180-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="ef180-189">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="ef180-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="ef180-190">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="ef180-190">App configuration</span></span>

<span data-ttu-id="ef180-191">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> metody `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ef180-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ef180-192">`ConfigureAppConfiguration`dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="ef180-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ef180-193">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="ef180-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="ef180-194">Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di.</span><span class="sxs-lookup"><span data-stu-id="ef180-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="ef180-195">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="ef180-196">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ef180-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="ef180-197">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="ef180-197">Settings for all app types</span></span>

<span data-ttu-id="ef180-198">V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef180-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="ef180-199">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` předponu nebo. `ASPNETCORE_`</span><span class="sxs-lookup"><span data-stu-id="ef180-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

### <a name="applicationname"></a><span data-ttu-id="ef180-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ef180-200">ApplicationName</span></span>

<span data-ttu-id="ef180-201">Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="ef180-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="ef180-202">**Klíč**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ef180-202">**Key**: applicationName</span></span>  
<span data-ttu-id="ef180-203">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="ef180-203">**Type**: *string*</span></span>  
<span data-ttu-id="ef180-204">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="ef180-205">**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="ef180-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="ef180-206">K nastavení této hodnoty použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="ef180-206">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="ef180-207">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="ef180-207">ContentRootPath</span></span>

<span data-ttu-id="ef180-208">Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="ef180-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="ef180-209">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="ef180-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="ef180-210">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="ef180-210">**Key**: contentRoot</span></span>  
<span data-ttu-id="ef180-211">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="ef180-211">**Type**: *string*</span></span>  
<span data-ttu-id="ef180-212">**Výchozí**: Složka, ve které se nachází sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="ef180-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="ef180-213">**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="ef180-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="ef180-214">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseContentRoot` zavolejte `IHostBuilder`na:</span><span class="sxs-lookup"><span data-stu-id="ef180-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

### <a name="environmentname"></a><span data-ttu-id="ef180-215">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ef180-215">EnvironmentName</span></span>

<span data-ttu-id="ef180-216">Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ef180-216">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="ef180-217">Hodnoty definované rozhraním zahrnují `Development`, `Staging`a `Production`.</span><span class="sxs-lookup"><span data-stu-id="ef180-217">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ef180-218">U hodnot se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="ef180-218">Values aren't case sensitive.</span></span>

<span data-ttu-id="ef180-219">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="ef180-219">**Key**: environment</span></span>  
<span data-ttu-id="ef180-220">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="ef180-220">**Type**: *string*</span></span>  
<span data-ttu-id="ef180-221">**Výchozí**: Produkční</span><span class="sxs-lookup"><span data-stu-id="ef180-221">**Default**: Production</span></span>  
<span data-ttu-id="ef180-222">**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="ef180-222">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="ef180-223">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseEnvironment` zavolejte `IHostBuilder`na:</span><span class="sxs-lookup"><span data-stu-id="ef180-223">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="ef180-224">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="ef180-224">ShutdownTimeout</span></span>

<span data-ttu-id="ef180-225">[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ef180-225">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="ef180-226">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="ef180-226">The default value is five seconds.</span></span>  <span data-ttu-id="ef180-227">Po uplynutí časového limitu hostitel:</span><span class="sxs-lookup"><span data-stu-id="ef180-227">During the timeout period, the host:</span></span>

* <span data-ttu-id="ef180-228">Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="ef180-228">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="ef180-229">Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="ef180-229">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="ef180-230">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="ef180-230">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="ef180-231">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="ef180-231">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="ef180-232">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="ef180-232">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="ef180-233">**Klíč**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="ef180-233">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="ef180-234">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="ef180-234">**Type**: *int*</span></span>  
<span data-ttu-id="ef180-235">**Výchozí**: **Proměnná prostředí**o 5 sekund:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="ef180-235">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="ef180-236">K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="ef180-236">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="ef180-237">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="ef180-237">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="ef180-238">Nastavení pro Web Apps</span><span class="sxs-lookup"><span data-stu-id="ef180-238">Settings for web apps</span></span>

<span data-ttu-id="ef180-239">Některá nastavení hostitele se vztahují jenom na úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef180-239">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="ef180-240">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` předponu nebo. `ASPNETCORE_`</span><span class="sxs-lookup"><span data-stu-id="ef180-240">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="ef180-241">K dispozici `IWebHostBuilder` jsou metody rozšíření pro tato nastavení.</span><span class="sxs-lookup"><span data-stu-id="ef180-241">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="ef180-242">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` `IWebHostBuilder`, je instancí, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ef180-242">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="ef180-243">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="ef180-243">CaptureStartupErrors</span></span>

<span data-ttu-id="ef180-244">V `false`důsledku dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="ef180-244">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="ef180-245">Když `true`hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.</span><span class="sxs-lookup"><span data-stu-id="ef180-245">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="ef180-246">**Klíč**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="ef180-246">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="ef180-247">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="ef180-247">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ef180-248">**Výchozí**: Použije se výchozí hodnota, `true` PokudseaplikacenespustísKestrelzaIIS,kdejevýchozíhodnota.`false`</span><span class="sxs-lookup"><span data-stu-id="ef180-248">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="ef180-249">**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="ef180-249">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="ef180-250">Chcete-li nastavit tuto hodnotu, použijte konfiguraci `CaptureStartupErrors`nebo volání:</span><span class="sxs-lookup"><span data-stu-id="ef180-250">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="ef180-251">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="ef180-251">DetailedErrors</span></span>

<span data-ttu-id="ef180-252">Pokud je povoleno nebo když je `Development`prostředí, aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="ef180-252">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="ef180-253">**Klíč**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="ef180-253">**Key**: detailedErrors</span></span>  
<span data-ttu-id="ef180-254">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="ef180-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ef180-255">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="ef180-255">**Default**: false</span></span>  
<span data-ttu-id="ef180-256">**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="ef180-256">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="ef180-257">Chcete-li nastavit tuto hodnotu, použijte konfiguraci `UseSetting`nebo volání:</span><span class="sxs-lookup"><span data-stu-id="ef180-257">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="ef180-258">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="ef180-258">HostingStartupAssemblies</span></span>

<span data-ttu-id="ef180-259">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ef180-259">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="ef180-260">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-260">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="ef180-261">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="ef180-261">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="ef180-262">**Klíč**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="ef180-262">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="ef180-263">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="ef180-263">**Type**: *string*</span></span>  
<span data-ttu-id="ef180-264">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="ef180-264">**Default**: Empty string</span></span>  
<span data-ttu-id="ef180-265">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ef180-265">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="ef180-266">Chcete-li nastavit tuto hodnotu, použijte konfiguraci `UseSetting`nebo volání:</span><span class="sxs-lookup"><span data-stu-id="ef180-266">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="ef180-267">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="ef180-267">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="ef180-268">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="ef180-268">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="ef180-269">**Klíč**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="ef180-269">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="ef180-270">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="ef180-270">**Type**: *string*</span></span>  
<span data-ttu-id="ef180-271">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="ef180-271">**Default**: Empty string</span></span>  
<span data-ttu-id="ef180-272">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ef180-272">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="ef180-273">Chcete-li nastavit tuto hodnotu, použijte konfiguraci `UseSetting`nebo volání:</span><span class="sxs-lookup"><span data-stu-id="ef180-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="ef180-274">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="ef180-274">HTTPS_Port</span></span>

<span data-ttu-id="ef180-275">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ef180-275">The HTTPS redirect port.</span></span> <span data-ttu-id="ef180-276">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ef180-276">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ef180-277">**Klíč**: https_port **typ**:**Výchozí hodnota** *řetězce*
: Výchozí hodnota není nastavena.</span><span class="sxs-lookup"><span data-stu-id="ef180-277">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="ef180-278">**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="ef180-278">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="ef180-279">Chcete-li nastavit tuto hodnotu, použijte konfiguraci `UseSetting`nebo volání:</span><span class="sxs-lookup"><span data-stu-id="ef180-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="ef180-280">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="ef180-280">PreferHostingUrls</span></span>

<span data-ttu-id="ef180-281">Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným `IWebHostBuilder` pomocí a místo nakonfigurovaných `IServer` s implementací.</span><span class="sxs-lookup"><span data-stu-id="ef180-281">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="ef180-282">**Klíč**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="ef180-282">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="ef180-283">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="ef180-283">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ef180-284">**Výchozí**: true</span><span class="sxs-lookup"><span data-stu-id="ef180-284">**Default**: true</span></span>  
<span data-ttu-id="ef180-285">**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="ef180-285">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="ef180-286">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="ef180-286">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="ef180-287">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="ef180-287">PreventHostingStartup</span></span>

<span data-ttu-id="ef180-288">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-288">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="ef180-289">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ef180-289">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="ef180-290">**Klíč**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="ef180-290">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="ef180-291">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="ef180-291">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ef180-292">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="ef180-292">**Default**: false</span></span>  
<span data-ttu-id="ef180-293">**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="ef180-293">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="ef180-294">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="ef180-294">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="ef180-295">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="ef180-295">StartupAssembly</span></span>

<span data-ttu-id="ef180-296">Sestavení, ve kterém se má `Startup` Hledat třída</span><span class="sxs-lookup"><span data-stu-id="ef180-296">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="ef180-297">**Klíč**: startupAssembly **typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="ef180-297">**Key**: startupAssembly **Type**: *string*</span></span>  
<span data-ttu-id="ef180-298">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="ef180-298">**Default**: The app's assembly</span></span>  
<span data-ttu-id="ef180-299">**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="ef180-299">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="ef180-300">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseStartup`volání.</span><span class="sxs-lookup"><span data-stu-id="ef180-300">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="ef180-301">`UseStartup`může převzít název sestavení (`string`) nebo typ (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="ef180-301">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="ef180-302">Pokud je `UseStartup` voláno více metod, má poslední z nich přednost.</span><span class="sxs-lookup"><span data-stu-id="ef180-302">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="ef180-303">URL – adresy</span><span class="sxs-lookup"><span data-stu-id="ef180-303">URLs</span></span>

<span data-ttu-id="ef180-304">Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="ef180-304">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="ef180-305">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="ef180-305">For example, `http://localhost:123`.</span></span> <span data-ttu-id="ef180-306">Pomocí příkazu\*"" určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu ( `http://*:5000`například).</span><span class="sxs-lookup"><span data-stu-id="ef180-306">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="ef180-307">Protokol (`http://` nebo `https://`) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="ef180-307">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="ef180-308">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="ef180-308">Supported formats vary among servers.</span></span>

<span data-ttu-id="ef180-309">**Klíč**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="ef180-309">**Key**: urls</span></span>  
<span data-ttu-id="ef180-310">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="ef180-310">**Type**: *string*</span></span>  
<span data-ttu-id="ef180-311">**Výchozí**: `http://localhost:5000` a `https://localhost:5001`proměnná prostředí: 
`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="ef180-311">**Default**: `http://localhost:5000` and `https://localhost:5001`
**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="ef180-312">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="ef180-312">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="ef180-313">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="ef180-313">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="ef180-314">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ef180-314">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="ef180-315">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ef180-315">WebRoot</span></span>

<span data-ttu-id="ef180-316">Relativní cesta ke statickým prostředkům aplikace</span><span class="sxs-lookup"><span data-stu-id="ef180-316">The relative path to the app's static assets.</span></span>

<span data-ttu-id="ef180-317">**Klíč**: Webroot</span><span class="sxs-lookup"><span data-stu-id="ef180-317">**Key**: webroot</span></span>  
<span data-ttu-id="ef180-318">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="ef180-318">**Type**: *string*</span></span>  
<span data-ttu-id="ef180-319">**Výchozí**: *(Kořen obsahu)/wwwroot*, pokud cesta existuje.</span><span class="sxs-lookup"><span data-stu-id="ef180-319">**Default**: *(Content Root)/wwwroot*, if the path exists.</span></span> <span data-ttu-id="ef180-320">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="ef180-320">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="ef180-321">**Proměnná prostředí**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="ef180-321">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="ef180-322">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseWebRoot`:</span><span class="sxs-lookup"><span data-stu-id="ef180-322">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="ef180-323">Správa životnosti hostitele</span><span class="sxs-lookup"><span data-stu-id="ef180-323">Manage the host lifetime</span></span>

<span data-ttu-id="ef180-324">Voláním metod v sestavené <xref:Microsoft.Extensions.Hosting.IHost> implementaci spustíte a zastavíte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ef180-324">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="ef180-325">Tyto metody ovlivňují všechny <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="ef180-325">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="ef180-326">Spustit</span><span class="sxs-lookup"><span data-stu-id="ef180-326">Run</span></span>

<span data-ttu-id="ef180-327"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.</span><span class="sxs-lookup"><span data-stu-id="ef180-327"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="ef180-328">RunAsync</span><span class="sxs-lookup"><span data-stu-id="ef180-328">RunAsync</span></span>

<span data-ttu-id="ef180-329"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="ef180-329"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="ef180-330">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="ef180-330">RunConsoleAsync</span></span>

<span data-ttu-id="ef180-331"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká na vypnutí CTRL + C/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ef180-331"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="ef180-332">Spustit</span><span class="sxs-lookup"><span data-stu-id="ef180-332">Start</span></span>

<span data-ttu-id="ef180-333"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="ef180-333"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="ef180-334">StartAsync</span><span class="sxs-lookup"><span data-stu-id="ef180-334">StartAsync</span></span>

<span data-ttu-id="ef180-335"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a vrátí <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="ef180-335"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="ef180-336"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na začátku `StartAsync`, což čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="ef180-336"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="ef180-337">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="ef180-337">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="ef180-338">StopAsync</span><span class="sxs-lookup"><span data-stu-id="ef180-338">StopAsync</span></span>

<span data-ttu-id="ef180-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusy o zastavení hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="ef180-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="ef180-340">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="ef180-340">WaitForShutdown</span></span>

<span data-ttu-id="ef180-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například prostřednictvím CTRL + C/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ef180-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="ef180-342">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="ef180-342">WaitForShutdownAsync</span></span>

<span data-ttu-id="ef180-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>daného tokenu a volání.</span><span class="sxs-lookup"><span data-stu-id="ef180-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="ef180-344">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="ef180-344">External control</span></span>

<span data-ttu-id="ef180-345">Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="ef180-345">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="ef180-346">ASP.NET Core aplikace nakonfigurují a spouštějí hostitele.</span><span class="sxs-lookup"><span data-stu-id="ef180-346">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="ef180-347">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="ef180-347">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="ef180-348">Tento článek se zabývá ASP.NET Core obecný hostitel (<xref:Microsoft.Extensions.Hosting.HostBuilder>), který se používá pro aplikace, které nezpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef180-348">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="ef180-349">Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele, aby bylo možné rozšířit pole hostitelských scénářů.</span><span class="sxs-lookup"><span data-stu-id="ef180-349">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="ef180-350">Zasílání zpráv, úloh na pozadí a dalších úloh nevyužívajících HTTP na základě výhody obecného hostitele z různých možností, jako je konfigurace, vkládání závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="ef180-350">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="ef180-351">Obecný hostitel je v ASP.NET Core 2,1 novinkou a není vhodný pro scénáře hostování webů.</span><span class="sxs-lookup"><span data-stu-id="ef180-351">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="ef180-352">U scénářů pro hostování webů použijte [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="ef180-352">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="ef180-353">Obecný hostitel nahradí webového hostitele v budoucí verzi a bude fungovat jako primární rozhraní API v rámci scénářů HTTP i non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="ef180-353">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="ef180-354">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ef180-354">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ef180-355">Při spuštění ukázkové aplikace v [Visual Studio Code](https://code.visualstudio.com/)použijte *externí nebo integrovaný terminál*.</span><span class="sxs-lookup"><span data-stu-id="ef180-355">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="ef180-356">Nespouštějte ukázku v `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="ef180-356">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="ef180-357">Nastavení konzoly v Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="ef180-357">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="ef180-358">Otevřete soubor *. VSCode/Launch. JSON* .</span><span class="sxs-lookup"><span data-stu-id="ef180-358">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="ef180-359">V konfiguraci **spuštění .NET Core (konzola)** vyhledejte položku **Konzola** .</span><span class="sxs-lookup"><span data-stu-id="ef180-359">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="ef180-360">Nastavte hodnotu buď `externalTerminal` nebo `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="ef180-360">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="ef180-361">Úvod</span><span class="sxs-lookup"><span data-stu-id="ef180-361">Introduction</span></span>

<span data-ttu-id="ef180-362">Knihovna obecných hostitelů je k dispozici v <xref:Microsoft.Extensions.Hosting> oboru názvů a poskytuje balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) .</span><span class="sxs-lookup"><span data-stu-id="ef180-362">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="ef180-363">Balíček je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější). `Microsoft.Extensions.Hosting`</span><span class="sxs-lookup"><span data-stu-id="ef180-363">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="ef180-364"><xref:Microsoft.Extensions.Hosting.IHostedService>je vstupním bodem pro provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="ef180-364"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="ef180-365">Každá <xref:Microsoft.Extensions.Hosting.IHostedService> implementace se spustí v pořadí [Registrace služby v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="ef180-365">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="ef180-366"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>je volána <xref:Microsoft.Extensions.Hosting.IHostedService> při spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> je volána v pořadí zpětné registrace při bezproblémovém ukončení hostitele.</span><span class="sxs-lookup"><span data-stu-id="ef180-366"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="ef180-367">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="ef180-367">Set up a host</span></span>

<span data-ttu-id="ef180-368"><xref:Microsoft.Extensions.Hosting.IHostBuilder>je hlavní součástí, kterou knihovny a aplikace používají k inicializaci, sestavování a spouštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="ef180-368"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="ef180-369">Možnosti</span><span class="sxs-lookup"><span data-stu-id="ef180-369">Options</span></span>

<span data-ttu-id="ef180-370"><xref:Microsoft.Extensions.Hosting.HostOptions>nakonfigurujte možnosti pro <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="ef180-370"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="ef180-371">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="ef180-371">Shutdown timeout</span></span>

<span data-ttu-id="ef180-372"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ef180-372"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="ef180-373">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="ef180-373">The default value is five seconds.</span></span>

<span data-ttu-id="ef180-374">Následující konfigurace možnosti v `Program.Main` nástroji zvyšuje výchozí časový limit pěti sekund vypnutí na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="ef180-374">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="ef180-375">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="ef180-375">Default services</span></span>

<span data-ttu-id="ef180-376">Během inicializace hostitele jsou registrovány následující služby:</span><span class="sxs-lookup"><span data-stu-id="ef180-376">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="ef180-377">[Prostředí](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="ef180-377">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="ef180-378">[Konfigurace](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="ef180-378">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="ef180-379"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="ef180-379"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="ef180-380"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="ef180-380"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="ef180-381">[Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="ef180-381">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="ef180-382">[Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="ef180-382">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="ef180-383">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="ef180-383">Host configuration</span></span>

<span data-ttu-id="ef180-384">Konfiguraci hostitele vytvořil:</span><span class="sxs-lookup"><span data-stu-id="ef180-384">Host configuration is created by:</span></span>

* <span data-ttu-id="ef180-385">Voláním metod <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozšíření pro nastavte [kořen obsahu](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="ef180-385">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="ef180-386">Čtení konfigurace od zprostředkovatelů konfigurace <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>v nástroji.</span><span class="sxs-lookup"><span data-stu-id="ef180-386">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="ef180-387">Metody rozšíření</span><span class="sxs-lookup"><span data-stu-id="ef180-387">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="ef180-388">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="ef180-388">Application key (name)</span></span>

<span data-ttu-id="ef180-389">Vlastnost [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="ef180-389">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="ef180-390">Chcete-li nastavit hodnotu explicitně, použijte [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="ef180-390">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="ef180-391">**Klíč**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ef180-391">**Key**: applicationName</span></span>  
<span data-ttu-id="ef180-392">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="ef180-392">**Type**: *string*</span></span>  
<span data-ttu-id="ef180-393">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace</span><span class="sxs-lookup"><span data-stu-id="ef180-393">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="ef180-394">**Nastavit pomocí**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="ef180-394">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="ef180-395">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ef180-395">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="ef180-396">Kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="ef180-396">Content root</span></span>

<span data-ttu-id="ef180-397">Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="ef180-397">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="ef180-398">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="ef180-398">**Key**: contentRoot</span></span>  
<span data-ttu-id="ef180-399">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="ef180-399">**Type**: *string*</span></span>  
<span data-ttu-id="ef180-400">**Výchozí**: Výchozím nastavením je složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-400">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="ef180-401">**Nastavit pomocí**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="ef180-401">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="ef180-402">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ef180-402">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="ef180-403">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="ef180-403">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a><span data-ttu-id="ef180-404">Prostředí</span><span class="sxs-lookup"><span data-stu-id="ef180-404">Environment</span></span>

<span data-ttu-id="ef180-405">Nastaví [prostředí](xref:fundamentals/environments)aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-405">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ef180-406">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="ef180-406">**Key**: environment</span></span>  
<span data-ttu-id="ef180-407">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="ef180-407">**Type**: *string*</span></span>  
<span data-ttu-id="ef180-408">**Výchozí**: Produkční</span><span class="sxs-lookup"><span data-stu-id="ef180-408">**Default**: Production</span></span>  
<span data-ttu-id="ef180-409">**Nastavit pomocí**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="ef180-409">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="ef180-410">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ef180-410">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="ef180-411">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ef180-411">The environment can be set to any value.</span></span> <span data-ttu-id="ef180-412">Hodnoty definované rozhraním zahrnují `Development`, `Staging`a `Production`.</span><span class="sxs-lookup"><span data-stu-id="ef180-412">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ef180-413">U hodnot se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="ef180-413">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="ef180-414">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="ef180-414">ConfigureHostConfiguration</span></span>

<span data-ttu-id="ef180-415"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*><xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> používá k<xref:Microsoft.Extensions.Configuration.IConfiguration> vytvoření pro hostitele.</span><span class="sxs-lookup"><span data-stu-id="ef180-415"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="ef180-416">Konfigurace hostitele slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-416">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="ef180-417"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="ef180-417"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="ef180-418">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="ef180-418">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="ef180-419">Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé.</span><span class="sxs-lookup"><span data-stu-id="ef180-419">No providers are included by default.</span></span> <span data-ttu-id="ef180-420">Musíte explicitně určit, v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>jakém poskytovateli konfigurace aplikace vyžaduje, včetně:</span><span class="sxs-lookup"><span data-stu-id="ef180-420">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="ef180-421">Konfigurace souboru (například ze souboru *HostSettings. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="ef180-421">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="ef180-422">Konfigurace proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ef180-422">Environment variable configuration.</span></span>
* <span data-ttu-id="ef180-423">Konfigurace argumentů příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="ef180-423">Command-line argument configuration.</span></span>
* <span data-ttu-id="ef180-424">Všichni další povinní poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ef180-424">Any other required configuration providers.</span></span>

<span data-ttu-id="ef180-425">Konfigurace souborů hostitele je povolená zadáním základní cesty `SetBasePath` aplikace a následným voláním jednoho z [poskytovatelů konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ef180-425">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="ef180-426">Ukázková aplikace používá soubor JSON, *HostSettings. JSON*, a volání <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> pro využívání nastavení konfigurace hostitele souboru.</span><span class="sxs-lookup"><span data-stu-id="ef180-426">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="ef180-427">Chcete-li přidat [konfiguraci proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="ef180-427">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="ef180-428">`AddEnvironmentVariables`přijme volitelnou uživatelem definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="ef180-428">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="ef180-429">Ukázková aplikace používá předponu `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="ef180-429">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="ef180-430">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="ef180-430">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="ef180-431">Když je hostitel ukázkové aplikace nakonfigurovaný, hodnota `PREFIX_ENVIRONMENT` proměnné prostředí se bude hodnotou konfigurace hostitele pro tento `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="ef180-431">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="ef180-432">Během vývoje při používání sady [Visual Studio](https://visualstudio.microsoft.com) nebo při spuštění aplikace `dotnet run`s se mohou proměnné prostředí nastavit v souboru *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="ef180-432">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="ef180-433">V [Visual Studio Code](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *. VSCode/Launch. JSON* během vývoje.</span><span class="sxs-lookup"><span data-stu-id="ef180-433">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="ef180-434">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ef180-434">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ef180-435">[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="ef180-435">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="ef180-436">K povolení argumentů příkazového řádku pro přepsání konfigurace poskytované předchozími poskytovateli konfigurace se přidá poslední konfigurace příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="ef180-436">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="ef180-437">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ef180-437">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="ef180-438">Další konfiguraci lze poskytnout pomocí klíčů [ApplicationName](#application-key-name) a [contentRoot](#content-root) .</span><span class="sxs-lookup"><span data-stu-id="ef180-438">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="ef180-439">Příklad `HostBuilder` konfigurace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="ef180-439">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="ef180-440">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="ef180-440">ConfigureAppConfiguration</span></span>

<span data-ttu-id="ef180-441">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementace.</span><span class="sxs-lookup"><span data-stu-id="ef180-441">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="ef180-442"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*><xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> používá k<xref:Microsoft.Extensions.Configuration.IConfiguration> vytvoření pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ef180-442"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="ef180-443"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="ef180-443"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="ef180-444">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="ef180-444">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="ef180-445">Konfigurace vytvořená nástrojem <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné <xref:Microsoft.Extensions.Hosting.IHost.Services*>operace a v.</span><span class="sxs-lookup"><span data-stu-id="ef180-445">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="ef180-446">Konfigurace aplikace automaticky přijímá konfiguraci hostitele, kterou poskytuje [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ef180-446">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="ef180-447">Příklad konfigurace aplikace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="ef180-447">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="ef180-448">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ef180-448">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="ef180-449">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="ef180-449">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="ef180-450">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="ef180-450">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="ef180-451">Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="ef180-451">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="ef180-452">Ukázková aplikace přesune své soubory nastavení aplikace JSON a *HostSettings. JSON* s následující `<Content>` položkou:</span><span class="sxs-lookup"><span data-stu-id="ef180-452">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="ef180-453">Metody rozšíření konfigurace, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> například a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, například [Microsoft. Extensions. Configuration. JSON](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft. Extensions. Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="ef180-453">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="ef180-454">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), tyto balíčky musí být kromě základního balíčku [Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) přidány do projektu.</span><span class="sxs-lookup"><span data-stu-id="ef180-454">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="ef180-455">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ef180-455">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="ef180-456">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="ef180-456">ConfigureServices</span></span>

<span data-ttu-id="ef180-457"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>Přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-457"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ef180-458"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="ef180-458"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="ef180-459">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="ef180-459">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="ef180-460">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="ef180-460">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="ef180-461">[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) `AddHostedService` používá metodu rozšíření k přidání služby pro události života, `LifetimeEventsHostedService`a časově omezená úloha `TimedHostedService`na pozadí, do aplikace:</span><span class="sxs-lookup"><span data-stu-id="ef180-461">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="ef180-462">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="ef180-462">ConfigureLogging</span></span>

<span data-ttu-id="ef180-463"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>Přidá delegáta pro konfiguraci poskytnutého <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ef180-463"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="ef180-464"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>může být voláno vícekrát s přísadou výsledků.</span><span class="sxs-lookup"><span data-stu-id="ef180-464"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="ef180-465">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="ef180-465">UseConsoleLifetime</span></span>

<span data-ttu-id="ef180-466"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>naslouchá klávesám CTRL + C/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pro spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="ef180-466"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="ef180-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="ef180-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="ef180-468"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>je předem registrován jako výchozí implementace životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="ef180-468"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="ef180-469">Je použita poslední registrovaná doba života.</span><span class="sxs-lookup"><span data-stu-id="ef180-469">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="ef180-470">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="ef180-470">Container configuration</span></span>

<span data-ttu-id="ef180-471">Pro podporu zapojení do jiných kontejnerů může hostitel přijmout <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="ef180-471">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="ef180-472">Poskytnutí továrny není součástí registrace DI Container, ale je místo toho hostitel, který se používá k vytvoření konkrétního kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="ef180-472">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="ef180-473">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí továrnu, která byla použita k vytvoření poskytovatele služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-473">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="ef180-474">Vlastní konfigurace kontejneru je spravovaná <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metodou.</span><span class="sxs-lookup"><span data-stu-id="ef180-474">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="ef180-475"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>poskytuje silné typové prostředí pro konfiguraci kontejneru nad podkladovým rozhraním API hostitele.</span><span class="sxs-lookup"><span data-stu-id="ef180-475"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="ef180-476"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="ef180-476"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="ef180-477">Vytvoření kontejneru služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="ef180-477">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="ef180-478">Poskytněte objekt pro vytváření kontejnerů služby:</span><span class="sxs-lookup"><span data-stu-id="ef180-478">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="ef180-479">Použijte objekt pro vytváření a nakonfigurujte vlastní kontejner služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="ef180-479">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="ef180-480">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="ef180-480">Extensibility</span></span>

<span data-ttu-id="ef180-481">Rozšiřitelnost hostitelů se provádí s metodami <xref:Microsoft.Extensions.Hosting.IHostBuilder>rozšíření na.</span><span class="sxs-lookup"><span data-stu-id="ef180-481">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="ef180-482">Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementaci s příkladem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) znázorněným v. <xref:fundamentals/host/hosted-services></span><span class="sxs-lookup"><span data-stu-id="ef180-482">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="ef180-483">Aplikace vytváří `UseHostedService` metodu rozšíření pro registraci hostované služby, která byla `T`předána:</span><span class="sxs-lookup"><span data-stu-id="ef180-483">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="ef180-484">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="ef180-484">Manage the host</span></span>

<span data-ttu-id="ef180-485">Implementace zodpovídá za spouštění a <xref:Microsoft.Extensions.Hosting.IHostedService> zastavování implementací, které jsou zaregistrované v kontejneru služby. <xref:Microsoft.Extensions.Hosting.IHost></span><span class="sxs-lookup"><span data-stu-id="ef180-485">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="ef180-486">Spustit</span><span class="sxs-lookup"><span data-stu-id="ef180-486">Run</span></span>

<span data-ttu-id="ef180-487"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud není hostitel vypnutý:</span><span class="sxs-lookup"><span data-stu-id="ef180-487"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="ef180-488">RunAsync</span><span class="sxs-lookup"><span data-stu-id="ef180-488">RunAsync</span></span>

<span data-ttu-id="ef180-489"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí:</span><span class="sxs-lookup"><span data-stu-id="ef180-489"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="ef180-490">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="ef180-490">RunConsoleAsync</span></span>

<span data-ttu-id="ef180-491"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká na vypnutí CTRL + C/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ef180-491"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="ef180-492">Spustit a StopAsync</span><span class="sxs-lookup"><span data-stu-id="ef180-492">Start and StopAsync</span></span>

<span data-ttu-id="ef180-493"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="ef180-493"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="ef180-494"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusy o zastavení hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="ef180-494"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="ef180-495">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="ef180-495">StartAsync and StopAsync</span></span>

<span data-ttu-id="ef180-496"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ef180-496"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="ef180-497"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>ukončí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ef180-497"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="ef180-498">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="ef180-498">WaitForShutdown</span></span>

<span data-ttu-id="ef180-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>je aktivována prostřednictvím <xref:Microsoft.Extensions.Hosting.IHostLifetime>, <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> například (naslouchá pro CTRL + C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="ef180-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="ef180-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ef180-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="ef180-501">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="ef180-501">WaitForShutdownAsync</span></span>

<span data-ttu-id="ef180-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>daného tokenu a volání.</span><span class="sxs-lookup"><span data-stu-id="ef180-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="ef180-503">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="ef180-503">External control</span></span>

<span data-ttu-id="ef180-504">Externí kontrolu hostitele lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="ef180-504">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="ef180-505"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, což čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="ef180-505"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="ef180-506">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="ef180-506">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="ef180-507">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="ef180-507">IHostingEnvironment interface</span></span>

<span data-ttu-id="ef180-508"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>poskytuje informace o hostitelském prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-508"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="ef180-509">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> aby bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="ef180-509">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="ef180-510">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ef180-510">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="ef180-511">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ef180-511">IApplicationLifetime interface</span></span>

<span data-ttu-id="ef180-512"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>umožňuje aktivity po spuštění a vypnutí, včetně řádných požadavků na vypnutí.</span><span class="sxs-lookup"><span data-stu-id="ef180-512"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="ef180-513">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="ef180-513">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="ef180-514">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="ef180-514">Cancellation Token</span></span> | <span data-ttu-id="ef180-515">Aktivováno, když&#8230;</span><span class="sxs-lookup"><span data-stu-id="ef180-515">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="ef180-516">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="ef180-516">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="ef180-517">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="ef180-517">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="ef180-518">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="ef180-518">All requests should be processed.</span></span> <span data-ttu-id="ef180-519">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="ef180-519">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="ef180-520">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="ef180-520">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="ef180-521">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="ef180-521">Requests may still be processing.</span></span> <span data-ttu-id="ef180-522">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="ef180-522">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="ef180-523">Konstruktor – vloží <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> službu do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="ef180-523">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="ef180-524">[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání konstruktoru do `LifetimeEventsHostedService` třídy ( <xref:Microsoft.Extensions.Hosting.IHostedService> implementace) pro registraci událostí.</span><span class="sxs-lookup"><span data-stu-id="ef180-524">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="ef180-525">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="ef180-525">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="ef180-526"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>požaduje ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ef180-526"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="ef180-527">Následující třída používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> k bezproblémovému vypnutí aplikace při volání `Shutdown` metody třídy:</span><span class="sxs-lookup"><span data-stu-id="ef180-527">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ef180-528">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ef180-528">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
