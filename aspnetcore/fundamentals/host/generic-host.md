---
title: Obecný hostitel .NET
author: rick-anderson
description: Seznamte se s obecným hostitelem .NET Core, který je zodpovědný za spouštění a správu životního cyklu aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/02/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 6a0ef02db883db3bc91722786cd042ccec092735
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659920"
---
# <a name="net-generic-host"></a><span data-ttu-id="78e92-103">Obecný hostitel .NET</span><span class="sxs-lookup"><span data-stu-id="78e92-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78e92-104">Tento článek představuje obecného hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny k jeho použití.</span><span class="sxs-lookup"><span data-stu-id="78e92-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="78e92-105">Co je hostitel?</span><span class="sxs-lookup"><span data-stu-id="78e92-105">What's a host?</span></span>

<span data-ttu-id="78e92-106">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="78e92-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="78e92-107">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="78e92-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="78e92-108">Protokolování</span><span class="sxs-lookup"><span data-stu-id="78e92-108">Logging</span></span>
* <span data-ttu-id="78e92-109">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="78e92-109">Configuration</span></span>
* <span data-ttu-id="78e92-110">`IHostedService` implementace</span><span class="sxs-lookup"><span data-stu-id="78e92-110">`IHostedService` implementations</span></span>

<span data-ttu-id="78e92-111">Při spuštění hostitele volá `IHostedService.StartAsync` na každé implementaci <xref:Microsoft.Extensions.Hosting.IHostedService>, kterou nalezne v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="78e92-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="78e92-112">V rámci webové aplikace je jedním z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="78e92-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="78e92-113">Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="78e92-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="78e92-114">Ve verzích ASP.NET Core starších než 3,0 se [webový hostitel](xref:fundamentals/host/web-host) používá pro úlohy http.</span><span class="sxs-lookup"><span data-stu-id="78e92-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="78e92-115">Webový hostitel již není doporučen pro webové aplikace a zůstává dostupný pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="78e92-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="78e92-116">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="78e92-116">Set up a host</span></span>

<span data-ttu-id="78e92-117">Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve třídě `Program`.</span><span class="sxs-lookup"><span data-stu-id="78e92-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="78e92-118">Metoda `Main`:</span><span class="sxs-lookup"><span data-stu-id="78e92-118">The `Main` method:</span></span>

* <span data-ttu-id="78e92-119">Volá metodu `CreateHostBuilder` pro vytvoření a konfiguraci objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="78e92-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="78e92-120">Volá `Build` a `Run` metody objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="78e92-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="78e92-121">Zde je *program.cs* kód pro úlohu jiného typu než HTTP a jedna implementace `IHostedService` přidána do kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="78e92-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="78e92-122">Pro úlohy HTTP je metoda `Main` stejná, ale `CreateHostBuilder` volá `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="78e92-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="78e92-123">Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="78e92-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="78e92-124">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že se najde `CreateHostBuilder` metoda, která nakonfiguruje hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="78e92-125">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="78e92-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="78e92-126">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="78e92-126">Default builder settings</span></span>

<span data-ttu-id="78e92-127">Metoda <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="78e92-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="78e92-128">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="78e92-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="78e92-129">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="78e92-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="78e92-130">Proměnné prostředí s předponou "DOTNET_".</span><span class="sxs-lookup"><span data-stu-id="78e92-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="78e92-131">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="78e92-131">Command-line arguments.</span></span>
* <span data-ttu-id="78e92-132">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="78e92-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="78e92-133">*appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="78e92-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="78e92-134">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="78e92-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="78e92-135">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development`.</span><span class="sxs-lookup"><span data-stu-id="78e92-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="78e92-136">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="78e92-136">Environment variables.</span></span>
  * <span data-ttu-id="78e92-137">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="78e92-137">Command-line arguments.</span></span>
* <span data-ttu-id="78e92-138">Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="78e92-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="78e92-139">Konzola</span><span class="sxs-lookup"><span data-stu-id="78e92-139">Console</span></span>
  * <span data-ttu-id="78e92-140">Ladění</span><span class="sxs-lookup"><span data-stu-id="78e92-140">Debug</span></span>
  * <span data-ttu-id="78e92-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="78e92-141">EventSource</span></span>
  * <span data-ttu-id="78e92-142">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="78e92-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="78e92-143">Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.</span><span class="sxs-lookup"><span data-stu-id="78e92-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="78e92-144">Metoda `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="78e92-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="78e92-145">Načte konfiguraci hostitele z proměnných prostředí s předponou "ASPNETCORE_".</span><span class="sxs-lookup"><span data-stu-id="78e92-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="78e92-146">Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="78e92-147">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="78e92-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="78e92-148">Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="78e92-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="78e92-149">Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , pokud ASPNETCORE_FORWARDEDHEADERS_ENABLED = true.</span><span class="sxs-lookup"><span data-stu-id="78e92-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="78e92-150">Povolí integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="78e92-150">Enables IIS integration.</span></span> <span data-ttu-id="78e92-151">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="78e92-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="78e92-152">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="78e92-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="78e92-153">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="78e92-153">Framework-provided services</span></span>

<span data-ttu-id="78e92-154">Mezi služby, které jsou zaregistrovány automaticky, patří následující:</span><span class="sxs-lookup"><span data-stu-id="78e92-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="78e92-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="78e92-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="78e92-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="78e92-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="78e92-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="78e92-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="78e92-158">Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="78e92-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="78e92-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="78e92-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="78e92-160">Vložení služby <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (dřív `IApplicationLifetime`) do libovolné třídy pro zpracování úloh po spuštění a řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="78e92-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="78e92-161">Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="78e92-162">Rozhraní obsahuje také metodu `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="78e92-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="78e92-163">Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:</span><span class="sxs-lookup"><span data-stu-id="78e92-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="78e92-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="78e92-164">IHostLifetime</span></span>

<span data-ttu-id="78e92-165">Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> určuje, kdy se hostitel spustí a kdy se zastaví.</span><span class="sxs-lookup"><span data-stu-id="78e92-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="78e92-166">Použije se Poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="78e92-166">The last implementation registered is used.</span></span>

<span data-ttu-id="78e92-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je výchozí `IHostLifetime` implementace.</span><span class="sxs-lookup"><span data-stu-id="78e92-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="78e92-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="78e92-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="78e92-169">Naslouchá pro kombinaci kláves CTRL + C/SIGINT nebo SIGTERM a volá <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pro spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="78e92-169">Listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="78e92-170">Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="78e92-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="78e92-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="78e92-171">IHostEnvironment</span></span>

<span data-ttu-id="78e92-172">Vložením služby <xref:Microsoft.Extensions.Hosting.IHostEnvironment> do třídy získáte informace o následujícím:</span><span class="sxs-lookup"><span data-stu-id="78e92-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="78e92-173">NázevAplikace</span><span class="sxs-lookup"><span data-stu-id="78e92-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="78e92-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="78e92-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="78e92-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="78e92-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="78e92-176">Webové aplikace implementují rozhraní `IWebHostEnvironment`, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="78e92-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="78e92-177">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="78e92-177">Host configuration</span></span>

<span data-ttu-id="78e92-178">Konfigurace hostitele se používá pro vlastnosti implementace <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="78e92-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="78e92-179">Konfigurace hostitele je k dispozici z [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) v rámci <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="78e92-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="78e92-180">Po `ConfigureAppConfiguration`se `HostBuilderContext.Configuration` nahradí konfigurací aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="78e92-181">Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> v `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="78e92-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="78e92-182">`ConfigureHostConfiguration` lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="78e92-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="78e92-183">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="78e92-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="78e92-184">Zprostředkovatel proměnné prostředí s předponou `DOTNET_` a argumenty příkazového řádku jsou součástí CreateDefaultBuilder.</span><span class="sxs-lookup"><span data-stu-id="78e92-184">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="78e92-185">U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="78e92-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="78e92-186">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="78e92-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="78e92-187">Například hodnota proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="78e92-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="78e92-188">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="78e92-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="78e92-189">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="78e92-189">App configuration</span></span>

<span data-ttu-id="78e92-190">Konfigurace aplikace se vytvoří voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="78e92-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="78e92-191">`ConfigureAppConfiguration` lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="78e92-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="78e92-192">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="78e92-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="78e92-193">Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di.</span><span class="sxs-lookup"><span data-stu-id="78e92-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="78e92-194">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="78e92-195">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="78e92-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="78e92-196">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="78e92-196">Settings for all app types</span></span>

<span data-ttu-id="78e92-197">V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP.</span><span class="sxs-lookup"><span data-stu-id="78e92-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="78e92-198">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="78e92-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="78e92-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="78e92-199">ApplicationName</span></span>

<span data-ttu-id="78e92-200">Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="78e92-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="78e92-201">**Klíč**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="78e92-201">**Key**: applicationName</span></span>  
<span data-ttu-id="78e92-202">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-202">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-203">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="78e92-204">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="78e92-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="78e92-205">K nastavení této hodnoty použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="78e92-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="78e92-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="78e92-206">ContentRootPath</span></span>

<span data-ttu-id="78e92-207">Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="78e92-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="78e92-208">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="78e92-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="78e92-209">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="78e92-209">**Key**: contentRoot</span></span>  
<span data-ttu-id="78e92-210">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-210">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-211">**Výchozí**: složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="78e92-212">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="78e92-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="78e92-213">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseContentRoot` volání v `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="78e92-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="78e92-214">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="78e92-214">For more information, see:</span></span>

* [<span data-ttu-id="78e92-215">Základy: kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="78e92-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="78e92-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="78e92-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="78e92-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="78e92-217">EnvironmentName</span></span>

<span data-ttu-id="78e92-218">Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="78e92-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="78e92-219">Mezi hodnoty definované rozhraní patří `Development`, `Staging`a `Production`.</span><span class="sxs-lookup"><span data-stu-id="78e92-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="78e92-220">U hodnot se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="78e92-220">Values aren't case sensitive.</span></span>

<span data-ttu-id="78e92-221">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="78e92-221">**Key**: environment</span></span>  
<span data-ttu-id="78e92-222">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-222">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-223">**Výchozí**: výroba</span><span class="sxs-lookup"><span data-stu-id="78e92-223">**Default**: Production</span></span>  
<span data-ttu-id="78e92-224">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="78e92-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="78e92-225">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseEnvironment` volání v `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="78e92-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="78e92-226">shutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="78e92-226">ShutdownTimeout</span></span>

<span data-ttu-id="78e92-227">[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="78e92-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="78e92-228">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="78e92-228">The default value is five seconds.</span></span>  <span data-ttu-id="78e92-229">Po uplynutí časového limitu hostitel:</span><span class="sxs-lookup"><span data-stu-id="78e92-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="78e92-230">Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="78e92-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="78e92-231">Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="78e92-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="78e92-232">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="78e92-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="78e92-233">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="78e92-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="78e92-234">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="78e92-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="78e92-235">**Klíč**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="78e92-235">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="78e92-236">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="78e92-236">**Type**: *int*</span></span>  
<span data-ttu-id="78e92-237">**Výchozí**: **Proměnná prostředí**o 5 sekund: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="78e92-237">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="78e92-238">K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="78e92-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="78e92-239">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="78e92-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="78e92-240">Nastavení pro Web Apps</span><span class="sxs-lookup"><span data-stu-id="78e92-240">Settings for web apps</span></span>

<span data-ttu-id="78e92-241">Některá nastavení hostitele se vztahují jenom na úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="78e92-241">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="78e92-242">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="78e92-242">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="78e92-243">Pro tato nastavení jsou k dispozici metody rozšíření na `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="78e92-243">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="78e92-244">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` je instance `IWebHostBuilder`, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="78e92-244">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="78e92-245">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="78e92-245">CaptureStartupErrors</span></span>

<span data-ttu-id="78e92-246">Při `false`dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="78e92-246">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="78e92-247">Při `true`zachytí hostitel při spuštění výjimky a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="78e92-247">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="78e92-248">**Klíč**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="78e92-248">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="78e92-249">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="78e92-249">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="78e92-250">**Výchozí**: výchozí nastavení `false`, pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true`.</span><span class="sxs-lookup"><span data-stu-id="78e92-250">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="78e92-251">**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="78e92-251">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="78e92-252">Chcete-li nastavit tuto hodnotu, použijte `CaptureStartupErrors`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="78e92-252">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="78e92-253">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="78e92-253">DetailedErrors</span></span>

<span data-ttu-id="78e92-254">Pokud je povoleno nebo když je prostředí `Development`, aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="78e92-254">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="78e92-255">**Klíč**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="78e92-255">**Key**: detailedErrors</span></span>  
<span data-ttu-id="78e92-256">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="78e92-256">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="78e92-257">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="78e92-257">**Default**: false</span></span>  
<span data-ttu-id="78e92-258">**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="78e92-258">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="78e92-259">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="78e92-259">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="78e92-260">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="78e92-260">HostingStartupAssemblies</span></span>

<span data-ttu-id="78e92-261">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="78e92-261">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="78e92-262">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-262">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="78e92-263">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="78e92-263">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="78e92-264">**Klíč**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="78e92-264">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="78e92-265">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-265">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-266">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="78e92-266">**Default**: Empty string</span></span>  
<span data-ttu-id="78e92-267">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="78e92-267">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="78e92-268">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="78e92-268">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="78e92-269">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="78e92-269">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="78e92-270">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="78e92-270">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="78e92-271">**Klíč**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="78e92-271">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="78e92-272">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-272">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-273">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="78e92-273">**Default**: Empty string</span></span>  
<span data-ttu-id="78e92-274">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="78e92-274">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="78e92-275">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="78e92-275">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="78e92-276">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="78e92-276">HTTPS_Port</span></span>

<span data-ttu-id="78e92-277">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="78e92-277">The HTTPS redirect port.</span></span> <span data-ttu-id="78e92-278">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="78e92-278">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="78e92-279">**Klíč**: https_port</span><span class="sxs-lookup"><span data-stu-id="78e92-279">**Key**: https_port</span></span>  
<span data-ttu-id="78e92-280">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-280">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-281">**Výchozí**hodnota: výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="78e92-281">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="78e92-282">**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="78e92-282">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="78e92-283">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="78e92-283">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="78e92-284">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="78e92-284">PreferHostingUrls</span></span>

<span data-ttu-id="78e92-285">Určuje, zda má hostitel naslouchat na adresách URL konfigurovaných pomocí `IWebHostBuilder` namísto těch nakonfigurovaných pomocí implementace `IServer`.</span><span class="sxs-lookup"><span data-stu-id="78e92-285">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="78e92-286">**Klíč**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="78e92-286">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="78e92-287">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="78e92-287">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="78e92-288">**Výchozí**: true</span><span class="sxs-lookup"><span data-stu-id="78e92-288">**Default**: true</span></span>  
<span data-ttu-id="78e92-289">**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="78e92-289">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="78e92-290">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `PreferHostingUrls`volání:</span><span class="sxs-lookup"><span data-stu-id="78e92-290">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="78e92-291">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="78e92-291">PreventHostingStartup</span></span>

<span data-ttu-id="78e92-292">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-292">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="78e92-293">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="78e92-293">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="78e92-294">**Klíč**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="78e92-294">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="78e92-295">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="78e92-295">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="78e92-296">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="78e92-296">**Default**: false</span></span>  
<span data-ttu-id="78e92-297">**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="78e92-297">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="78e92-298">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseSetting` volání:</span><span class="sxs-lookup"><span data-stu-id="78e92-298">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="78e92-299">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="78e92-299">StartupAssembly</span></span>

<span data-ttu-id="78e92-300">Sestavení, ve kterém se má hledat `Startup` třídy</span><span class="sxs-lookup"><span data-stu-id="78e92-300">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="78e92-301">**Klíč**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="78e92-301">**Key**: startupAssembly</span></span>  
<span data-ttu-id="78e92-302">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-302">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-303">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="78e92-303">**Default**: The app's assembly</span></span>  
<span data-ttu-id="78e92-304">**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="78e92-304">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="78e92-305">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="78e92-305">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="78e92-306">`UseStartup` může převzít název sestavení (`string`) nebo typ (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="78e92-306">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="78e92-307">Je-li voláno více metod `UseStartup`, má poslední název přednost.</span><span class="sxs-lookup"><span data-stu-id="78e92-307">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="78e92-308">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="78e92-308">URLs</span></span>

<span data-ttu-id="78e92-309">Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="78e92-309">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="78e92-310">například `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="78e92-310">For example, `http://localhost:123`.</span></span> <span data-ttu-id="78e92-311">Pomocí příkazu "\*" označíte, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="78e92-311">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="78e92-312">Protokol (`http://` nebo `https://`) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="78e92-312">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="78e92-313">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="78e92-313">Supported formats vary among servers.</span></span>

<span data-ttu-id="78e92-314">**Klíč**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="78e92-314">**Key**: urls</span></span>  
<span data-ttu-id="78e92-315">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-315">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-316">**Výchozí**: `http://localhost:5000` a `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="78e92-316">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="78e92-317">**Proměnná prostředí**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="78e92-317">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="78e92-318">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseUrls`volání:</span><span class="sxs-lookup"><span data-stu-id="78e92-318">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="78e92-319">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="78e92-319">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="78e92-320">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="78e92-320">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="78e92-321">WebRoot</span><span class="sxs-lookup"><span data-stu-id="78e92-321">WebRoot</span></span>

<span data-ttu-id="78e92-322">Relativní cesta ke statickým prostředkům aplikace</span><span class="sxs-lookup"><span data-stu-id="78e92-322">The relative path to the app's static assets.</span></span>

<span data-ttu-id="78e92-323">**Klíč**: Webroot</span><span class="sxs-lookup"><span data-stu-id="78e92-323">**Key**: webroot</span></span>  
<span data-ttu-id="78e92-324">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-324">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-325">**Výchozí**: výchozí hodnota je `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="78e92-325">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="78e92-326">Cesta k *obsahu {root}/wwwroot* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="78e92-326">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="78e92-327">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="78e92-327">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="78e92-328">**Proměnná prostředí**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="78e92-328">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="78e92-329">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseWebRoot`volání:</span><span class="sxs-lookup"><span data-stu-id="78e92-329">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="78e92-330">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="78e92-330">For more information, see:</span></span>

* [<span data-ttu-id="78e92-331">Základy: web root</span><span class="sxs-lookup"><span data-stu-id="78e92-331">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="78e92-332">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="78e92-332">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="78e92-333">Správa životnosti hostitele</span><span class="sxs-lookup"><span data-stu-id="78e92-333">Manage the host lifetime</span></span>

<span data-ttu-id="78e92-334">Volejte metody pro sestavenou <xref:Microsoft.Extensions.Hosting.IHost> implementaci pro spuštění a zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-334">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="78e92-335">Tyto metody ovlivňují všechny <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="78e92-335">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="78e92-336">Spusťte</span><span class="sxs-lookup"><span data-stu-id="78e92-336">Run</span></span>

<span data-ttu-id="78e92-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.</span><span class="sxs-lookup"><span data-stu-id="78e92-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="78e92-338">RunAsync</span><span class="sxs-lookup"><span data-stu-id="78e92-338">RunAsync</span></span>

<span data-ttu-id="78e92-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task>, která se dokončí při aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="78e92-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="78e92-340">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="78e92-340">RunConsoleAsync</span></span>

<span data-ttu-id="78e92-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestavování a spouštění hostitele a čeká na vypnutí CTRL + C/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="78e92-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="78e92-342">Start</span><span class="sxs-lookup"><span data-stu-id="78e92-342">Start</span></span>

<span data-ttu-id="78e92-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spouští hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="78e92-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="78e92-344">StartAsync</span><span class="sxs-lookup"><span data-stu-id="78e92-344">StartAsync</span></span>

<span data-ttu-id="78e92-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí hostitele a vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="78e92-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="78e92-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se volá na začátku `StartAsync`, která čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="78e92-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="78e92-347">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="78e92-347">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="78e92-348">StopAsync</span><span class="sxs-lookup"><span data-stu-id="78e92-348">StopAsync</span></span>

<span data-ttu-id="78e92-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> se pokusí zastavit hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="78e92-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="78e92-350">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="78e92-350">WaitForShutdown</span></span>

<span data-ttu-id="78e92-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například prostřednictvím CTRL + C/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="78e92-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="78e92-352">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="78e92-352">WaitForShutdownAsync</span></span>

<span data-ttu-id="78e92-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje prostřednictvím daného tokenu a volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="78e92-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="78e92-354">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="78e92-354">External control</span></span>

<span data-ttu-id="78e92-355">Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="78e92-355">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="78e92-356">ASP.NET Core aplikace nakonfigurují a spouštějí hostitele.</span><span class="sxs-lookup"><span data-stu-id="78e92-356">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="78e92-357">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="78e92-357">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="78e92-358">Tento článek se zabývá ASP.NET Core obecný hostitel (<xref:Microsoft.Extensions.Hosting.HostBuilder>), který se používá pro aplikace, které nezpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="78e92-358">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="78e92-359">Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele, aby bylo možné rozšířit pole hostitelských scénářů.</span><span class="sxs-lookup"><span data-stu-id="78e92-359">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="78e92-360">Zasílání zpráv, úloh na pozadí a dalších úloh nevyužívajících HTTP na základě výhody obecného hostitele z různých možností, jako je konfigurace, vkládání závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="78e92-360">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="78e92-361">Obecný hostitel je v ASP.NET Core 2,1 novinkou a není vhodný pro scénáře hostování webů.</span><span class="sxs-lookup"><span data-stu-id="78e92-361">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="78e92-362">U scénářů pro hostování webů použijte [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="78e92-362">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="78e92-363">Obecný hostitel nahradí webového hostitele v budoucí verzi a bude fungovat jako primární rozhraní API v rámci scénářů HTTP i non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="78e92-363">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="78e92-364">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="78e92-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="78e92-365">Při spuštění ukázkové aplikace v [Visual Studio Code](https://code.visualstudio.com/)použijte *externí nebo integrovaný terminál*.</span><span class="sxs-lookup"><span data-stu-id="78e92-365">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="78e92-366">Nespouštějte ukázku ve `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="78e92-366">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="78e92-367">Nastavení konzoly v Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="78e92-367">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="78e92-368">Otevřete soubor *. VSCode/Launch. JSON* .</span><span class="sxs-lookup"><span data-stu-id="78e92-368">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="78e92-369">V konfiguraci **spuštění .NET Core (konzola)** vyhledejte položku **Konzola** .</span><span class="sxs-lookup"><span data-stu-id="78e92-369">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="78e92-370">Nastavte hodnotu buď `externalTerminal`, nebo `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="78e92-370">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="78e92-371">Úvod</span><span class="sxs-lookup"><span data-stu-id="78e92-371">Introduction</span></span>

<span data-ttu-id="78e92-372">Knihovna obecných hostitelů je k dispozici v oboru názvů <xref:Microsoft.Extensions.Hosting> a poskytuje balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) .</span><span class="sxs-lookup"><span data-stu-id="78e92-372">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="78e92-373">Balíček `Microsoft.Extensions.Hosting` obsahuje [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="78e92-373">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="78e92-374"><xref:Microsoft.Extensions.Hosting.IHostedService> je vstupním bodem pro provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="78e92-374"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="78e92-375">Jednotlivé implementace <xref:Microsoft.Extensions.Hosting.IHostedService> jsou spouštěny v pořadí [Registrace služby v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="78e92-375">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="78e92-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> se volá na každém <xref:Microsoft.Extensions.Hosting.IHostedService> při spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> se volá v pořadí zpětné registrace při bezproblémovém vypnutí hostitele.</span><span class="sxs-lookup"><span data-stu-id="78e92-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="78e92-377">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="78e92-377">Set up a host</span></span>

<span data-ttu-id="78e92-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> je hlavní součást, kterou knihovny a aplikace používají k inicializaci, sestavování a spouštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="78e92-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="78e92-379">Možnosti</span><span class="sxs-lookup"><span data-stu-id="78e92-379">Options</span></span>

<span data-ttu-id="78e92-380"><xref:Microsoft.Extensions.Hosting.HostOptions> nakonfigurovat možnosti pro <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="78e92-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="78e92-381">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="78e92-381">Shutdown timeout</span></span>

<span data-ttu-id="78e92-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="78e92-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="78e92-383">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="78e92-383">The default value is five seconds.</span></span>

<span data-ttu-id="78e92-384">Následující konfigurace možnosti v `Program.Main` zvyšuje výchozí časový limit pěti sekund vypnutí na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="78e92-384">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="78e92-385">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="78e92-385">Default services</span></span>

<span data-ttu-id="78e92-386">Během inicializace hostitele jsou registrovány následující služby:</span><span class="sxs-lookup"><span data-stu-id="78e92-386">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="78e92-387">[Prostředí](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="78e92-387">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="78e92-388">[Konfigurace](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="78e92-388">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="78e92-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="78e92-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="78e92-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="78e92-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="78e92-391">[Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="78e92-391">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="78e92-392">[Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="78e92-392">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="78e92-393">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="78e92-393">Host configuration</span></span>

<span data-ttu-id="78e92-394">Konfiguraci hostitele vytvořil:</span><span class="sxs-lookup"><span data-stu-id="78e92-394">Host configuration is created by:</span></span>

* <span data-ttu-id="78e92-395">Volání rozšiřujících metod v <xref:Microsoft.Extensions.Hosting.IHostBuilder> pro nastavení [kořenového adresáře obsahu](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="78e92-395">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="78e92-396">Čtení konfigurace od zprostředkovatelů konfigurace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="78e92-396">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="78e92-397">Metody rozšíření</span><span class="sxs-lookup"><span data-stu-id="78e92-397">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="78e92-398">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="78e92-398">Application key (name)</span></span>

<span data-ttu-id="78e92-399">Vlastnost [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="78e92-399">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="78e92-400">Chcete-li nastavit hodnotu explicitně, použijte [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="78e92-400">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="78e92-401">**Klíč**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="78e92-401">**Key**: applicationName</span></span>  
<span data-ttu-id="78e92-402">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-402">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-403">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-403">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="78e92-404">**Nastavit pomocí**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="78e92-404">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="78e92-405">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="78e92-405">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="78e92-406">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="78e92-406">Content root</span></span>

<span data-ttu-id="78e92-407">Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="78e92-407">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="78e92-408">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="78e92-408">**Key**: contentRoot</span></span>  
<span data-ttu-id="78e92-409">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-409">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-410">**Výchozí**: výchozí nastavení složky, kde se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-410">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="78e92-411">**Nastavit pomocí**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="78e92-411">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="78e92-412">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="78e92-412">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="78e92-413">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="78e92-413">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="78e92-414">Další informace najdete v tématu [základy: kořen obsahu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="78e92-414">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="78e92-415">Prostředí</span><span class="sxs-lookup"><span data-stu-id="78e92-415">Environment</span></span>

<span data-ttu-id="78e92-416">Nastaví [prostředí](xref:fundamentals/environments)aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-416">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="78e92-417">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="78e92-417">**Key**: environment</span></span>  
<span data-ttu-id="78e92-418">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="78e92-418">**Type**: *string*</span></span>  
<span data-ttu-id="78e92-419">**Výchozí**: výroba</span><span class="sxs-lookup"><span data-stu-id="78e92-419">**Default**: Production</span></span>  
<span data-ttu-id="78e92-420">**Nastavit pomocí**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="78e92-420">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="78e92-421">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="78e92-421">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="78e92-422">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="78e92-422">The environment can be set to any value.</span></span> <span data-ttu-id="78e92-423">Mezi hodnoty definované rozhraní patří `Development`, `Staging`a `Production`.</span><span class="sxs-lookup"><span data-stu-id="78e92-423">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="78e92-424">U hodnot se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="78e92-424">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="78e92-425">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="78e92-425">ConfigureHostConfiguration</span></span>

<span data-ttu-id="78e92-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele.</span><span class="sxs-lookup"><span data-stu-id="78e92-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="78e92-427">Konfigurace hostitele se používá k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-427">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="78e92-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="78e92-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="78e92-429">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="78e92-429">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="78e92-430">Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé.</span><span class="sxs-lookup"><span data-stu-id="78e92-430">No providers are included by default.</span></span> <span data-ttu-id="78e92-431">Musíte explicitně zadat, které poskytovatelé konfigurace aplikace vyžaduje v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, včetně:</span><span class="sxs-lookup"><span data-stu-id="78e92-431">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="78e92-432">Konfigurace souboru (například ze souboru *HostSettings. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="78e92-432">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="78e92-433">Konfigurace proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="78e92-433">Environment variable configuration.</span></span>
* <span data-ttu-id="78e92-434">Konfigurace argumentů příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="78e92-434">Command-line argument configuration.</span></span>
* <span data-ttu-id="78e92-435">Všichni další povinní poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="78e92-435">Any other required configuration providers.</span></span>

<span data-ttu-id="78e92-436">Konfigurace souborů hostitele je povolená zadáním základní cesty aplikace s `SetBasePath` následovaným voláním jednoho z [poskytovatelů konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="78e92-436">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="78e92-437">Ukázková aplikace používá soubor JSON *HostSettings. JSON*a volá <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> ke využívání nastavení konfigurace hostitele souboru.</span><span class="sxs-lookup"><span data-stu-id="78e92-437">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="78e92-438">Chcete-li přidat [konfiguraci proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v Tvůrci hostitele.</span><span class="sxs-lookup"><span data-stu-id="78e92-438">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="78e92-439">`AddEnvironmentVariables` akceptuje volitelnou uživatelem definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="78e92-439">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="78e92-440">Ukázková aplikace používá předponu `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="78e92-440">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="78e92-441">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="78e92-441">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="78e92-442">Když je nakonfigurován hostitel ukázkové aplikace, hodnota proměnné prostředí pro `PREFIX_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="78e92-442">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="78e92-443">Během vývoje při používání sady [Visual Studio](https://visualstudio.microsoft.com) nebo při spuštění aplikace s `dotnet run`lze proměnné prostředí nastavit v souboru *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="78e92-443">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="78e92-444">V [Visual Studio Code](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *. VSCode/Launch. JSON* během vývoje.</span><span class="sxs-lookup"><span data-stu-id="78e92-444">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="78e92-445">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="78e92-445">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="78e92-446">[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="78e92-446">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="78e92-447">K povolení argumentů příkazového řádku pro přepsání konfigurace poskytované předchozími poskytovateli konfigurace se přidá poslední konfigurace příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="78e92-447">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="78e92-448">*HostSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="78e92-448">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="78e92-449">Další konfiguraci lze poskytnout pomocí klíčů [ApplicationName](#application-key-name) a [contentRoot](#content-root) .</span><span class="sxs-lookup"><span data-stu-id="78e92-449">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="78e92-450">Příklad `HostBuilder` konfigurace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="78e92-450">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="78e92-451">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="78e92-451">ConfigureAppConfiguration</span></span>

<span data-ttu-id="78e92-452">Konfigurace aplikace se vytvoří voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> v implementaci <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="78e92-452">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="78e92-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78e92-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="78e92-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="78e92-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="78e92-455">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="78e92-455">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="78e92-456">Konfigurace vytvořená nástrojem <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="78e92-456">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="78e92-457">Konfigurace aplikace automaticky přijímá konfiguraci hostitele, kterou poskytuje [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="78e92-457">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="78e92-458">Příklad konfigurace aplikace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="78e92-458">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="78e92-459">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="78e92-459">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="78e92-460">*appSettings. Vývoj. JSON*:</span><span class="sxs-lookup"><span data-stu-id="78e92-460">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="78e92-461">*appSettings. Produkční. JSON*:</span><span class="sxs-lookup"><span data-stu-id="78e92-461">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="78e92-462">Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="78e92-462">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="78e92-463">Ukázková aplikace přesune své soubory nastavení aplikace JSON a *HostSettings. JSON* s následující položkou `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="78e92-463">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="78e92-464">Metody rozšíření konfigurace, například <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, například [Microsoft. Extensions. Configuration. JSON](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft. Extensions. Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="78e92-464">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="78e92-465">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), tyto balíčky musí být kromě základního balíčku [Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) přidány do projektu.</span><span class="sxs-lookup"><span data-stu-id="78e92-465">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="78e92-466">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="78e92-466">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="78e92-467">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="78e92-467">ConfigureServices</span></span>

<span data-ttu-id="78e92-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="78e92-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="78e92-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="78e92-470">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="78e92-470">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="78e92-471">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="78e92-471">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="78e92-472">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá metodu rozšíření `AddHostedService` k přidání služby pro události života, `LifetimeEventsHostedService`a časově omezená úloha na pozadí, `TimedHostedService`do aplikace:</span><span class="sxs-lookup"><span data-stu-id="78e92-472">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="78e92-473">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="78e92-473">ConfigureLogging</span></span>

<span data-ttu-id="78e92-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> přidá delegáta pro konfiguraci poskytnuté <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="78e92-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="78e92-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> může být voláno vícekrát s doplňkovou výsledků.</span><span class="sxs-lookup"><span data-stu-id="78e92-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="78e92-476">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="78e92-476">UseConsoleLifetime</span></span>

<span data-ttu-id="78e92-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> naslouchá kombinaci kláves CTRL + C/SIGINT nebo SIGTERM a volá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="78e92-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="78e92-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="78e92-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="78e92-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je předem registrován jako výchozí implementace životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="78e92-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="78e92-480">Je použita poslední registrovaná doba života.</span><span class="sxs-lookup"><span data-stu-id="78e92-480">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="78e92-481">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="78e92-481">Container configuration</span></span>

<span data-ttu-id="78e92-482">Pro podporu zapojení do jiných kontejnerů může hostitel přijmout <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="78e92-482">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="78e92-483">Poskytnutí továrny není součástí registrace DI Container, ale je místo toho hostitel, který se používá k vytvoření konkrétního kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="78e92-483">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="78e92-484">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí továrnu, která byla použita k vytvoření poskytovatele služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="78e92-485">Vlastní konfigurace kontejneru je spravovaná metodou <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="78e92-485">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="78e92-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> poskytuje silné typové prostředí pro konfiguraci kontejneru nad podkladovým rozhraním API hostitele.</span><span class="sxs-lookup"><span data-stu-id="78e92-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="78e92-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="78e92-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="78e92-488">Vytvoření kontejneru služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="78e92-488">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="78e92-489">Poskytněte objekt pro vytváření kontejnerů služby:</span><span class="sxs-lookup"><span data-stu-id="78e92-489">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="78e92-490">Použijte objekt pro vytváření a nakonfigurujte vlastní kontejner služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="78e92-490">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="78e92-491">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="78e92-491">Extensibility</span></span>

<span data-ttu-id="78e92-492">Rozšiřitelnost hostitelů se provádí s metodami rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="78e92-492">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="78e92-493">Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementaci s příkladem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) znázorněným v <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="78e92-493">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="78e92-494">Aplikace vytvoří metodu rozšíření `UseHostedService` k registraci hostované služby předané v `T`:</span><span class="sxs-lookup"><span data-stu-id="78e92-494">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="78e92-495">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="78e92-495">Manage the host</span></span>

<span data-ttu-id="78e92-496">Implementace <xref:Microsoft.Extensions.Hosting.IHost> zodpovídá za spuštění a zastavení <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrované v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="78e92-496">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="78e92-497">Spusťte</span><span class="sxs-lookup"><span data-stu-id="78e92-497">Run</span></span>

<span data-ttu-id="78e92-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud není hostitel vypnutý:</span><span class="sxs-lookup"><span data-stu-id="78e92-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="78e92-499">RunAsync</span><span class="sxs-lookup"><span data-stu-id="78e92-499">RunAsync</span></span>

<span data-ttu-id="78e92-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task>, která se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="78e92-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="78e92-501">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="78e92-501">RunConsoleAsync</span></span>

<span data-ttu-id="78e92-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestavování a spouštění hostitele a čeká na vypnutí CTRL + C/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="78e92-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="78e92-503">Spustit a StopAsync</span><span class="sxs-lookup"><span data-stu-id="78e92-503">Start and StopAsync</span></span>

<span data-ttu-id="78e92-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spouští hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="78e92-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="78e92-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> se pokusí zastavit hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="78e92-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="78e92-506">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="78e92-506">StartAsync and StopAsync</span></span>

<span data-ttu-id="78e92-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78e92-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="78e92-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> zastaví aplikaci.</span><span class="sxs-lookup"><span data-stu-id="78e92-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="78e92-509">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="78e92-509">WaitForShutdown</span></span>

<span data-ttu-id="78e92-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se spustí prostřednictvím <xref:Microsoft.Extensions.Hosting.IHostLifetime>, jako je například `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (naslouchá pro CTRL + C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="78e92-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="78e92-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="78e92-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="78e92-512">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="78e92-512">WaitForShutdownAsync</span></span>

<span data-ttu-id="78e92-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje prostřednictvím daného tokenu a volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="78e92-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="78e92-514">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="78e92-514">External control</span></span>

<span data-ttu-id="78e92-515">Externí kontrolu hostitele lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="78e92-515">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="78e92-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se volá na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, která čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="78e92-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="78e92-517">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="78e92-517">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="78e92-518">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="78e92-518">IHostingEnvironment interface</span></span>

<span data-ttu-id="78e92-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> poskytuje informace o hostitelském prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="78e92-520">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) k získání <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>, aby bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="78e92-520">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="78e92-521">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="78e92-521">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="78e92-522">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="78e92-522">IApplicationLifetime interface</span></span>

<span data-ttu-id="78e92-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> umožňuje aktivity po spuštění a vypnutí, včetně řádných požadavků na vypnutí.</span><span class="sxs-lookup"><span data-stu-id="78e92-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="78e92-524">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="78e92-524">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="78e92-525">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="78e92-525">Cancellation Token</span></span> | <span data-ttu-id="78e92-526">Aktivováno, když&#8230;</span><span class="sxs-lookup"><span data-stu-id="78e92-526">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="78e92-527">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="78e92-527">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="78e92-528">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="78e92-528">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="78e92-529">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="78e92-529">All requests should be processed.</span></span> <span data-ttu-id="78e92-530">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="78e92-530">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="78e92-531">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="78e92-531">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="78e92-532">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="78e92-532">Requests may still be processing.</span></span> <span data-ttu-id="78e92-533">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="78e92-533">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="78e92-534">Konstruktor – vloží službu <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="78e92-534">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="78e92-535">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání konstruktoru do `LifetimeEventsHostedService` třídy (implementace <xref:Microsoft.Extensions.Hosting.IHostedService>) k registraci událostí.</span><span class="sxs-lookup"><span data-stu-id="78e92-535">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="78e92-536">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="78e92-536">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="78e92-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> žádosti o ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="78e92-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="78e92-538">Následující třída používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> k bezproblémovému vypnutí aplikace, když je volána metoda `Shutdown` třídy:</span><span class="sxs-lookup"><span data-stu-id="78e92-538">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="78e92-539">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="78e92-539">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
