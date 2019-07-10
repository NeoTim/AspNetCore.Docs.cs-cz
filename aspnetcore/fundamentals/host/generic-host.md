---
title: .NET Generic Host
author: tdykstra
description: Informace o obecných hostitele .NET Core, který je zodpovědný za spouštění a životního cyklu správy aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 07/01/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: d787559eaecd6d4d6cfe01e37baf28774a90c5c3
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724424"
---
# <a name="net-generic-host"></a><span data-ttu-id="07310-103">.NET Generic Host</span><span class="sxs-lookup"><span data-stu-id="07310-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07310-104">Tento článek představuje obecné hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny o tom, jak ho použít.</span><span class="sxs-lookup"><span data-stu-id="07310-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="07310-105">Co je hostitele?</span><span class="sxs-lookup"><span data-stu-id="07310-105">What's a host?</span></span>

<span data-ttu-id="07310-106">A *hostitele* je objekt, který zapouzdřuje prostředky vaší aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="07310-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="07310-107">Injektáž závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="07310-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="07310-108">Protokolování</span><span class="sxs-lookup"><span data-stu-id="07310-108">Logging</span></span>
* <span data-ttu-id="07310-109">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="07310-109">Configuration</span></span>
* <span data-ttu-id="07310-110">`IHostedService` Implementace</span><span class="sxs-lookup"><span data-stu-id="07310-110">`IHostedService` implementations</span></span>

<span data-ttu-id="07310-111">Při spuštění hostitele, které volá `IHostedService.StartAsync` na každé provedení <xref:Microsoft.Extensions.Hosting.IHostedService> nalezenému v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="07310-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="07310-112">Ve webové aplikaci, jeden z `IHostedService` implementace je webová služba, která se spustí [implementaci serveru HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="07310-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="07310-113">Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07310-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="07310-114">Ve verzích starších než 3.0, ASP.NET Core [webového hostitele](xref:fundamentals/host/web-host) se používá pro úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="07310-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="07310-115">Webového hostitele už se nedoporučuje pro webové aplikace a zůstane k dispozici pouze z důvodu zpětné kompatibility.</span><span class="sxs-lookup"><span data-stu-id="07310-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="07310-116">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="07310-116">Set up a host</span></span>

<span data-ttu-id="07310-117">Hostitel je typicky nakonfigurován, sestavení a spuštění kódu v `Program` třídy.</span><span class="sxs-lookup"><span data-stu-id="07310-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="07310-118">`Main` Metody:</span><span class="sxs-lookup"><span data-stu-id="07310-118">The `Main` method:</span></span>

* <span data-ttu-id="07310-119">Volání `CreateHostBuilder` metody vytvoření a konfigurace objekt Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="07310-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="07310-120">Volání `Build` a `Run` metody Tvůrce objektu.</span><span class="sxs-lookup"><span data-stu-id="07310-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="07310-121">Tady je *Program.cs* kód pro úlohu jiným protokolem než HTTP pomocí jediného `IHostedService` DI kontejneru přidá implementace.</span><span class="sxs-lookup"><span data-stu-id="07310-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="07310-122">Úloh HTTP `Main` metodou je stejný ale `CreateHostBuilder` volání `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="07310-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="07310-123">Pokud aplikace využívá Entity Framework Core, neměňte název nebo podpis `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="07310-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="07310-124">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) chcete vyhledat `CreateHostBuilder` metodu, která nakonfiguruje hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="07310-125">Další informace najdete v tématu [vytváření DbContext v době návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="07310-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="07310-126">Výchozí nastavení Tvůrce</span><span class="sxs-lookup"><span data-stu-id="07310-126">Default builder settings</span></span> 

<span data-ttu-id="07310-127"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Metody:</span><span class="sxs-lookup"><span data-stu-id="07310-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="07310-128">Nastaví obsahu kořenovou cesta vrácená procedurou <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="07310-128">Sets the content root to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="07310-129">Načítání z konfigurace hostitele:</span><span class="sxs-lookup"><span data-stu-id="07310-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="07310-130">Proměnné prostředí s předponou "DOTNET_".</span><span class="sxs-lookup"><span data-stu-id="07310-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="07310-131">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="07310-131">Command-line arguments.</span></span>
* <span data-ttu-id="07310-132">Konfigurace aplikace načte z:</span><span class="sxs-lookup"><span data-stu-id="07310-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="07310-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="07310-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="07310-134">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="07310-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="07310-135">[Tajný klíč správce](xref:security/app-secrets) při spuštění aplikace `Development` prostředí.</span><span class="sxs-lookup"><span data-stu-id="07310-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="07310-136">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="07310-136">Environment variables.</span></span>
  * <span data-ttu-id="07310-137">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="07310-137">Command-line arguments.</span></span>
* <span data-ttu-id="07310-138">Přidá následující [protokolování](xref:fundamentals/logging/index) zprostředkovatelé:</span><span class="sxs-lookup"><span data-stu-id="07310-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="07310-139">Konzola</span><span class="sxs-lookup"><span data-stu-id="07310-139">Console</span></span>
  * <span data-ttu-id="07310-140">Ladění</span><span class="sxs-lookup"><span data-stu-id="07310-140">Debug</span></span>
  * <span data-ttu-id="07310-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="07310-141">EventSource</span></span>
  * <span data-ttu-id="07310-142">Protokol událostí (pouze při spuštění na Windows)</span><span class="sxs-lookup"><span data-stu-id="07310-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="07310-143">Umožňuje [oboru ověření](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) po vývojové prostředí.</span><span class="sxs-lookup"><span data-stu-id="07310-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="07310-144">`ConfigureWebHostDefaults` Metody:</span><span class="sxs-lookup"><span data-stu-id="07310-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="07310-145">Načtení hostovat konfiguraci z proměnné prostředí s předponou "ASPNETCORE_".</span><span class="sxs-lookup"><span data-stu-id="07310-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="07310-146">Nastaví [Kestrel](xref:fundamentals/servers/kestrel) serveru webového serveru a nakonfiguruje ho pomocí aplikace poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="07310-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="07310-147">Možnosti výchozí Kestrel serveru najdete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="07310-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="07310-148">Přidá [hostitele filtrování middleware](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="07310-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="07310-149">Přidá [předané záhlaví middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) Pokud ASPNETCORE_FORWARDEDHEADERS_ENABLED = true.</span><span class="sxs-lookup"><span data-stu-id="07310-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="07310-150">Umožňuje integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="07310-150">Enables IIS integration.</span></span> <span data-ttu-id="07310-151">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="07310-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="07310-152">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro službu web apps](#settings-for-web-apps) částech dále v tomto článku ukazují, jak přepsat výchozí nastavení Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="07310-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="07310-153">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="07310-153">Framework-provided services</span></span>

<span data-ttu-id="07310-154">Služby, které jsou registrovány automaticky zahrnují následující:</span><span class="sxs-lookup"><span data-stu-id="07310-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="07310-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="07310-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="07310-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="07310-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="07310-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="07310-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="07310-158">Seznam všechny služby poskytované rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="07310-158">For a list of all framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="07310-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="07310-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="07310-160">Vložit <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (dříve `IApplicationLifetime`) service do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07310-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="07310-161">Tři vlastnosti v rozhraní jsou tokeny zrušení použije k registraci aplikace počáteční a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="07310-162">Zahrnuje také rozhraní `StopApplication` metody.</span><span class="sxs-lookup"><span data-stu-id="07310-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="07310-163">Následující příklad je určený `IHostedService` implementace, která se registruje `IApplicationLifetime` události:</span><span class="sxs-lookup"><span data-stu-id="07310-163">The following example is an `IHostedService` implementation that registers the `IApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="07310-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="07310-164">IHostLifetime</span></span>

<span data-ttu-id="07310-165"><xref:Microsoft.Extensions.Hosting.IHostLifetime> Implementace řídí při spuštění hostitele a při zastavení.</span><span class="sxs-lookup"><span data-stu-id="07310-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="07310-166">Poslední implementace registrována se používá.</span><span class="sxs-lookup"><span data-stu-id="07310-166">The last implementation registered is used.</span></span>

<span data-ttu-id="07310-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> Výchozí hodnota je `IHostLifetime` implementace.</span><span class="sxs-lookup"><span data-stu-id="07310-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="07310-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="07310-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="07310-169">čeká na kombinaci kláves Ctrl + C/SIGINT nebo SIGTERM a volání <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> zahájíte proces vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07310-169">listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="07310-170">Odblokuje rozšíření, jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="07310-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="07310-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="07310-171">IHostEnvironment</span></span>

<span data-ttu-id="07310-172">Vložit <xref:Microsoft.Extensions.Hosting.IHostEnvironment> služby do třídy a získat informace o následující:</span><span class="sxs-lookup"><span data-stu-id="07310-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="07310-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="07310-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="07310-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="07310-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="07310-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="07310-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="07310-176">Webové aplikace implementovat `IWebHostEnvironment` rozhraní, která dědí `IHostEnvironment` a přidá:</span><span class="sxs-lookup"><span data-stu-id="07310-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds:</span></span>

* [<span data-ttu-id="07310-177">WebRootPath</span><span class="sxs-lookup"><span data-stu-id="07310-177">WebRootPath</span></span>](#webroot)

## <a name="host-configuration"></a><span data-ttu-id="07310-178">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="07310-178">Host configuration</span></span>

<span data-ttu-id="07310-179">Konfigurace hostitele se používá pro vlastnosti <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementace.</span><span class="sxs-lookup"><span data-stu-id="07310-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="07310-180">Konfigurace hostitele je k dispozici [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="07310-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="07310-181">Po `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` nahradí se konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="07310-182">Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> na `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="07310-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="07310-183">`ConfigureHostConfiguration` můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="07310-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="07310-184">Hostitel používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč.</span><span class="sxs-lookup"><span data-stu-id="07310-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="07310-185">Zprostředkovatel proměnné prostředí s předponou `DOTNET_` a argumenty příkazového řádku jsou zahrnuty CreateDefaultBuilder.</span><span class="sxs-lookup"><span data-stu-id="07310-185">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="07310-186">Pro webové aplikace, poskytovatel proměnné prostředí s předponou `ASPNETCORE_` je přidána.</span><span class="sxs-lookup"><span data-stu-id="07310-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="07310-187">Předpona, která se odebere, když jsou proměnné prostředí načteny.</span><span class="sxs-lookup"><span data-stu-id="07310-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="07310-188">Například, hodnotu proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` stane hodnota konfigurace hostitele `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="07310-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="07310-189">Následující příklad vytvoří konfigurace hostitele:</span><span class="sxs-lookup"><span data-stu-id="07310-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="07310-190">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="07310-190">App configuration</span></span>

<span data-ttu-id="07310-191">Konfigurace aplikace je vytvořen zavoláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="07310-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="07310-192">`ConfigureAppConfiguration` můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="07310-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="07310-193">Aplikace používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč.</span><span class="sxs-lookup"><span data-stu-id="07310-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="07310-194">Konfigurace vytvořil `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a as a service od DI.</span><span class="sxs-lookup"><span data-stu-id="07310-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="07310-195">Konfigurace hostitele je taky přidaný ke konfiguraci aplikací.</span><span class="sxs-lookup"><span data-stu-id="07310-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="07310-196">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="07310-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="07310-197">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="07310-197">Settings for all app types</span></span>

<span data-ttu-id="07310-198">Tato část obsahuje seznam nastavení hostitele, které se vztahují na HTTP a jiným protokolem než HTTP úlohy.</span><span class="sxs-lookup"><span data-stu-id="07310-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="07310-199">Ve výchozím nastavení, můžete použít ke konfiguraci těchto nastavení proměnné prostředí mají `DOTNET_` nebo `ASPNETCORE_` předponu.</span><span class="sxs-lookup"><span data-stu-id="07310-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

### <a name="applicationname"></a><span data-ttu-id="07310-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="07310-200">ApplicationName</span></span>

<span data-ttu-id="07310-201">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) vlastnost nastaven z konfigurace hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="07310-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="07310-202">**Klíč**: applicationName</span><span class="sxs-lookup"><span data-stu-id="07310-202">**Key**: applicationName</span></span>  
<span data-ttu-id="07310-203">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="07310-203">**Type**: *string*</span></span>  
<span data-ttu-id="07310-204">**Výchozí**: Název sestavení, který obsahuje vstupní aplikace bodu.</span><span class="sxs-lookup"><span data-stu-id="07310-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="07310-205">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="07310-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="07310-206">Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="07310-206">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="07310-207">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="07310-207">ContentRootPath</span></span>

<span data-ttu-id="07310-208">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) vlastnost určuje, kde začíná hostitele vyhledávání obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="07310-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="07310-209">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="07310-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="07310-210">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="07310-210">**Key**: contentRoot</span></span>  
<span data-ttu-id="07310-211">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="07310-211">**Type**: *string*</span></span>  
<span data-ttu-id="07310-212">**Výchozí**: Složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="07310-213">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="07310-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="07310-214">Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseContentRoot` na `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="07310-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

### <a name="environmentname"></a><span data-ttu-id="07310-215">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="07310-215">EnvironmentName</span></span>

<span data-ttu-id="07310-216">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) vlastnost lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="07310-216">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="07310-217">Hodnoty definované v rámci rozhraní zahrnují `Development`, `Staging`, a `Production`.</span><span class="sxs-lookup"><span data-stu-id="07310-217">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="07310-218">Hodnoty se velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="07310-218">Values aren't case sensitive.</span></span>

<span data-ttu-id="07310-219">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="07310-219">**Key**: environment</span></span>  
<span data-ttu-id="07310-220">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="07310-220">**Type**: *string*</span></span>  
<span data-ttu-id="07310-221">**Výchozí**: Produkční</span><span class="sxs-lookup"><span data-stu-id="07310-221">**Default**: Production</span></span>  
<span data-ttu-id="07310-222">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="07310-222">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="07310-223">Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseEnvironment` na `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="07310-223">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="07310-224">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="07310-224">ShutdownTimeout</span></span>

<span data-ttu-id="07310-225">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="07310-225">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="07310-226">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="07310-226">The default value is five seconds.</span></span>  <span data-ttu-id="07310-227">Během období časového limitu hostitele:</span><span class="sxs-lookup"><span data-stu-id="07310-227">During the timeout period, the host:</span></span>

* <span data-ttu-id="07310-228">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="07310-228">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="07310-229">Limitu pokusí zastavit hostovaných služeb, protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="07310-229">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="07310-230">Pokud časový limit vyprší před všechny zarážky hostovaných služeb, zastaví se všechny zbývající služby active při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-230">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="07310-231">Zastavení služeb i v případě, že se nedokončilo zpracování.</span><span class="sxs-lookup"><span data-stu-id="07310-231">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="07310-232">Pokud služby vyžadují další čas ukončení, zvýšit časový limit.</span><span class="sxs-lookup"><span data-stu-id="07310-232">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="07310-233">**Klíč**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="07310-233">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="07310-234">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="07310-234">**Type**: *int*</span></span>  
<span data-ttu-id="07310-235">**Výchozí**: 5 sekund **proměnnou prostředí**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="07310-235">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="07310-236">Tuto hodnotu nastavit pomocí proměnné prostředí nebo konfigurace `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="07310-236">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="07310-237">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="07310-237">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="07310-238">Nastavení pro webové aplikace</span><span class="sxs-lookup"><span data-stu-id="07310-238">Settings for web apps</span></span>

<span data-ttu-id="07310-239">Některá nastavení hostitele platí pouze pro úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="07310-239">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="07310-240">Ve výchozím nastavení, můžete použít ke konfiguraci těchto nastavení proměnné prostředí mají `DOTNET_` nebo `ASPNETCORE_` předponu.</span><span class="sxs-lookup"><span data-stu-id="07310-240">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="07310-241">Rozšiřující metody na `IWebHostBuilder` jsou dostupné pro tato nastavení.</span><span class="sxs-lookup"><span data-stu-id="07310-241">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="07310-242">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` je instance `IWebHostBuilder`, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="07310-242">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="07310-243">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="07310-243">CaptureStartupErrors</span></span>

<span data-ttu-id="07310-244">Když `false`, chyby během spuštění výsledku na hostiteli. operace bude ukončena.</span><span class="sxs-lookup"><span data-stu-id="07310-244">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="07310-245">Když `true`, zachytí výjimky během spouštění hostitele a se pokusí spustit na serveru.</span><span class="sxs-lookup"><span data-stu-id="07310-245">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="07310-246">**Klíč**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="07310-246">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="07310-247">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="07310-247">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="07310-248">**Výchozí**: Výchozí hodnota je `false` Pokud aplikace běží s Kestrel za služby IIS, kde je jako výchozí `true`.</span><span class="sxs-lookup"><span data-stu-id="07310-248">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="07310-249">**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="07310-249">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="07310-250">Nastavit tuto hodnotu použít konfigurace nebo volání `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="07310-250">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="07310-251">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="07310-251">DetailedErrors</span></span>

<span data-ttu-id="07310-252">Když povolena, nebo když je prostředí `Development`, aplikace zaznamenává podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="07310-252">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="07310-253">**Klíč**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="07310-253">**Key**: detailedErrors</span></span>  
<span data-ttu-id="07310-254">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="07310-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="07310-255">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="07310-255">**Default**: false</span></span>  
<span data-ttu-id="07310-256">**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="07310-256">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="07310-257">Nastavit tuto hodnotu použít konfigurace nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="07310-257">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="07310-258">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="07310-258">HostingStartupAssemblies</span></span>

<span data-ttu-id="07310-259">Středníkem oddělený řetězec hostování načíst při spuštění po spuštění sestavení.</span><span class="sxs-lookup"><span data-stu-id="07310-259">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="07310-260">I když výchozí hodnota konfigurace je prázdný řetězec, hostování při spuštění sestavení vždy zahrnovat sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-260">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="07310-261">Při hostování při spuštění sestavení jsou k dispozici, se přidají do sestavení aplikace pro načtení, když aplikace je sestavena své běžné služby během spouštění.</span><span class="sxs-lookup"><span data-stu-id="07310-261">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="07310-262">**Klíč**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="07310-262">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="07310-263">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="07310-263">**Type**: *string*</span></span>  
<span data-ttu-id="07310-264">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="07310-264">**Default**: Empty string</span></span>  
<span data-ttu-id="07310-265">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="07310-265">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="07310-266">Nastavit tuto hodnotu použít konfigurace nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="07310-266">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="07310-267">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="07310-267">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="07310-268">Řetězec oddělený středníkem při spuštění sestavení mají vyloučit při spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="07310-268">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="07310-269">**Klíč**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="07310-269">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="07310-270">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="07310-270">**Type**: *string*</span></span>  
<span data-ttu-id="07310-271">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="07310-271">**Default**: Empty string</span></span>  
<span data-ttu-id="07310-272">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="07310-272">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="07310-273">Nastavit tuto hodnotu použít konfigurace nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="07310-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="httpsport"></a><span data-ttu-id="07310-274">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="07310-274">HTTPS_Port</span></span>

<span data-ttu-id="07310-275">HTTPS přesměrování portu.</span><span class="sxs-lookup"><span data-stu-id="07310-275">The HTTPS redirect port.</span></span> <span data-ttu-id="07310-276">Použít v [vynucování HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="07310-276">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="07310-277">**Klíč**: https_port **typ**: *řetězec*
**výchozí**: Výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="07310-277">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="07310-278">**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="07310-278">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="07310-279">Nastavit tuto hodnotu použít konfigurace nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="07310-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="07310-280">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="07310-280">PreferHostingUrls</span></span>

<span data-ttu-id="07310-281">Určuje, zda hostitel naslouchat požadavkům na adresy URL nakonfigurované `IWebHostBuilder` místo nastavení nakonfigurovaného pomocí `IServer` implementace.</span><span class="sxs-lookup"><span data-stu-id="07310-281">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="07310-282">**Klíč**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="07310-282">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="07310-283">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="07310-283">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="07310-284">**Výchozí**: true</span><span class="sxs-lookup"><span data-stu-id="07310-284">**Default**: true</span></span>  
<span data-ttu-id="07310-285">**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="07310-285">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="07310-286">Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="07310-286">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="07310-287">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="07310-287">PreventHostingStartup</span></span>

<span data-ttu-id="07310-288">Brání automatické načítání hostování při spuštění sestavení, včetně hostování při spuštění sestavení nakonfiguroval sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-288">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="07310-289">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="07310-289">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="07310-290">**Klíč**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="07310-290">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="07310-291">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="07310-291">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="07310-292">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="07310-292">**Default**: false</span></span>  
<span data-ttu-id="07310-293">**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="07310-293">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="07310-294">Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="07310-294">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="07310-295">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="07310-295">StartupAssembly</span></span>

<span data-ttu-id="07310-296">Sestavení pro hledání `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="07310-296">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="07310-297">**Klíč**: startupAssembly **typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="07310-297">**Key**: startupAssembly **Type**: *string*</span></span>  
<span data-ttu-id="07310-298">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="07310-298">**Default**: The app's assembly</span></span>  
<span data-ttu-id="07310-299">**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="07310-299">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="07310-300">Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="07310-300">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="07310-301">`UseStartup` může trvat název sestavení (`string`) nebo typu (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="07310-301">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="07310-302">Pokud je položek víc `UseStartup` metody jsou volány, má přednost před poslední z nich.</span><span class="sxs-lookup"><span data-stu-id="07310-302">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="07310-303">URL – adresy</span><span class="sxs-lookup"><span data-stu-id="07310-303">URLs</span></span>

<span data-ttu-id="07310-304">Středníkem oddělený seznam IP adres nebo adres hostitele s porty a protokoly, které server naslouchat požadavkům na požadavky.</span><span class="sxs-lookup"><span data-stu-id="07310-304">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="07310-305">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="07310-305">For example, `http://localhost:123`.</span></span> <span data-ttu-id="07310-306">Použití "\*" k označení, že by měl server naslouchat požadavkům na IP adresu nebo název hostitele zadaný port a protokol (například `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="07310-306">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="07310-307">Protokol (`http://` nebo `https://`) musí být součástí jednotlivé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="07310-307">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="07310-308">Podporované formáty lišit mezi servery.</span><span class="sxs-lookup"><span data-stu-id="07310-308">Supported formats vary among servers.</span></span>

<span data-ttu-id="07310-309">**Klíč**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="07310-309">**Key**: urls</span></span>  
<span data-ttu-id="07310-310">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="07310-310">**Type**: *string*</span></span>  
<span data-ttu-id="07310-311">**Výchozí**: `http://localhost:5000` a `https://localhost:5001` 
 **proměnnou prostředí**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="07310-311">**Default**: `http://localhost:5000` and `https://localhost:5001`
**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="07310-312">Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="07310-312">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="07310-313">Kestrel má svůj vlastní konfigurace koncového bodu rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="07310-313">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="07310-314">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="07310-314">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="07310-315">WebRoot</span><span class="sxs-lookup"><span data-stu-id="07310-315">WebRoot</span></span>

<span data-ttu-id="07310-316">Relativní cesta k statických prostředků aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-316">The relative path to the app's static assets.</span></span>

<span data-ttu-id="07310-317">**Klíč**: webroot</span><span class="sxs-lookup"><span data-stu-id="07310-317">**Key**: webroot</span></span>  
<span data-ttu-id="07310-318">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="07310-318">**Type**: *string*</span></span>  
<span data-ttu-id="07310-319">**Výchozí**: *(Obsah kořenové) / wwwroot*, pokud cesta existuje.</span><span class="sxs-lookup"><span data-stu-id="07310-319">**Default**: *(Content Root)/wwwroot*, if the path exists.</span></span> <span data-ttu-id="07310-320">Pokud cesta neexistuje, no-op soubor zprostředkovatele se používá.</span><span class="sxs-lookup"><span data-stu-id="07310-320">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="07310-321">**Proměnná prostředí**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="07310-321">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="07310-322">Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseWebRoot`:</span><span class="sxs-lookup"><span data-stu-id="07310-322">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="07310-323">Správa životního cyklu hostitelů</span><span class="sxs-lookup"><span data-stu-id="07310-323">Manage the host lifetime</span></span>

<span data-ttu-id="07310-324">Volání metod na vestavěný <xref:Microsoft.Extensions.Hosting.IHost> implementace spuštění a zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-324">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="07310-325">Tyto metody ovlivňují všechny <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="07310-325">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="07310-326">Spustit</span><span class="sxs-lookup"><span data-stu-id="07310-326">Run</span></span>

<span data-ttu-id="07310-327"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spuštění aplikace a blokuje volající vlákno, dokud nebude ukončen hostitele.</span><span class="sxs-lookup"><span data-stu-id="07310-327"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="07310-328">RunAsync</span><span class="sxs-lookup"><span data-stu-id="07310-328">RunAsync</span></span>

<span data-ttu-id="07310-329"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spuštění aplikace a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07310-329"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="07310-330">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="07310-330">RunConsoleAsync</span></span>

<span data-ttu-id="07310-331"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestaví a spustí hostitele a čeká na kombinaci kláves Ctrl + C/SIGINT nebo SIGTERM vypnout.</span><span class="sxs-lookup"><span data-stu-id="07310-331"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="07310-332">Spustit</span><span class="sxs-lookup"><span data-stu-id="07310-332">Start</span></span>

<span data-ttu-id="07310-333"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Hostitel spustí synchronně.</span><span class="sxs-lookup"><span data-stu-id="07310-333"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="07310-334">StartAsync</span><span class="sxs-lookup"><span data-stu-id="07310-334">StartAsync</span></span>

<span data-ttu-id="07310-335"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> Spustí hostitele a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07310-335"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="07310-336"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> volá se na začátku `StartAsync`, která čeká, dokud neskončí než budete pokračovat.</span><span class="sxs-lookup"><span data-stu-id="07310-336"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="07310-337">To umožňuje zpoždění spuštění, dokud signalizován externí událostí.</span><span class="sxs-lookup"><span data-stu-id="07310-337">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="07310-338">StopAsync</span><span class="sxs-lookup"><span data-stu-id="07310-338">StopAsync</span></span>

<span data-ttu-id="07310-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusí se zastavit hostitele v rámci zadaného časového limitu.</span><span class="sxs-lookup"><span data-stu-id="07310-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="07310-340">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="07310-340">WaitForShutdown</span></span>

<span data-ttu-id="07310-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blokuje volající vlákno, dokud se vypnutí se aktivuje IHostLifetime, například prostřednictvím kombinace kláves Ctrl + C/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="07310-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="07310-342">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="07310-342">WaitForShutdownAsync</span></span>

<span data-ttu-id="07310-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při vypnutí se spouští přes daný token a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="07310-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="07310-344">Vnější ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="07310-344">External control</span></span>

<span data-ttu-id="07310-345">Přímé řízení životního cyklu hostitelů lze dosáhnout pomocí metody, které je možné volat externě:</span><span class="sxs-lookup"><span data-stu-id="07310-345">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="07310-346">Aplikace ASP.NET Core, konfigurace a spouštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="07310-346">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="07310-347">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="07310-347">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="07310-348">Tento článek popisuje obecný hostitele ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), který se používá pro aplikace, které není zpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="07310-348">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="07310-349">Účelem obecný hostitele je oddělit kanálu HTTP z hostitele webového rozhraní API umožňující širší škálu scénářů hostitele.</span><span class="sxs-lookup"><span data-stu-id="07310-349">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="07310-350">Zasílání zpráv, úlohy na pozadí a další úlohy jiným protokolem než HTTP podle obecného hostitele je výhodné společné funkce, jako jsou konfigurace, injektáž závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="07310-350">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="07310-351">Obecný hostitele je nového v ASP.NET Core 2.1 a není vhodné pro scénáře hostování webů.</span><span class="sxs-lookup"><span data-stu-id="07310-351">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="07310-352">Pro web scénářích hostování, použijte [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="07310-352">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="07310-353">Obecný hostitele, nahradí webového hostitele v budoucí verzi a fungovat jako primární hostitele rozhraní API scénáře jiným protokolem než HTTP a protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="07310-353">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="07310-354">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07310-354">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="07310-355">Při spuštění ukázkové aplikace [Visual Studio Code](https://code.visualstudio.com/), použijte *externí nebo integrovaného terminálu*.</span><span class="sxs-lookup"><span data-stu-id="07310-355">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="07310-356">Nejdou spustit v ukázce `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="07310-356">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="07310-357">Nastavení konzoly ve Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="07310-357">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="07310-358">Otevřít *.vscode/launch.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="07310-358">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="07310-359">V **.NET Core spuštění (konzola)** konfigurace, vyhledejte **konzoly** položka.</span><span class="sxs-lookup"><span data-stu-id="07310-359">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="07310-360">Nastavte hodnotu na buď `externalTerminal` nebo `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="07310-360">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="07310-361">Úvod</span><span class="sxs-lookup"><span data-stu-id="07310-361">Introduction</span></span>

<span data-ttu-id="07310-362">Je k dispozici v knihovně obecný hostitele <xref:Microsoft.Extensions.Hosting> obor názvů a poskytuje [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="07310-362">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="07310-363">`Microsoft.Extensions.Hosting` Je součástí balíčku [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="07310-363">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="07310-364"><xref:Microsoft.Extensions.Hosting.IHostedService> je vstupním bodem k provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="07310-364"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="07310-365">Každý <xref:Microsoft.Extensions.Hosting.IHostedService> implementace provádí v pořadí podle [službu registrace v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="07310-365">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="07310-366"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> je volána v každé <xref:Microsoft.Extensions.Hosting.IHostedService> při spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> je volána v pořadí reverzní registrace při řádné vypnutí hostitele.</span><span class="sxs-lookup"><span data-stu-id="07310-366"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="07310-367">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="07310-367">Set up a host</span></span>

<span data-ttu-id="07310-368"><xref:Microsoft.Extensions.Hosting.IHostBuilder> je hlavní komponenty, knihovny a aplikace použít k inicializaci, vytvoření a spuštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="07310-368"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="07310-369">Možnosti</span><span class="sxs-lookup"><span data-stu-id="07310-369">Options</span></span>

<span data-ttu-id="07310-370"><xref:Microsoft.Extensions.Hosting.HostOptions> Konfigurace možností <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="07310-370"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="07310-371">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="07310-371">Shutdown timeout</span></span>

<span data-ttu-id="07310-372"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> Nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="07310-372"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="07310-373">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="07310-373">The default value is five seconds.</span></span>

<span data-ttu-id="07310-374">Následující možnost konfigurace v `Program.Main` zvyšuje výchozí pět druhý vypnutí časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="07310-374">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="07310-375">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="07310-375">Default services</span></span>

<span data-ttu-id="07310-376">Během inicializace hostitele jsou registrovány následující služby:</span><span class="sxs-lookup"><span data-stu-id="07310-376">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="07310-377">[Prostředí](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="07310-377">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="07310-378">[Konfigurace](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="07310-378">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="07310-379"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="07310-379"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="07310-380"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="07310-380"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="07310-381">[Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="07310-381">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="07310-382">[Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="07310-382">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="07310-383">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="07310-383">Host configuration</span></span>

<span data-ttu-id="07310-384">Konfigurace hostitele bylo vytvořeno.</span><span class="sxs-lookup"><span data-stu-id="07310-384">Host configuration is created by:</span></span>

* <span data-ttu-id="07310-385">Volání metody rozšíření u <xref:Microsoft.Extensions.Hosting.IHostBuilder> nastavit [obsahu kořenové](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="07310-385">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="07310-386">Čtení konfigurace od poskytovatelů konfigurace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="07310-386">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="07310-387">Rozšiřující metody</span><span class="sxs-lookup"><span data-stu-id="07310-387">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="07310-388">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="07310-388">Application key (name)</span></span>

<span data-ttu-id="07310-389">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) vlastnost nastaven z konfigurace hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="07310-389">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="07310-390">Pokud chcete explicitně nastavit hodnotu, použijte [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="07310-390">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="07310-391">**Klíč**: applicationName</span><span class="sxs-lookup"><span data-stu-id="07310-391">**Key**: applicationName</span></span>  
<span data-ttu-id="07310-392">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="07310-392">**Type**: *string*</span></span>  
<span data-ttu-id="07310-393">**Výchozí**: Název sestavení obsahujícího položku aplikaci přejděte.</span><span class="sxs-lookup"><span data-stu-id="07310-393">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="07310-394">**Sada s použitím**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="07310-394">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="07310-395">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="07310-395">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="07310-396">Kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="07310-396">Content root</span></span>

<span data-ttu-id="07310-397">Toto nastavení určuje, kde začíná hostitele vyhledávání obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="07310-397">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="07310-398">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="07310-398">**Key**: contentRoot</span></span>  
<span data-ttu-id="07310-399">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="07310-399">**Type**: *string*</span></span>  
<span data-ttu-id="07310-400">**Výchozí**: Výchozí hodnota je do složky, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-400">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="07310-401">**Sada s použitím**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="07310-401">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="07310-402">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="07310-402">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="07310-403">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="07310-403">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a><span data-ttu-id="07310-404">Prostředí</span><span class="sxs-lookup"><span data-stu-id="07310-404">Environment</span></span>

<span data-ttu-id="07310-405">Nastaví aplikace [prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="07310-405">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="07310-406">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="07310-406">**Key**: environment</span></span>  
<span data-ttu-id="07310-407">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="07310-407">**Type**: *string*</span></span>  
<span data-ttu-id="07310-408">**Výchozí**: Produkční</span><span class="sxs-lookup"><span data-stu-id="07310-408">**Default**: Production</span></span>  
<span data-ttu-id="07310-409">**Sada s použitím**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="07310-409">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="07310-410">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="07310-410">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="07310-411">Prostředí můžete nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="07310-411">The environment can be set to any value.</span></span> <span data-ttu-id="07310-412">Hodnoty definované v rámci rozhraní zahrnují `Development`, `Staging`, a `Production`.</span><span class="sxs-lookup"><span data-stu-id="07310-412">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="07310-413">Hodnoty se velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="07310-413">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="07310-414">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="07310-414">ConfigureHostConfiguration</span></span>

<span data-ttu-id="07310-415"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele.</span><span class="sxs-lookup"><span data-stu-id="07310-415"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="07310-416">Konfigurace hostitele slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-416">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="07310-417"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="07310-417"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="07310-418">Hostitel používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč.</span><span class="sxs-lookup"><span data-stu-id="07310-418">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="07310-419">Žádní poskytovatelé jsou zahrnuté ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="07310-419">No providers are included by default.</span></span> <span data-ttu-id="07310-420">Je nutné explicitně zadat jakýkoli poskytovatelé konfigurace aplikace vyžaduje v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, včetně:</span><span class="sxs-lookup"><span data-stu-id="07310-420">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="07310-421">Konfigurace rozhraní File (např. z *hostsettings.json* souboru).</span><span class="sxs-lookup"><span data-stu-id="07310-421">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="07310-422">Konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="07310-422">Environment variable configuration.</span></span>
* <span data-ttu-id="07310-423">Konfigurace argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="07310-423">Command-line argument configuration.</span></span>
* <span data-ttu-id="07310-424">Žádné další požadované konfigurace poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="07310-424">Any other required configuration providers.</span></span>

<span data-ttu-id="07310-425">Je povolená souboru konfigurace hostitele tak, že zadáte základní cesty aplikace s `SetBasePath` následovanou voláním do jednoho z [souboru poskytovatelé konfigurace](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="07310-425">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="07310-426">Ukázková aplikace používá soubor JSON, *hostsettings.json*a volání <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> využívat v souboru nastavení konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="07310-426">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="07310-427">Chcete-li přidat [konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="07310-427">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="07310-428">`AddEnvironmentVariables` přijímá volitelný uživatelsky definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="07310-428">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="07310-429">Ukázková aplikace používá předponou `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="07310-429">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="07310-430">Předpona, která se odebere, když jsou proměnné prostředí načteny.</span><span class="sxs-lookup"><span data-stu-id="07310-430">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="07310-431">Když je ukázková aplikace hostitel nakonfigurovaný, hodnotu proměnné prostředí pro `PREFIX_ENVIRONMENT` stane hodnota konfigurace hostitele `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="07310-431">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="07310-432">Během vývoje. při použití [sady Visual Studio](https://visualstudio.microsoft.com) nebo spuštěním aplikace s `dotnet run`, lze nastavit proměnné prostředí *Properties/launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="07310-432">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="07310-433">V [Visual Studio Code](https://code.visualstudio.com/), lze nastavit proměnné prostředí *.vscode/launch.json* souboru během vývoje.</span><span class="sxs-lookup"><span data-stu-id="07310-433">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="07310-434">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="07310-434">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="07310-435">[Příkazový řádek konfigurace](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="07310-435">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="07310-436">Konfigurace příkazového řádku je tak, aby povolovala argumenty příkazového řádku k přepsání konfigurace poskytované starší poskytovatelé konfigurace přidáni jako poslední.</span><span class="sxs-lookup"><span data-stu-id="07310-436">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="07310-437">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="07310-437">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="07310-438">Je možné poskytnout další konfigurace [applicationName](#application-key-name) a [contentRoot](#content-root) klíče.</span><span class="sxs-lookup"><span data-stu-id="07310-438">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="07310-439">Příklad `HostBuilder` konfiguraci pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="07310-439">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="07310-440">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="07310-440">ConfigureAppConfiguration</span></span>

<span data-ttu-id="07310-441">Konfigurace aplikace je vytvořen zavoláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementace.</span><span class="sxs-lookup"><span data-stu-id="07310-441">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="07310-442"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="07310-442"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="07310-443"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="07310-443"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="07310-444">Aplikace používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč.</span><span class="sxs-lookup"><span data-stu-id="07310-444">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="07310-445">Konfigurace vytvořil <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="07310-445">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="07310-446">Konfigurace aplikace automaticky obdrží konfigurace hostitele poskytované [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="07310-446">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="07310-447">Příklad aplikace konfigurace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="07310-447">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="07310-448">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="07310-448">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="07310-449">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="07310-449">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="07310-450">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="07310-450">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="07310-451">Přesunutí souborů nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu nástroje MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="07310-451">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="07310-452">Ukázková aplikace přesune své soubory JSON aplikace nastavení a *hostsettings.json* následujícím `<Content>` položky:</span><span class="sxs-lookup"><span data-stu-id="07310-452">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="07310-453">Metody rozšíření konfigurace, jako například <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, jako například [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="07310-453">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="07310-454">Pokud aplikace používá [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), tyto balíčky musí být přidán do projektu kromě základní [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) balíčku.</span><span class="sxs-lookup"><span data-stu-id="07310-454">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="07310-455">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="07310-455">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="07310-456">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="07310-456">ConfigureServices</span></span>

<span data-ttu-id="07310-457"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> Přidá do aplikace služeb [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="07310-457"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="07310-458"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="07310-458"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="07310-459">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="07310-459">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="07310-460">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="07310-460">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="07310-461">[Ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá `AddHostedService` metodu rozšíření k přidání služby pro události doby života `LifetimeEventsHostedService`a úlohu na pozadí vypršel časový limit `TimedHostedService`, do aplikace:</span><span class="sxs-lookup"><span data-stu-id="07310-461">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="07310-462">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="07310-462">ConfigureLogging</span></span>

<span data-ttu-id="07310-463"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> přidá delegáta pro konfiguraci zadaných <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="07310-463"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="07310-464"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> může být volána více než jednou s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="07310-464"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="07310-465">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="07310-465">UseConsoleLifetime</span></span>

<span data-ttu-id="07310-466"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> čeká na kombinaci kláves Ctrl + C/SIGINT nebo SIGTERM a volání <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> zahájíte proces vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07310-466"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="07310-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> odblokuje rozšíření, jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="07310-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="07310-468"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> je zaregistrované předem. jako výchozí implementace životnost.</span><span class="sxs-lookup"><span data-stu-id="07310-468"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="07310-469">Poslední doba života zaregistrovaný se používá.</span><span class="sxs-lookup"><span data-stu-id="07310-469">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="07310-470">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="07310-470">Container configuration</span></span>

<span data-ttu-id="07310-471">Pro podporu připojení v ostatních kontejnerech, může přijmout hostitele <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="07310-471">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="07310-472">Poskytuje objekt pro vytváření, které nejsou součástí registrace kontejnerů DI, ale místo toho je vnitřní hostitel používá k vytvoření kontejneru pro konkrétní DI.</span><span class="sxs-lookup"><span data-stu-id="07310-472">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="07310-473">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí objekt pro vytváření použitý k vytvoření poskytovatele služby app service.</span><span class="sxs-lookup"><span data-stu-id="07310-473">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="07310-474">Konfigurace vlastního kontejneru je spravován <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metody.</span><span class="sxs-lookup"><span data-stu-id="07310-474">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="07310-475"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> poskytuje možnosti silného typu pro konfiguraci kontejneru nad základního hostitele rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="07310-475"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="07310-476"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="07310-476"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="07310-477">Vytvoření služby kontejneru pro aplikace:</span><span class="sxs-lookup"><span data-stu-id="07310-477">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="07310-478">Poskytuje objekt pro vytváření služby kontejneru:</span><span class="sxs-lookup"><span data-stu-id="07310-478">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="07310-479">Použijte objekt pro vytváření a konfigurace kontejneru vlastní služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="07310-479">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="07310-480">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="07310-480">Extensibility</span></span>

<span data-ttu-id="07310-481">Rozšiřitelnost hostitele se provádí pomocí metody rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="07310-481">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="07310-482">Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementaci [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) příklad jsme vám ukázali v <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="07310-482">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="07310-483">Vytvoří aplikaci `UseHostedService` metodu rozšíření k registraci hostované služby předaný `T`:</span><span class="sxs-lookup"><span data-stu-id="07310-483">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="07310-484">Spravovat hostitele</span><span class="sxs-lookup"><span data-stu-id="07310-484">Manage the host</span></span>

<span data-ttu-id="07310-485"><xref:Microsoft.Extensions.Hosting.IHost> Implementace je zodpovědný za spouštění a zastavování <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="07310-485">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="07310-486">Spustit</span><span class="sxs-lookup"><span data-stu-id="07310-486">Run</span></span>

<span data-ttu-id="07310-487"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spuštění aplikace a blokuje volající vlákno, dokud nebude ukončen hostitele:</span><span class="sxs-lookup"><span data-stu-id="07310-487"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="07310-488">RunAsync</span><span class="sxs-lookup"><span data-stu-id="07310-488">RunAsync</span></span>

<span data-ttu-id="07310-489"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spuštění aplikace a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí, když se aktivuje token zrušení nebo vypnutí:</span><span class="sxs-lookup"><span data-stu-id="07310-489"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="07310-490">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="07310-490">RunConsoleAsync</span></span>

<span data-ttu-id="07310-491"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestaví a spustí hostitele a čeká na kombinaci kláves Ctrl + C/SIGINT nebo SIGTERM vypnout.</span><span class="sxs-lookup"><span data-stu-id="07310-491"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="07310-492">Počáteční a StopAsync</span><span class="sxs-lookup"><span data-stu-id="07310-492">Start and StopAsync</span></span>

<span data-ttu-id="07310-493"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Hostitel spustí synchronně.</span><span class="sxs-lookup"><span data-stu-id="07310-493"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="07310-494"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusí se zastavit hostitele v rámci zadaného časového limitu.</span><span class="sxs-lookup"><span data-stu-id="07310-494"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="07310-495">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="07310-495">StartAsync and StopAsync</span></span>

<span data-ttu-id="07310-496"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-496"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="07310-497"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> Ukončí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="07310-497"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="07310-498">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="07310-498">WaitForShutdown</span></span>

<span data-ttu-id="07310-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se spouští přes <xref:Microsoft.Extensions.Hosting.IHostLifetime>, jako například <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (čeká na kombinaci kláves Ctrl + C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="07310-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="07310-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="07310-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="07310-501">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="07310-501">WaitForShutdownAsync</span></span>

<span data-ttu-id="07310-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při vypnutí se spouští přes daný token a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="07310-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="07310-503">Vnější ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="07310-503">External control</span></span>

<span data-ttu-id="07310-504">Vnější ovládací prvek hostitele lze dosáhnout pomocí metody, které je možné volat externě:</span><span class="sxs-lookup"><span data-stu-id="07310-504">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="07310-505"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> volá se na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, která čeká, dokud neskončí než budete pokračovat.</span><span class="sxs-lookup"><span data-stu-id="07310-505"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="07310-506">To umožňuje zpoždění spuštění, dokud signalizován externí událostí.</span><span class="sxs-lookup"><span data-stu-id="07310-506">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="07310-507">IHostingEnvironment rozhraní</span><span class="sxs-lookup"><span data-stu-id="07310-507">IHostingEnvironment interface</span></span>

<span data-ttu-id="07310-508"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> poskytuje informace o aplikaci prvku hostitelské prostředí.</span><span class="sxs-lookup"><span data-stu-id="07310-508"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="07310-509">Použít [konstruktor vkládání](xref:fundamentals/dependency-injection) získat <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> Chcete-li použít její vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="07310-509">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="07310-510">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="07310-510">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="07310-511">IApplicationLifetime rozhraní</span><span class="sxs-lookup"><span data-stu-id="07310-511">IApplicationLifetime interface</span></span>

<span data-ttu-id="07310-512"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> umožňuje po spuštění a vypnutí aktivity, včetně žádostí o řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07310-512"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="07310-513">Tři vlastnosti v rozhraní jsou tokeny zrušení použije k registraci <xref:System.Action> metody, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07310-513">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="07310-514">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="07310-514">Cancellation Token</span></span> | <span data-ttu-id="07310-515">Při aktivaci&#8230;</span><span class="sxs-lookup"><span data-stu-id="07310-515">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="07310-516">Hostitel plně byla spuštěna.</span><span class="sxs-lookup"><span data-stu-id="07310-516">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="07310-517">Hostitel se dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07310-517">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="07310-518">By měl zpracovat všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="07310-518">All requests should be processed.</span></span> <span data-ttu-id="07310-519">Vypnutí blokuje, dokud se tato událost se dokončí.</span><span class="sxs-lookup"><span data-stu-id="07310-519">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="07310-520">Hostitel provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07310-520">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="07310-521">Žádosti se možná ještě zpracovávají.</span><span class="sxs-lookup"><span data-stu-id="07310-521">Requests may still be processing.</span></span> <span data-ttu-id="07310-522">Vypnutí blokuje, dokud se tato událost se dokončí.</span><span class="sxs-lookup"><span data-stu-id="07310-522">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="07310-523">Vložit konstruktoru <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="07310-523">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="07310-524">[Ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá konstruktor injektáž do `LifetimeEventsHostedService` třídy ( <xref:Microsoft.Extensions.Hosting.IHostedService> implementace) k registraci události.</span><span class="sxs-lookup"><span data-stu-id="07310-524">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="07310-525">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="07310-525">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="07310-526"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> požádá o ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="07310-526"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="07310-527">Následující třídy používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> řádně vypnout aplikaci při třídy `Shutdown` volání metody:</span><span class="sxs-lookup"><span data-stu-id="07310-527">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="07310-528">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="07310-528">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
