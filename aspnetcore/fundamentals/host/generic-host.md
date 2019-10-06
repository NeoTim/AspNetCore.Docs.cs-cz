---
title: Obecný hostitel .NET
author: tdykstra
description: Seznamte se s obecným hostitelem .NET Core, který je zodpovědný za spouštění a správu životního cyklu aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: bd6e01697900b93d5b98122c726e1f8c8b89c0fc
ms.sourcegitcommit: 4115bf0e850c13d4e655beb5ab5e8ff431173cb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981931"
---
# <a name="net-generic-host"></a><span data-ttu-id="d19e5-103">Obecný hostitel .NET</span><span class="sxs-lookup"><span data-stu-id="d19e5-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d19e5-104">Tento článek představuje obecného hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny k jeho použití.</span><span class="sxs-lookup"><span data-stu-id="d19e5-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="d19e5-105">Co je hostitel?</span><span class="sxs-lookup"><span data-stu-id="d19e5-105">What's a host?</span></span>

<span data-ttu-id="d19e5-106">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="d19e5-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="d19e5-107">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="d19e5-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="d19e5-108">Protokolování</span><span class="sxs-lookup"><span data-stu-id="d19e5-108">Logging</span></span>
* <span data-ttu-id="d19e5-109">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="d19e5-109">Configuration</span></span>
* <span data-ttu-id="d19e5-110">@no__t – 0 implementace</span><span class="sxs-lookup"><span data-stu-id="d19e5-110">`IHostedService` implementations</span></span>

<span data-ttu-id="d19e5-111">Při spuštění hostitele zavolá `IHostedService.StartAsync` na každé implementaci <xref:Microsoft.Extensions.Hosting.IHostedService>, kterou najde v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="d19e5-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="d19e5-112">V rámci webové aplikace je jednou z implementací `IHostedService` webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="d19e5-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="d19e5-113">Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="d19e5-114">Ve verzích ASP.NET Core starších než 3,0 se [webový hostitel](xref:fundamentals/host/web-host) používá pro úlohy http.</span><span class="sxs-lookup"><span data-stu-id="d19e5-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="d19e5-115">Webový hostitel již není doporučen pro webové aplikace a zůstává dostupný pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d19e5-116">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="d19e5-116">Set up a host</span></span>

<span data-ttu-id="d19e5-117">Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve třídě `Program`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="d19e5-118">`Main` Metody:</span><span class="sxs-lookup"><span data-stu-id="d19e5-118">The `Main` method:</span></span>

* <span data-ttu-id="d19e5-119">Volá metodu `CreateHostBuilder` pro vytvoření a konfiguraci objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="d19e5-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="d19e5-120">Zavolá @no__t metody-0 a `Run` v objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="d19e5-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="d19e5-121">Zde je *program.cs* kód pro úlohu jiného typu než HTTP a jedna implementace `IHostedService` přidána do kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="d19e5-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="d19e5-122">Pro úlohy HTTP je metoda `Main` stejná, ale volání `CreateHostBuilder` `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d19e5-123">Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu metody `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="d19e5-124">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že budou nalezeny metody `CreateHostBuilder`, které nakonfigurují hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="d19e5-125">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="d19e5-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="d19e5-126">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="d19e5-126">Default builder settings</span></span> 

<span data-ttu-id="d19e5-127"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Metody:</span><span class="sxs-lookup"><span data-stu-id="d19e5-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="d19e5-128">Nastaví kořen obsahu na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-128">Sets the content root to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="d19e5-129">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="d19e5-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="d19e5-130">Proměnné prostředí s předponou "DOTNET_".</span><span class="sxs-lookup"><span data-stu-id="d19e5-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="d19e5-131">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d19e5-131">Command-line arguments.</span></span>
* <span data-ttu-id="d19e5-132">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="d19e5-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="d19e5-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d19e5-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="d19e5-134">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="d19e5-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d19e5-135">[Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží v prostředí `Development`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="d19e5-136">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-136">Environment variables.</span></span>
  * <span data-ttu-id="d19e5-137">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d19e5-137">Command-line arguments.</span></span>
* <span data-ttu-id="d19e5-138">Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="d19e5-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="d19e5-139">Konzola</span><span class="sxs-lookup"><span data-stu-id="d19e5-139">Console</span></span>
  * <span data-ttu-id="d19e5-140">Ladění</span><span class="sxs-lookup"><span data-stu-id="d19e5-140">Debug</span></span>
  * <span data-ttu-id="d19e5-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="d19e5-141">EventSource</span></span>
  * <span data-ttu-id="d19e5-142">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="d19e5-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="d19e5-143">Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="d19e5-144">`ConfigureWebHostDefaults` Metody:</span><span class="sxs-lookup"><span data-stu-id="d19e5-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="d19e5-145">Načte konfiguraci hostitele z proměnných prostředí s předponou "ASPNETCORE_".</span><span class="sxs-lookup"><span data-stu-id="d19e5-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="d19e5-146">Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="d19e5-147">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d19e5-148">Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d19e5-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="d19e5-149">Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , pokud ASPNETCORE_FORWARDEDHEADERS_ENABLED = true.</span><span class="sxs-lookup"><span data-stu-id="d19e5-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="d19e5-150">Povolí integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="d19e5-150">Enables IIS integration.</span></span> <span data-ttu-id="d19e5-151">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="d19e5-152">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="d19e5-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d19e5-153">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="d19e5-153">Framework-provided services</span></span>

<span data-ttu-id="d19e5-154">Mezi služby, které jsou zaregistrovány automaticky, patří následující:</span><span class="sxs-lookup"><span data-stu-id="d19e5-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="d19e5-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d19e5-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="d19e5-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d19e5-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="d19e5-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d19e5-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="d19e5-158">Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="d19e5-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d19e5-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="d19e5-160">Vložením služby <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (dřív `IApplicationLifetime`) do libovolné třídy můžete zpracovat úlohy po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="d19e5-161">Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="d19e5-162">Rozhraní obsahuje také metodu `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="d19e5-163">Následující příklad je implementace `IHostedService` registrující události `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="d19e5-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d19e5-164">IHostLifetime</span></span>

<span data-ttu-id="d19e5-165">Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> určuje, kdy se hostitel spustí a kdy se zastaví.</span><span class="sxs-lookup"><span data-stu-id="d19e5-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="d19e5-166">Použije se Poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-166">The last implementation registered is used.</span></span>

<span data-ttu-id="d19e5-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> je výchozí implementace `IHostLifetime`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="d19e5-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="d19e5-169">čeká na klávesovou zkratku CTRL + C/SIGINT nebo SIGTERM a volání <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-169">listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="d19e5-170">Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d19e5-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="d19e5-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d19e5-171">IHostEnvironment</span></span>

<span data-ttu-id="d19e5-172">Vložením služby <xref:Microsoft.Extensions.Hosting.IHostEnvironment> do třídy získáte informace o následujícím:</span><span class="sxs-lookup"><span data-stu-id="d19e5-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="d19e5-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d19e5-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="d19e5-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d19e5-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="d19e5-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d19e5-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="d19e5-176">Webové aplikace implementují rozhraní @no__t 0, které dědí `IHostEnvironment` a přidává:</span><span class="sxs-lookup"><span data-stu-id="d19e5-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds:</span></span>

* [<span data-ttu-id="d19e5-177">WebRootPath</span><span class="sxs-lookup"><span data-stu-id="d19e5-177">WebRootPath</span></span>](#webroot)

## <a name="host-configuration"></a><span data-ttu-id="d19e5-178">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="d19e5-178">Host configuration</span></span>

<span data-ttu-id="d19e5-179">Konfigurace hostitele se používá pro vlastnosti implementace <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="d19e5-180">Konfigurace hostitele je k dispozici z [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) v rámci <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="d19e5-181">Po `ConfigureAppConfiguration` se `HostBuilderContext.Configuration` nahradí konfigurací aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="d19e5-182">Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> v `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d19e5-183">`ConfigureHostConfiguration` se dá volat víckrát s přičítáním výsledků.</span><span class="sxs-lookup"><span data-stu-id="d19e5-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d19e5-184">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="d19e5-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d19e5-185">Zprostředkovatel proměnné prostředí s předponou `DOTNET_` a argumenty příkazového řádku jsou obsaženy v CreateDefaultBuilder.</span><span class="sxs-lookup"><span data-stu-id="d19e5-185">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="d19e5-186">U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="d19e5-187">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d19e5-188">Například hodnota proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro klíč `environment`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d19e5-189">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="d19e5-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="d19e5-190">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="d19e5-190">App configuration</span></span>

<span data-ttu-id="d19e5-191">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> v `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d19e5-192">`ConfigureAppConfiguration` se dá volat víckrát s přičítáním výsledků.</span><span class="sxs-lookup"><span data-stu-id="d19e5-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d19e5-193">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="d19e5-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="d19e5-194">Konfigurace vytvořená `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di.</span><span class="sxs-lookup"><span data-stu-id="d19e5-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="d19e5-195">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="d19e5-196">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d19e5-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="d19e5-197">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="d19e5-197">Settings for all app types</span></span>

<span data-ttu-id="d19e5-198">V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP.</span><span class="sxs-lookup"><span data-stu-id="d19e5-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d19e5-199">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="d19e5-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d19e5-200">ApplicationName</span></span>

<span data-ttu-id="d19e5-201">Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="d19e5-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="d19e5-202">**Klíč**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d19e5-202">**Key**: applicationName</span></span>  
<span data-ttu-id="d19e5-203">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-203">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-204">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="d19e5-205">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d19e5-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="d19e5-206">K nastavení této hodnoty použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-206">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="d19e5-207">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d19e5-207">ContentRootPath</span></span>

<span data-ttu-id="d19e5-208">Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="d19e5-209">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="d19e5-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="d19e5-210">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="d19e5-210">**Key**: contentRoot</span></span>  
<span data-ttu-id="d19e5-211">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-211">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-212">**Výchozí**: Složka, ve které se nachází sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="d19e5-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="d19e5-213">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d19e5-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="d19e5-214">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseContentRoot` v `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

### <a name="environmentname"></a><span data-ttu-id="d19e5-215">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d19e5-215">EnvironmentName</span></span>

<span data-ttu-id="d19e5-216">Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-216">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="d19e5-217">Mezi hodnoty definované rozhraním patří `Development`, `Staging` a `Production`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-217">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d19e5-218">U hodnot se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="d19e5-218">Values aren't case sensitive.</span></span>

<span data-ttu-id="d19e5-219">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="d19e5-219">**Key**: environment</span></span>  
<span data-ttu-id="d19e5-220">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-220">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-221">**Výchozí**: Produkční</span><span class="sxs-lookup"><span data-stu-id="d19e5-221">**Default**: Production</span></span>  
<span data-ttu-id="d19e5-222">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d19e5-222">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="d19e5-223">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseEnvironment` v `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-223">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="d19e5-224">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="d19e5-224">ShutdownTimeout</span></span>

<span data-ttu-id="d19e5-225">[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-225">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d19e5-226">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="d19e5-226">The default value is five seconds.</span></span>  <span data-ttu-id="d19e5-227">Po uplynutí časového limitu hostitel:</span><span class="sxs-lookup"><span data-stu-id="d19e5-227">During the timeout period, the host:</span></span>

* <span data-ttu-id="d19e5-228">Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="d19e5-228">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d19e5-229">Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="d19e5-229">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="d19e5-230">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="d19e5-230">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d19e5-231">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="d19e5-231">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d19e5-232">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="d19e5-232">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="d19e5-233">**Klíč**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="d19e5-233">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="d19e5-234">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="d19e5-234">**Type**: *int*</span></span>  
<span data-ttu-id="d19e5-235">**Výchozí**: 5 sekund **Proměnná prostředí**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d19e5-235">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d19e5-236">K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-236">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="d19e5-237">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="d19e5-237">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="d19e5-238">Nastavení pro Web Apps</span><span class="sxs-lookup"><span data-stu-id="d19e5-238">Settings for web apps</span></span>

<span data-ttu-id="d19e5-239">Některá nastavení hostitele se vztahují jenom na úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="d19e5-239">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="d19e5-240">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-240">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="d19e5-241">Pro tato nastavení jsou k dispozici metody rozšíření `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-241">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="d19e5-242">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` je instance `IWebHostBuilder`, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d19e5-242">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="d19e5-243">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d19e5-243">CaptureStartupErrors</span></span>

<span data-ttu-id="d19e5-244">Pokud `false`, chyby během spuštění hostitele se ukončí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-244">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d19e5-245">Při `true` zachytí hostitel při spuštění výjimky a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="d19e5-245">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="d19e5-246">**Klíč**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d19e5-246">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="d19e5-247">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="d19e5-247">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d19e5-248">**Výchozí**: Výchozí hodnota je `false`, pokud se aplikace nespustí s Kestrel za IIS, kde výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-248">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d19e5-249">**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d19e5-249">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d19e5-250">Chcete-li nastavit tuto hodnotu, použijte metodu Configuration nebo call `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-250">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="d19e5-251">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="d19e5-251">DetailedErrors</span></span>

<span data-ttu-id="d19e5-252">Pokud je povoleno nebo když je prostředí `Development`, aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="d19e5-252">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="d19e5-253">**Klíč**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="d19e5-253">**Key**: detailedErrors</span></span>  
<span data-ttu-id="d19e5-254">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="d19e5-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d19e5-255">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="d19e5-255">**Default**: false</span></span>  
<span data-ttu-id="d19e5-256">**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d19e5-256">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="d19e5-257">Chcete-li nastavit tuto hodnotu, použijte metodu Configuration nebo call `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-257">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="d19e5-258">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d19e5-258">HostingStartupAssemblies</span></span>

<span data-ttu-id="d19e5-259">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="d19e5-259">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="d19e5-260">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-260">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d19e5-261">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="d19e5-261">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="d19e5-262">**Klíč**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d19e5-262">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="d19e5-263">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-263">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-264">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="d19e5-264">**Default**: Empty string</span></span>  
<span data-ttu-id="d19e5-265">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d19e5-265">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d19e5-266">Chcete-li nastavit tuto hodnotu, použijte metodu Configuration nebo call `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-266">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="d19e5-267">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d19e5-267">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="d19e5-268">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="d19e5-268">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d19e5-269">**Klíč**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d19e5-269">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="d19e5-270">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-270">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-271">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="d19e5-271">**Default**: Empty string</span></span>  
<span data-ttu-id="d19e5-272">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d19e5-272">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="d19e5-273">Chcete-li nastavit tuto hodnotu, použijte metodu Configuration nebo call `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="d19e5-274">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="d19e5-274">HTTPS_Port</span></span>

<span data-ttu-id="d19e5-275">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d19e5-275">The HTTPS redirect port.</span></span> <span data-ttu-id="d19e5-276">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="d19e5-276">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d19e5-277">**Klíč**: https_port</span><span class="sxs-lookup"><span data-stu-id="d19e5-277">**Key**: https_port</span></span>  
<span data-ttu-id="d19e5-278">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-278">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-279">**Výchozí**: Výchozí hodnota není nastavena.</span><span class="sxs-lookup"><span data-stu-id="d19e5-279">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="d19e5-280">**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d19e5-280">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="d19e5-281">Chcete-li nastavit tuto hodnotu, použijte metodu Configuration nebo call `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="d19e5-282">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d19e5-282">PreferHostingUrls</span></span>

<span data-ttu-id="d19e5-283">Určuje, zda má hostitel naslouchat adresám URL konfigurovaným pomocí `IWebHostBuilder` namísto nastavení nakonfigurovaného pomocí implementace `IServer`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-283">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d19e5-284">**Klíč**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d19e5-284">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="d19e5-285">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="d19e5-285">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d19e5-286">**Výchozí**: true</span><span class="sxs-lookup"><span data-stu-id="d19e5-286">**Default**: true</span></span>  
<span data-ttu-id="d19e5-287">**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d19e5-287">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="d19e5-288">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-288">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="d19e5-289">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d19e5-289">PreventHostingStartup</span></span>

<span data-ttu-id="d19e5-290">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-290">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d19e5-291">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-291">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d19e5-292">**Klíč**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d19e5-292">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="d19e5-293">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="d19e5-293">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d19e5-294">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="d19e5-294">**Default**: false</span></span>  
<span data-ttu-id="d19e5-295">**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d19e5-295">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="d19e5-296">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-296">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="d19e5-297">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="d19e5-297">StartupAssembly</span></span>

<span data-ttu-id="d19e5-298">Sestavení, ve kterém se má hledat třída `Startup`</span><span class="sxs-lookup"><span data-stu-id="d19e5-298">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d19e5-299">**Klíč**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="d19e5-299">**Key**: startupAssembly</span></span>  
<span data-ttu-id="d19e5-300">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-300">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-301">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="d19e5-301">**Default**: The app's assembly</span></span>  
<span data-ttu-id="d19e5-302">**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d19e5-302">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d19e5-303">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-303">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="d19e5-304">`UseStartup` může převzít název sestavení (`string`) nebo typ (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="d19e5-304">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="d19e5-305">Je-li voláno více metod `UseStartup`, má poslední přednost.</span><span class="sxs-lookup"><span data-stu-id="d19e5-305">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="d19e5-306">URL – adresy</span><span class="sxs-lookup"><span data-stu-id="d19e5-306">URLs</span></span>

<span data-ttu-id="d19e5-307">Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="d19e5-307">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="d19e5-308">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-308">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d19e5-309">Pomocí "\*" označíte, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="d19e5-309">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d19e5-310">Protokol (`http://` nebo `https://`) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d19e5-310">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d19e5-311">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="d19e5-311">Supported formats vary among servers.</span></span>

<span data-ttu-id="d19e5-312">**Klíč**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="d19e5-312">**Key**: urls</span></span>  
<span data-ttu-id="d19e5-313">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-313">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-314">**Výchozí**: `http://localhost:5000` a `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="d19e5-314">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="d19e5-315">**Proměnná prostředí**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="d19e5-315">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="d19e5-316">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-316">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="d19e5-317">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-317">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d19e5-318">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-318">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="d19e5-319">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d19e5-319">WebRoot</span></span>

<span data-ttu-id="d19e5-320">Relativní cesta ke statickým prostředkům aplikace</span><span class="sxs-lookup"><span data-stu-id="d19e5-320">The relative path to the app's static assets.</span></span>

<span data-ttu-id="d19e5-321">**Klíč**: Webroot</span><span class="sxs-lookup"><span data-stu-id="d19e5-321">**Key**: webroot</span></span>  
<span data-ttu-id="d19e5-322">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-322">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-323">**Výchozí**: *(Kořen obsahu)/wwwroot*, pokud cesta existuje.</span><span class="sxs-lookup"><span data-stu-id="d19e5-323">**Default**: *(Content Root)/wwwroot*, if the path exists.</span></span> <span data-ttu-id="d19e5-324">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="d19e5-324">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="d19e5-325">**Proměnná prostředí**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d19e5-325">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="d19e5-326">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseWebRoot`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-326">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="d19e5-327">Správa životnosti hostitele</span><span class="sxs-lookup"><span data-stu-id="d19e5-327">Manage the host lifetime</span></span>

<span data-ttu-id="d19e5-328">Pro spuštění a zastavení aplikace volejte metody pro vytvořenou implementaci <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-328">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="d19e5-329">Tyto metody ovlivňují všechny implementace <xref:Microsoft.Extensions.Hosting.IHostedService>, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="d19e5-329">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d19e5-330">Spusťte</span><span class="sxs-lookup"><span data-stu-id="d19e5-330">Run</span></span>

<span data-ttu-id="d19e5-331"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.</span><span class="sxs-lookup"><span data-stu-id="d19e5-331"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="d19e5-332">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d19e5-332">RunAsync</span></span>

<span data-ttu-id="d19e5-333"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-333"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="d19e5-334">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d19e5-334">RunConsoleAsync</span></span>

<span data-ttu-id="d19e5-335"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> povolí podporu konzoly, sestaví a spustí hostitele a počká, až se vypne CTRL + C/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="d19e5-335"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="d19e5-336">Spustit</span><span class="sxs-lookup"><span data-stu-id="d19e5-336">Start</span></span>

<span data-ttu-id="d19e5-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="d19e5-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="d19e5-338">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d19e5-338">StartAsync</span></span>

<span data-ttu-id="d19e5-339"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí hostitele a vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-339"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="d19e5-340"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se volá na začátku `StartAsync`, která čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="d19e5-340"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d19e5-341">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-341">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="d19e5-342">StopAsync</span><span class="sxs-lookup"><span data-stu-id="d19e5-342">StopAsync</span></span>

<span data-ttu-id="d19e5-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> se pokusí zastavit hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="d19e5-344">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d19e5-344">WaitForShutdown</span></span>

<span data-ttu-id="d19e5-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například prostřednictvím CTRL + C/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="d19e5-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="d19e5-346">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d19e5-346">WaitForShutdownAsync</span></span>

<span data-ttu-id="d19e5-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="d19e5-348">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="d19e5-348">External control</span></span>

<span data-ttu-id="d19e5-349">Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="d19e5-349">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="d19e5-350">ASP.NET Core aplikace nakonfigurují a spouštějí hostitele.</span><span class="sxs-lookup"><span data-stu-id="d19e5-350">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="d19e5-351">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="d19e5-351">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="d19e5-352">Tento článek se zabývá ASP.NET Core obecný hostitel (<xref:Microsoft.Extensions.Hosting.HostBuilder>), který se používá pro aplikace, které nezpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="d19e5-352">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="d19e5-353">Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele, aby bylo možné rozšířit pole hostitelských scénářů.</span><span class="sxs-lookup"><span data-stu-id="d19e5-353">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="d19e5-354">Zasílání zpráv, úloh na pozadí a dalších úloh nevyužívajících HTTP na základě výhody obecného hostitele z různých možností, jako je konfigurace, vkládání závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="d19e5-354">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="d19e5-355">Obecný hostitel je v ASP.NET Core 2,1 novinkou a není vhodný pro scénáře hostování webů.</span><span class="sxs-lookup"><span data-stu-id="d19e5-355">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="d19e5-356">U scénářů pro hostování webů použijte [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="d19e5-356">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d19e5-357">Obecný hostitel nahradí webového hostitele v budoucí verzi a bude fungovat jako primární rozhraní API v rámci scénářů HTTP i non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="d19e5-357">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="d19e5-358">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d19e5-358">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d19e5-359">Při spuštění ukázkové aplikace v [Visual Studio Code](https://code.visualstudio.com/)použijte *externí nebo integrovaný terminál*.</span><span class="sxs-lookup"><span data-stu-id="d19e5-359">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="d19e5-360">Nespouštějte ukázku v `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-360">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="d19e5-361">Nastavení konzoly v Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="d19e5-361">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="d19e5-362">Otevřete soubor *. VSCode/Launch. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d19e5-362">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="d19e5-363">V konfiguraci **spuštění .NET Core (konzola)** vyhledejte položku **Konzola** .</span><span class="sxs-lookup"><span data-stu-id="d19e5-363">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="d19e5-364">Nastavte hodnotu na buď `externalTerminal`, nebo `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-364">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="d19e5-365">Úvod</span><span class="sxs-lookup"><span data-stu-id="d19e5-365">Introduction</span></span>

<span data-ttu-id="d19e5-366">Knihovna obecných hostitelů je k dispozici v oboru názvů <xref:Microsoft.Extensions.Hosting> a poskytuje balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) .</span><span class="sxs-lookup"><span data-stu-id="d19e5-366">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="d19e5-367">Balíček `Microsoft.Extensions.Hosting` je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="d19e5-367">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="d19e5-368"><xref:Microsoft.Extensions.Hosting.IHostedService> je vstupním bodem pro provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-368"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="d19e5-369">Každá implementace <xref:Microsoft.Extensions.Hosting.IHostedService> se spouští v pořadí [Registrace služby v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="d19e5-369">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="d19e5-370"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> se volá na každé <xref:Microsoft.Extensions.Hosting.IHostedService> při spuštění hostitele a v pořadí zpětné registrace se volá <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>, když se hostitel řádně ukončí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-370"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d19e5-371">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="d19e5-371">Set up a host</span></span>

<span data-ttu-id="d19e5-372"><xref:Microsoft.Extensions.Hosting.IHostBuilder> je hlavní součást, kterou knihovny a aplikace používají k inicializaci, sestavení a spuštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="d19e5-372"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="d19e5-373">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d19e5-373">Options</span></span>

<span data-ttu-id="d19e5-374"><xref:Microsoft.Extensions.Hosting.HostOptions> konfigurovat možnosti pro <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-374"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="d19e5-375">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="d19e5-375">Shutdown timeout</span></span>

<span data-ttu-id="d19e5-376"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-376"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d19e5-377">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="d19e5-377">The default value is five seconds.</span></span>

<span data-ttu-id="d19e5-378">Následující konfigurace možnosti v `Program.Main` zvyšuje výchozí časový limit pět sekund na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="d19e5-378">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="d19e5-379">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="d19e5-379">Default services</span></span>

<span data-ttu-id="d19e5-380">Během inicializace hostitele jsou registrovány následující služby:</span><span class="sxs-lookup"><span data-stu-id="d19e5-380">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="d19e5-381">[Prostředí](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="d19e5-381">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="d19e5-382">[Konfigurace](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="d19e5-382">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="d19e5-383"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="d19e5-383"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="d19e5-384"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="d19e5-384"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="d19e5-385">[Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="d19e5-385">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="d19e5-386">[Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="d19e5-386">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d19e5-387">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="d19e5-387">Host configuration</span></span>

<span data-ttu-id="d19e5-388">Konfiguraci hostitele vytvořil:</span><span class="sxs-lookup"><span data-stu-id="d19e5-388">Host configuration is created by:</span></span>

* <span data-ttu-id="d19e5-389">Volání metod rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder> pro nastavení [kořenového adresáře obsahu](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="d19e5-389">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="d19e5-390">Čtení konfigurace od zprostředkovatelů konfigurace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-390">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="d19e5-391">Metody rozšíření</span><span class="sxs-lookup"><span data-stu-id="d19e5-391">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="d19e5-392">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="d19e5-392">Application key (name)</span></span>

<span data-ttu-id="d19e5-393">Vlastnost [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="d19e5-393">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="d19e5-394">Chcete-li nastavit hodnotu explicitně, použijte [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="d19e5-394">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="d19e5-395">**Klíč**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d19e5-395">**Key**: applicationName</span></span>  
<span data-ttu-id="d19e5-396">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-396">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-397">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace</span><span class="sxs-lookup"><span data-stu-id="d19e5-397">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="d19e5-398">**Nastavit pomocí**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="d19e5-398">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="d19e5-399">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d19e5-399">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="d19e5-400">Kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="d19e5-400">Content root</span></span>

<span data-ttu-id="d19e5-401">Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-401">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="d19e5-402">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="d19e5-402">**Key**: contentRoot</span></span>  
<span data-ttu-id="d19e5-403">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-403">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-404">**Výchozí**: Výchozím nastavením je složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-404">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="d19e5-405">**Nastavit pomocí**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="d19e5-405">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="d19e5-406">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d19e5-406">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d19e5-407">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="d19e5-407">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a><span data-ttu-id="d19e5-408">Prostředí</span><span class="sxs-lookup"><span data-stu-id="d19e5-408">Environment</span></span>

<span data-ttu-id="d19e5-409">Nastaví [prostředí](xref:fundamentals/environments)aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-409">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="d19e5-410">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="d19e5-410">**Key**: environment</span></span>  
<span data-ttu-id="d19e5-411">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d19e5-411">**Type**: *string*</span></span>  
<span data-ttu-id="d19e5-412">**Výchozí**: Produkční</span><span class="sxs-lookup"><span data-stu-id="d19e5-412">**Default**: Production</span></span>  
<span data-ttu-id="d19e5-413">**Nastavit pomocí**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="d19e5-413">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="d19e5-414">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d19e5-414">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d19e5-415">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-415">The environment can be set to any value.</span></span> <span data-ttu-id="d19e5-416">Mezi hodnoty definované rozhraním patří `Development`, `Staging` a `Production`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-416">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d19e5-417">U hodnot se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="d19e5-417">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="d19e5-418">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="d19e5-418">ConfigureHostConfiguration</span></span>

<span data-ttu-id="d19e5-419"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele.</span><span class="sxs-lookup"><span data-stu-id="d19e5-419"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="d19e5-420">Konfigurace hostitele se používá k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-420">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="d19e5-421"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> se dá volat víckrát s přičítáním výsledků.</span><span class="sxs-lookup"><span data-stu-id="d19e5-421"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d19e5-422">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="d19e5-422">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d19e5-423">Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé.</span><span class="sxs-lookup"><span data-stu-id="d19e5-423">No providers are included by default.</span></span> <span data-ttu-id="d19e5-424">Musíte explicitně zadat, co vyžadují poskytovatelé konfigurace, aby aplikace <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, včetně:</span><span class="sxs-lookup"><span data-stu-id="d19e5-424">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="d19e5-425">Konfigurace souboru (například ze souboru *HostSettings. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="d19e5-425">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="d19e5-426">Konfigurace proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-426">Environment variable configuration.</span></span>
* <span data-ttu-id="d19e5-427">Konfigurace argumentů příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d19e5-427">Command-line argument configuration.</span></span>
* <span data-ttu-id="d19e5-428">Všichni další povinní poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-428">Any other required configuration providers.</span></span>

<span data-ttu-id="d19e5-429">Konfigurace souborů hostitele je povolená zadáním základní cesty aplikace s `SetBasePath` následovaným voláním jednoho z [poskytovatelů konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d19e5-429">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="d19e5-430">Ukázková aplikace používá soubor JSON *HostSettings. JSON*a volá <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> pro využívání nastavení konfigurace hostitele souboru.</span><span class="sxs-lookup"><span data-stu-id="d19e5-430">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="d19e5-431">Chcete-li přidat [konfiguraci proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na tvůrci hostitele.</span><span class="sxs-lookup"><span data-stu-id="d19e5-431">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="d19e5-432">`AddEnvironmentVariables` akceptuje volitelnou uživatelem definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-432">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="d19e5-433">Ukázková aplikace používá předponu `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-433">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="d19e5-434">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-434">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d19e5-435">Po nakonfigurování hostitele ukázkové aplikace se hodnota proměnné prostředí `PREFIX_ENVIRONMENT` stala hodnotou konfigurace hostitele pro klíč `environment`.</span><span class="sxs-lookup"><span data-stu-id="d19e5-435">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d19e5-436">Během vývoje při použití sady [Visual Studio](https://visualstudio.microsoft.com) nebo spuštění aplikace s `dotnet run` lze proměnné prostředí nastavit v souboru *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d19e5-436">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="d19e5-437">V [Visual Studio Code](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *. VSCode/Launch. JSON* během vývoje.</span><span class="sxs-lookup"><span data-stu-id="d19e5-437">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="d19e5-438">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-438">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d19e5-439">[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-439">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="d19e5-440">K povolení argumentů příkazového řádku pro přepsání konfigurace poskytované předchozími poskytovateli konfigurace se přidá poslední konfigurace příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d19e5-440">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="d19e5-441">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d19e5-441">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="d19e5-442">Další konfiguraci lze poskytnout pomocí klíčů [ApplicationName](#application-key-name) a [contentRoot](#content-root) .</span><span class="sxs-lookup"><span data-stu-id="d19e5-442">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="d19e5-443">Příklad konfigurace `HostBuilder` pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="d19e5-443">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="d19e5-444">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d19e5-444">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d19e5-445">Konfigurace aplikace se vytvoří voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> v implementaci <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-445">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="d19e5-446"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d19e5-446"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="d19e5-447"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> se dá volat víckrát s přičítáním výsledků.</span><span class="sxs-lookup"><span data-stu-id="d19e5-447"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d19e5-448">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="d19e5-448">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="d19e5-449">Konfigurace vytvořená <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-449">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="d19e5-450">Konfigurace aplikace automaticky přijímá konfiguraci hostitele, kterou poskytuje [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d19e5-450">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="d19e5-451">Příklad konfigurace aplikace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="d19e5-451">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="d19e5-452">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d19e5-452">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="d19e5-453">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="d19e5-453">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="d19e5-454">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="d19e5-454">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="d19e5-455">Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-455">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="d19e5-456">Ukázková aplikace přesune své soubory nastavení aplikace JSON a *HostSettings. JSON* s následující položkou `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-456">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="d19e5-457">Metody rozšíření konfigurace, například <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, například [Microsoft. Extensions. Configuration. JSON](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft. Extensions. Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="d19e5-457">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="d19e5-458">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), tyto balíčky musí být kromě základního balíčku [Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) přidány do projektu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-458">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="d19e5-459">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-459">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="d19e5-460">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="d19e5-460">ConfigureServices</span></span>

<span data-ttu-id="d19e5-461"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-461"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d19e5-462"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> se dá volat víckrát s přičítáním výsledků.</span><span class="sxs-lookup"><span data-stu-id="d19e5-462"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d19e5-463">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-463">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d19e5-464">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-464">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="d19e5-465">[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá metodu rozšíření `AddHostedService` k přidání služby pro události života, `LifetimeEventsHostedService` a časované úlohy na pozadí, `TimedHostedService`, do aplikace:</span><span class="sxs-lookup"><span data-stu-id="d19e5-465">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="d19e5-466">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="d19e5-466">ConfigureLogging</span></span>

<span data-ttu-id="d19e5-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> přidá delegáta pro konfiguraci poskytnuté <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="d19e5-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> se dá volat víckrát s přičítáním výsledkům.</span><span class="sxs-lookup"><span data-stu-id="d19e5-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="d19e5-469">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="d19e5-469">UseConsoleLifetime</span></span>

<span data-ttu-id="d19e5-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> naslouchá CTRL + C/SIGINT nebo SIGTERM a zavolá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pro spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="d19e5-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> odblokuje rozšíření, jako je například [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d19e5-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="d19e5-472"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> je předem registrován jako výchozí implementace životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-472"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="d19e5-473">Je použita poslední registrovaná doba života.</span><span class="sxs-lookup"><span data-stu-id="d19e5-473">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="d19e5-474">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="d19e5-474">Container configuration</span></span>

<span data-ttu-id="d19e5-475">Pro podporu zapojení do jiných kontejnerů může hostitel přijmout <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-475">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="d19e5-476">Poskytnutí továrny není součástí registrace DI Container, ale je místo toho hostitel, který se používá k vytvoření konkrétního kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="d19e5-476">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="d19e5-477">[UseServiceProviderFactory (IServiceProviderFactory @ no__t-1TContainerBuilder @ no__t-2)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepíše výchozí továrnu, která byla použita k vytvoření poskytovatele služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-477">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="d19e5-478">Vlastní konfigurace kontejneru je spravovaná metodou <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-478">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="d19e5-479"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> poskytuje silné typové prostředí pro konfiguraci kontejneru nad podkladovým rozhraním API hostitele.</span><span class="sxs-lookup"><span data-stu-id="d19e5-479"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="d19e5-480"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> se dá volat víckrát s přičítáním výsledků.</span><span class="sxs-lookup"><span data-stu-id="d19e5-480"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d19e5-481">Vytvoření kontejneru služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="d19e5-481">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="d19e5-482">Poskytněte objekt pro vytváření kontejnerů služby:</span><span class="sxs-lookup"><span data-stu-id="d19e5-482">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="d19e5-483">Použijte objekt pro vytváření a nakonfigurujte vlastní kontejner služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="d19e5-483">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="d19e5-484">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="d19e5-484">Extensibility</span></span>

<span data-ttu-id="d19e5-485">Rozšiřitelnost hostitelů se provádí s metodami rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-485">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="d19e5-486">Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje implementaci <xref:Microsoft.Extensions.Hosting.IHostBuilder> s příkladem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) , který je znázorněn v <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-486">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="d19e5-487">Aplikace vytváří metodu rozšíření `UseHostedService` k registraci hostované služby předané v `T`:</span><span class="sxs-lookup"><span data-stu-id="d19e5-487">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="d19e5-488">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="d19e5-488">Manage the host</span></span>

<span data-ttu-id="d19e5-489">Implementace <xref:Microsoft.Extensions.Hosting.IHost> zodpovídá za spouštění a zastavování implementací <xref:Microsoft.Extensions.Hosting.IHostedService>, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="d19e5-489">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d19e5-490">Spusťte</span><span class="sxs-lookup"><span data-stu-id="d19e5-490">Run</span></span>

<span data-ttu-id="d19e5-491"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý:</span><span class="sxs-lookup"><span data-stu-id="d19e5-491"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="d19e5-492">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d19e5-492">RunAsync</span></span>

<span data-ttu-id="d19e5-493"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task>, která se dokončí při aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-493"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="d19e5-494">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d19e5-494">RunConsoleAsync</span></span>

<span data-ttu-id="d19e5-495"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> povolí podporu konzoly, sestaví a spustí hostitele a počká, až se vypne CTRL + C/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="d19e5-495"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="d19e5-496">Spustit a StopAsync</span><span class="sxs-lookup"><span data-stu-id="d19e5-496">Start and StopAsync</span></span>

<span data-ttu-id="d19e5-497"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="d19e5-497"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="d19e5-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> se pokusí zastavit hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="d19e5-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="d19e5-499">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="d19e5-499">StartAsync and StopAsync</span></span>

<span data-ttu-id="d19e5-500">@no__t – 0 spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d19e5-500"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="d19e5-501"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> zastaví aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d19e5-501"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="d19e5-502">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d19e5-502">WaitForShutdown</span></span>

<span data-ttu-id="d19e5-503"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se spustí prostřednictvím <xref:Microsoft.Extensions.Hosting.IHostLifetime>, jako je například <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (naslouchá pro CTRL + C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="d19e5-503"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="d19e5-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="d19e5-505">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d19e5-505">WaitForShutdownAsync</span></span>

<span data-ttu-id="d19e5-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="d19e5-507">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="d19e5-507">External control</span></span>

<span data-ttu-id="d19e5-508">Externí kontrolu hostitele lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="d19e5-508">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="d19e5-509"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se volá na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, která čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="d19e5-509"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d19e5-510">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-510">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="d19e5-511">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="d19e5-511">IHostingEnvironment interface</span></span>

<span data-ttu-id="d19e5-512"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> poskytuje informace o hostitelském prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d19e5-512"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="d19e5-513">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>, aby bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d19e5-513">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="d19e5-514">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d19e5-514">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="d19e5-515">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d19e5-515">IApplicationLifetime interface</span></span>

<span data-ttu-id="d19e5-516"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> umožňuje aktivity po spuštění a vypnutí, včetně řádných požadavků na vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-516"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="d19e5-517">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci metod @no__t 0, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-517">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="d19e5-518">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="d19e5-518">Cancellation Token</span></span> | <span data-ttu-id="d19e5-519">Aktivováno, když&#8230;</span><span class="sxs-lookup"><span data-stu-id="d19e5-519">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="d19e5-520">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="d19e5-520">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="d19e5-521">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-521">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="d19e5-522">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="d19e5-522">All requests should be processed.</span></span> <span data-ttu-id="d19e5-523">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-523">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="d19e5-524">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-524">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="d19e5-525">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="d19e5-525">Requests may still be processing.</span></span> <span data-ttu-id="d19e5-526">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-526">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="d19e5-527">Konstruktor – vloží službu <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="d19e5-527">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="d19e5-528">[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání konstruktoru do třídy `LifetimeEventsHostedService` (implementace <xref:Microsoft.Extensions.Hosting.IHostedService>) k registraci událostí.</span><span class="sxs-lookup"><span data-stu-id="d19e5-528">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="d19e5-529">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="d19e5-529">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="d19e5-530"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> žádosti o ukončení aplikace</span><span class="sxs-lookup"><span data-stu-id="d19e5-530"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="d19e5-531">Následující třída používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> k bezproblémovému vypnutí aplikace, když je volána metoda `Shutdown` třídy:</span><span class="sxs-lookup"><span data-stu-id="d19e5-531">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="d19e5-532">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d19e5-532">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
