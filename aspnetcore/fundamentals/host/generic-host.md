---
title: Obecný hostitel rozhraní .NET
author: rick-anderson
description: Další informace o obecném hostiteli .NET Core, který je zodpovědný za spuštění aplikace a správu životnosti.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 454216cec72048217ede412f8ff6d4261f7353b1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417635"
---
# <a name="net-generic-host"></a><span data-ttu-id="3d441-103">Obecný hostitel rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="3d441-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3d441-104">Tento článek představuje obecný hostitel .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny, jak jej používat.</span><span class="sxs-lookup"><span data-stu-id="3d441-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="3d441-105">Co je to hostitel?</span><span class="sxs-lookup"><span data-stu-id="3d441-105">What's a host?</span></span>

<span data-ttu-id="3d441-106">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="3d441-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="3d441-107">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="3d441-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="3d441-108">Protokolování</span><span class="sxs-lookup"><span data-stu-id="3d441-108">Logging</span></span>
* <span data-ttu-id="3d441-109">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="3d441-109">Configuration</span></span>
* <span data-ttu-id="3d441-110">`IHostedService`Implementace</span><span class="sxs-lookup"><span data-stu-id="3d441-110">`IHostedService` implementations</span></span>

<span data-ttu-id="3d441-111">Při spuštění hostitele volá `IHostedService.StartAsync` na každou <xref:Microsoft.Extensions.Hosting.IHostedService> implementaci, která najde v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="3d441-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="3d441-112">Ve webové aplikaci je `IHostedService` jednou z implementací webová služba, která spouští [implementaci http serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="3d441-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="3d441-113">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je správa životnosti: kontrola nad spuštěním aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="3d441-114">Ve verzích ASP.NET Core starší než 3.0 se [webový hostitel](xref:fundamentals/host/web-host) používá pro úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="3d441-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="3d441-115">Webový hostitel se již nedoporučuje pro webové aplikace a zůstává k dispozici pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="3d441-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="3d441-116">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="3d441-116">Set up a host</span></span>

<span data-ttu-id="3d441-117">Hostitel je obvykle nakonfigurován, sestaven a spuštěn `Program` podle kódu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="3d441-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="3d441-118">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="3d441-118">The `Main` method:</span></span>

* <span data-ttu-id="3d441-119">Volá `CreateHostBuilder` metodu k vytvoření a konfiguraci objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="3d441-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="3d441-120">Volání `Build` `Run` a metody objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="3d441-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="3d441-121">Tady je *Program.cs* kód pro úlohy bez `IHostedService` http, s jedinou implementací přidanou do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="3d441-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="3d441-122">Pro úlohu HTTP `Main` je metoda `CreateHostBuilder` stejná, ale volá `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="3d441-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="3d441-123">Pokud aplikace používá Core entity frameworku, neměňte `CreateHostBuilder` název nebo podpis metody.</span><span class="sxs-lookup"><span data-stu-id="3d441-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="3d441-124">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že najdou metodu, `CreateHostBuilder` která konfiguruje hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="3d441-125">Další informace naleznete v [tématu Návrh-time DbContext Vytvoření](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="3d441-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="3d441-126">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="3d441-126">Default builder settings</span></span>

<span data-ttu-id="3d441-127">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="3d441-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="3d441-128">Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>cestu vrácenou programem .</span><span class="sxs-lookup"><span data-stu-id="3d441-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="3d441-129">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="3d441-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="3d441-130">Proměnné prostředí s `DOTNET_`předponou .</span><span class="sxs-lookup"><span data-stu-id="3d441-130">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="3d441-131">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3d441-131">Command-line arguments.</span></span>
* <span data-ttu-id="3d441-132">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="3d441-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="3d441-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3d441-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="3d441-134">*nastavení aplikace. {Prostředí}.json*.</span><span class="sxs-lookup"><span data-stu-id="3d441-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="3d441-135">[Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí.</span><span class="sxs-lookup"><span data-stu-id="3d441-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="3d441-136">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="3d441-136">Environment variables.</span></span>
  * <span data-ttu-id="3d441-137">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3d441-137">Command-line arguments.</span></span>
* <span data-ttu-id="3d441-138">Přidá následující zprostředkovatele [protokolování:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="3d441-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="3d441-139">Konzola</span><span class="sxs-lookup"><span data-stu-id="3d441-139">Console</span></span>
  * <span data-ttu-id="3d441-140">Ladit</span><span class="sxs-lookup"><span data-stu-id="3d441-140">Debug</span></span>
  * <span data-ttu-id="3d441-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="3d441-141">EventSource</span></span>
  * <span data-ttu-id="3d441-142">EventLog (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="3d441-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="3d441-143">Umožňuje [ověření oboru](xref:fundamentals/dependency-injection#scope-validation) a ověření [závislostí,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) když je prostředí Vývoj.</span><span class="sxs-lookup"><span data-stu-id="3d441-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="3d441-144">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="3d441-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="3d441-145">Načte konfiguraci hostitele z `ASPNETCORE_`proměnných prostředí s předponou .</span><span class="sxs-lookup"><span data-stu-id="3d441-145">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="3d441-146">Nastaví [server Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje jej pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="3d441-147">Výchozí možnosti serveru Kestrel naleznete <xref:fundamentals/servers/kestrel#kestrel-options>v tématu .</span><span class="sxs-lookup"><span data-stu-id="3d441-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="3d441-148">Přidá [middleware filtrování hostitelů](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="3d441-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="3d441-149">Přidá [middleware s předaných záhlaví,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se rovná . `true`</span><span class="sxs-lookup"><span data-stu-id="3d441-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="3d441-150">Umožňuje integraci iis.</span><span class="sxs-lookup"><span data-stu-id="3d441-150">Enables IIS integration.</span></span> <span data-ttu-id="3d441-151">Výchozí možnosti iis <xref:host-and-deploy/iis/index#iis-options>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="3d441-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="3d441-152">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a nastavení pro webové [aplikace](#settings-for-web-apps) části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="3d441-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="3d441-153">Rámcové služby</span><span class="sxs-lookup"><span data-stu-id="3d441-153">Framework-provided services</span></span>

<span data-ttu-id="3d441-154">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="3d441-154">The following services are registered automatically:</span></span>

* [<span data-ttu-id="3d441-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3d441-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="3d441-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3d441-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="3d441-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="3d441-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="3d441-158">Další informace o službách poskytovaných <xref:fundamentals/dependency-injection#framework-provided-services>rámcem naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="3d441-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="3d441-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3d441-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="3d441-160"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Vstříkněte `IApplicationLifetime`(dříve) službu do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="3d441-161">Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci metod spuštění aplikace a zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="3d441-162">Rozhraní také obsahuje `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="3d441-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="3d441-163">Následující příklad je `IHostedService` implementace, `IHostApplicationLifetime` která registruje události:</span><span class="sxs-lookup"><span data-stu-id="3d441-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="3d441-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3d441-164">IHostLifetime</span></span>

<span data-ttu-id="3d441-165">Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> řídí při spuštění hostitele a při jeho zastavení.</span><span class="sxs-lookup"><span data-stu-id="3d441-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="3d441-166">Používá se poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="3d441-166">The last implementation registered is used.</span></span>

<span data-ttu-id="3d441-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementace.</span><span class="sxs-lookup"><span data-stu-id="3d441-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="3d441-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="3d441-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="3d441-169">Naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-169">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="3d441-170">Odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="3d441-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="3d441-171">IHostŽivotní prostředí</span><span class="sxs-lookup"><span data-stu-id="3d441-171">IHostEnvironment</span></span>

<span data-ttu-id="3d441-172">Vložte <xref:Microsoft.Extensions.Hosting.IHostEnvironment> službu do třídy, abyste získali informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="3d441-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="3d441-173">Název_aplikace</span><span class="sxs-lookup"><span data-stu-id="3d441-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="3d441-174">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="3d441-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="3d441-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3d441-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="3d441-176">Webové aplikace `IWebHostEnvironment` implementují rozhraní, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="3d441-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="3d441-177">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="3d441-177">Host configuration</span></span>

<span data-ttu-id="3d441-178">Konfigurace hostitele se používá pro <xref:Microsoft.Extensions.Hosting.IHostEnvironment> vlastnosti implementace.</span><span class="sxs-lookup"><span data-stu-id="3d441-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="3d441-179">Konfigurace hostitele je k dispozici z <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř .</span><span class="sxs-lookup"><span data-stu-id="3d441-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="3d441-180">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , je nahrazen a konfigurátor aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="3d441-181">Chcete-li přidat <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> konfiguraci hostitele, volejte na . `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="3d441-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3d441-182">`ConfigureHostConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="3d441-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3d441-183">Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="3d441-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="3d441-184">Zprostředkovatel proměnných prostředí `DOTNET_` s argumenty předpony a `CreateDefaultBuilder`příkazového řádku jsou zahrnuty do .</span><span class="sxs-lookup"><span data-stu-id="3d441-184">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3d441-185">Pro webové aplikace je přidán poskytovatel `ASPNETCORE_` proměnných prostředí s předponou.</span><span class="sxs-lookup"><span data-stu-id="3d441-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="3d441-186">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="3d441-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="3d441-187">Například hodnota proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se stane `environment` hodnotou konfigurace hostitele pro klíč.</span><span class="sxs-lookup"><span data-stu-id="3d441-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="3d441-188">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="3d441-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="3d441-189">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="3d441-189">App configuration</span></span>

<span data-ttu-id="3d441-190">Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří `IHostBuilder`voláním na .</span><span class="sxs-lookup"><span data-stu-id="3d441-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3d441-191">`ConfigureAppConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="3d441-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3d441-192">Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="3d441-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="3d441-193">Konfigurace vytvořená `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako služba z DI.</span><span class="sxs-lookup"><span data-stu-id="3d441-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="3d441-194">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="3d441-195">Další informace naleznete [v tématu Konfigurace v ASP.NET jádru](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="3d441-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="3d441-196">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="3d441-196">Settings for all app types</span></span>

<span data-ttu-id="3d441-197">V této části jsou uvedena nastavení hostitele, která platí pro úlohy protokolu HTTP i jiné než http.</span><span class="sxs-lookup"><span data-stu-id="3d441-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="3d441-198">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="3d441-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="3d441-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="3d441-199">ApplicationName</span></span>

<span data-ttu-id="3d441-200">Vlastnost [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele.</span><span class="sxs-lookup"><span data-stu-id="3d441-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="3d441-201">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="3d441-201">**Key**: `applicationName`</span></span>  
<span data-ttu-id="3d441-202">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-202">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-203">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="3d441-204">**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="3d441-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="3d441-205">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="3d441-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="3d441-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3d441-206">ContentRootPath</span></span>

<span data-ttu-id="3d441-207">Vlastnost [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="3d441-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="3d441-208">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="3d441-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="3d441-209">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="3d441-209">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="3d441-210">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-210">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-211">**Výchozí**: Složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="3d441-212">**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="3d441-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="3d441-213">Chcete-li nastavit tuto hodnotu, `UseContentRoot` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="3d441-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="3d441-214">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="3d441-214">For more information, see:</span></span>

* [<span data-ttu-id="3d441-215">Základy: Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="3d441-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="3d441-216">Webroot</span><span class="sxs-lookup"><span data-stu-id="3d441-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="3d441-217">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="3d441-217">EnvironmentName</span></span>

<span data-ttu-id="3d441-218">Vlastnost [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="3d441-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="3d441-219">Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a .</span><span class="sxs-lookup"><span data-stu-id="3d441-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="3d441-220">Hodnoty nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="3d441-220">Values aren't case-sensitive.</span></span>

<span data-ttu-id="3d441-221">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="3d441-221">**Key**: `environment`</span></span>  
<span data-ttu-id="3d441-222">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-222">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-223">**Výchozí :**`Production`</span><span class="sxs-lookup"><span data-stu-id="3d441-223">**Default**: `Production`</span></span>  
<span data-ttu-id="3d441-224">**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="3d441-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="3d441-225">Chcete-li nastavit tuto hodnotu, `UseEnvironment` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="3d441-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="3d441-226">Timeout vypnutí</span><span class="sxs-lookup"><span data-stu-id="3d441-226">ShutdownTimeout</span></span>

<span data-ttu-id="3d441-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>výtok pro .</span><span class="sxs-lookup"><span data-stu-id="3d441-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="3d441-228">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="3d441-228">The default value is five seconds.</span></span>  <span data-ttu-id="3d441-229">Během časového období hostitel:</span><span class="sxs-lookup"><span data-stu-id="3d441-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="3d441-230">Aktivuje [iHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="3d441-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="3d441-231">Pokusí se zastavit hostované služby, protokolování chyb pro služby, které se nepodaří zastavit.</span><span class="sxs-lookup"><span data-stu-id="3d441-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="3d441-232">Pokud vyprší časový limit před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="3d441-233">Služby se zastaví, i když ještě nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="3d441-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="3d441-234">Pokud služby vyžadují další čas k zastavení, zvyšte časový čas.</span><span class="sxs-lookup"><span data-stu-id="3d441-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="3d441-235">**Klíč**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="3d441-235">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="3d441-236">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="3d441-236">**Type**: `int`</span></span>  
<span data-ttu-id="3d441-237">**Výchozí**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="3d441-237">**Default**: 5 seconds</span></span>  
<span data-ttu-id="3d441-238">**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="3d441-238">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="3d441-239">Chcete-li tuto hodnotu nastavit, `HostOptions`použijte proměnnou prostředí nebo nakonfigurujte .</span><span class="sxs-lookup"><span data-stu-id="3d441-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="3d441-240">Následující příklad nastaví časový čas na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="3d441-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="3d441-241">Zakázat opětovné načtení konfigurace aplikace při změně</span><span class="sxs-lookup"><span data-stu-id="3d441-241">Disable app configuration reload on change</span></span>

<span data-ttu-id="3d441-242">[Ve výchozím nastavení](xref:fundamentals/configuration/index#default), *appsettings.json* a *appsettings.{ Prostředí}.json* jsou znovu načteny při změně souboru.</span><span class="sxs-lookup"><span data-stu-id="3d441-242">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="3d441-243">Chcete-li zakázat toto chování opětovného načtení v `hostBuilder:reloadConfigOnChange` ASP.NET `false`Core 5.0 Preview 3 nebo novější, nastavte klíč na .</span><span class="sxs-lookup"><span data-stu-id="3d441-243">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="3d441-244">**Klíč**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="3d441-244">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="3d441-245">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="3d441-245">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3d441-246">**Výchozí :**`true`</span><span class="sxs-lookup"><span data-stu-id="3d441-246">**Default**: `true`</span></span>  
<span data-ttu-id="3d441-247">**Argument příkazového řádku**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="3d441-247">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="3d441-248">**Proměnná prostředí**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="3d441-248">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="3d441-249">Oddělovač`:`dvojtečky ( ) nefunguje s hierarchickými klíči proměnné prostředí na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="3d441-249">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="3d441-250">Další informace naleznete v tématu [Proměnné prostředí](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="3d441-250">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="3d441-251">Nastavení webových aplikací</span><span class="sxs-lookup"><span data-stu-id="3d441-251">Settings for web apps</span></span>

<span data-ttu-id="3d441-252">Některá nastavení hostitele platí pouze pro úlohy PROTOKOLU HTTP.</span><span class="sxs-lookup"><span data-stu-id="3d441-252">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="3d441-253">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="3d441-253">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="3d441-254">Pro tato `IWebHostBuilder` nastavení jsou k dispozici metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="3d441-254">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="3d441-255">Ukázky kódu, které ukazují, `webBuilder` jak volat `IWebHostBuilder`metody rozšíření assume je instance , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3d441-255">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="3d441-256">Chyby po spuštění systému Capture</span><span class="sxs-lookup"><span data-stu-id="3d441-256">CaptureStartupErrors</span></span>

<span data-ttu-id="3d441-257">Když `false`, chyby při spuštění za následek ukončení hostitele.</span><span class="sxs-lookup"><span data-stu-id="3d441-257">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="3d441-258">Když `true`hostitel zachytí výjimky při spuštění a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="3d441-258">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="3d441-259">**Klíč**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="3d441-259">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="3d441-260">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="3d441-260">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3d441-261">**Výchozí**: Výchozí `false` nastavení, pokud aplikace běží s Kestrel `true`za IIS, kde je výchozí .</span><span class="sxs-lookup"><span data-stu-id="3d441-261">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="3d441-262">**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="3d441-262">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="3d441-263">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="3d441-263">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="3d441-264">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="3d441-264">DetailedErrors</span></span>

<span data-ttu-id="3d441-265">Pokud je povoleno nebo `Development`pokud je prostředí , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="3d441-265">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="3d441-266">**Klíč**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="3d441-266">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="3d441-267">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="3d441-267">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3d441-268">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="3d441-268">**Default**: `false`</span></span>  
<span data-ttu-id="3d441-269">**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="3d441-269">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="3d441-270">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3d441-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="3d441-271">Hostovánístartupových sestavení</span><span class="sxs-lookup"><span data-stu-id="3d441-271">HostingStartupAssemblies</span></span>

<span data-ttu-id="3d441-272">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který se načte při spuštění.</span><span class="sxs-lookup"><span data-stu-id="3d441-272">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="3d441-273">Přestože hodnota konfigurace výchozí prázdný řetězec, hostování spouštěcí sestavení vždy součástí sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-273">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="3d441-274">Když jsou k dispozici hostování spouštěcísestavení, jsou přidány do sestavení aplikace pro načtení při aplikaci vytváří své běžné služby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="3d441-274">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="3d441-275">**Klíč**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="3d441-275">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="3d441-276">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-276">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-277">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="3d441-277">**Default**: Empty string</span></span>  
<span data-ttu-id="3d441-278">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3d441-278">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="3d441-279">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3d441-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="3d441-280">HostováníStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="3d441-280">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="3d441-281">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který má být při spuštění vyloučen.</span><span class="sxs-lookup"><span data-stu-id="3d441-281">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="3d441-282">**Klíč**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="3d441-282">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="3d441-283">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-283">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-284">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="3d441-284">**Default**: Empty string</span></span>  
<span data-ttu-id="3d441-285">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3d441-285">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="3d441-286">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3d441-286">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="3d441-287">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="3d441-287">HTTPS_Port</span></span>

<span data-ttu-id="3d441-288">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3d441-288">The HTTPS redirect port.</span></span> <span data-ttu-id="3d441-289">Používá se při [vynucování protokolu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="3d441-289">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3d441-290">**Klíč**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="3d441-290">**Key**: `https_port`</span></span>  
<span data-ttu-id="3d441-291">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-291">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-292">**Výchozí**: Výchozí hodnota není nastavena.</span><span class="sxs-lookup"><span data-stu-id="3d441-292">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="3d441-293">**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="3d441-293">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="3d441-294">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3d441-294">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="3d441-295">PreferhostingUrls</span><span class="sxs-lookup"><span data-stu-id="3d441-295">PreferHostingUrls</span></span>

<span data-ttu-id="3d441-296">Označuje, zda má hostitel naslouchat adresám `IWebHostBuilder` URL nakonfigurovaným s `IServer` namísto adres URL nakonfigurovaných s implementací.</span><span class="sxs-lookup"><span data-stu-id="3d441-296">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="3d441-297">**Klíč**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="3d441-297">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="3d441-298">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="3d441-298">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3d441-299">**Výchozí :**`true`</span><span class="sxs-lookup"><span data-stu-id="3d441-299">**Default**: `true`</span></span>  
<span data-ttu-id="3d441-300">**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="3d441-300">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="3d441-301">Chcete-li nastavit tuto hodnotu, `PreferHostingUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="3d441-301">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="3d441-302">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="3d441-302">PreventHostingStartup</span></span>

<span data-ttu-id="3d441-303">Zabraňuje automatickému načítání hostitelských spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-303">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="3d441-304">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3d441-304">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="3d441-305">**Klíč**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="3d441-305">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="3d441-306">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="3d441-306">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3d441-307">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="3d441-307">**Default**: `false`</span></span>  
<span data-ttu-id="3d441-308">**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="3d441-308">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="3d441-309">Chcete-li nastavit tuto hodnotu, `UseSetting` použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="3d441-309">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="3d441-310">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="3d441-310">StartupAssembly</span></span>

<span data-ttu-id="3d441-311">Sestavení k hledání `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="3d441-311">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="3d441-312">**Klíč**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="3d441-312">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="3d441-313">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-313">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-314">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="3d441-314">**Default**: The app's assembly</span></span>  
<span data-ttu-id="3d441-315">**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="3d441-315">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="3d441-316">Chcete-li nastavit tuto hodnotu, `UseStartup`použijte proměnnou prostředí nebo volání .</span><span class="sxs-lookup"><span data-stu-id="3d441-316">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="3d441-317">`UseStartup`může mít název`string`sestavení ( )`TStartup`nebo typ ( ).</span><span class="sxs-lookup"><span data-stu-id="3d441-317">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="3d441-318">Pokud `UseStartup` je voláno více metod, má přednost poslední metoda.</span><span class="sxs-lookup"><span data-stu-id="3d441-318">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="3d441-319">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="3d441-319">URLs</span></span>

<span data-ttu-id="3d441-320">Seznam adres IP oddělených středníkem nebo hostitelských adres s porty a protokoly, které by měl server naslouchat požadavkům.</span><span class="sxs-lookup"><span data-stu-id="3d441-320">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="3d441-321">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="3d441-321">For example, `http://localhost:123`.</span></span> <span data-ttu-id="3d441-322">Pomocí\*" " označuje, že server by měl naslouchat požadavkům na libovolnou adresu IP `http://*:5000`nebo název hostitele pomocí zadaného portu a protokolu (například ).</span><span class="sxs-lookup"><span data-stu-id="3d441-322">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="3d441-323">Protokol (`http://` `https://`nebo ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="3d441-323">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="3d441-324">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="3d441-324">Supported formats vary among servers.</span></span>

<span data-ttu-id="3d441-325">**Klíč**:`urls`</span><span class="sxs-lookup"><span data-stu-id="3d441-325">**Key**: `urls`</span></span>  
<span data-ttu-id="3d441-326">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-326">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-327">**Výchozí** `http://localhost:5000` : a`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="3d441-327">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="3d441-328">**Proměnná prostředí**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="3d441-328">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="3d441-329">Chcete-li nastavit tuto hodnotu, `UseUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="3d441-329">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="3d441-330">Kestrel má vlastní konfigurační rozhraní API koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="3d441-330">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="3d441-331">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3d441-331">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="3d441-332">Webroot</span><span class="sxs-lookup"><span data-stu-id="3d441-332">WebRoot</span></span>

<span data-ttu-id="3d441-333">Relativní cesta ke statickým prostředkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-333">The relative path to the app's static assets.</span></span>

<span data-ttu-id="3d441-334">**Klíč**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="3d441-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="3d441-335">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-335">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-336">**Výchozí**: Výchozí `wwwroot`hodnota je .</span><span class="sxs-lookup"><span data-stu-id="3d441-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="3d441-337">Cesta k *kořenovému adresáři obsahu}/www root* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="3d441-337">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="3d441-338">Pokud cesta neexistuje, použije se zprostředkovatel souboru bez operace.</span><span class="sxs-lookup"><span data-stu-id="3d441-338">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="3d441-339">**Proměnná prostředí**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="3d441-339">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="3d441-340">Chcete-li nastavit tuto hodnotu, `UseWebRoot`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="3d441-340">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="3d441-341">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="3d441-341">For more information, see:</span></span>

* [<span data-ttu-id="3d441-342">Základy: Kořen webu</span><span class="sxs-lookup"><span data-stu-id="3d441-342">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="3d441-343">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3d441-343">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="3d441-344">Správa doby trvání hostitele</span><span class="sxs-lookup"><span data-stu-id="3d441-344">Manage the host lifetime</span></span>

<span data-ttu-id="3d441-345">Volání metody na <xref:Microsoft.Extensions.Hosting.IHost> vytvořené implementace spustit a zastavit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3d441-345">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="3d441-346">Tyto metody <xref:Microsoft.Extensions.Hosting.IHostedService> ovlivňují všechny implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="3d441-346">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="3d441-347">Spusťte</span><span class="sxs-lookup"><span data-stu-id="3d441-347">Run</span></span>

<span data-ttu-id="3d441-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne.</span><span class="sxs-lookup"><span data-stu-id="3d441-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="3d441-349">Synchronizace runasync</span><span class="sxs-lookup"><span data-stu-id="3d441-349">RunAsync</span></span>

<span data-ttu-id="3d441-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="3d441-351">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="3d441-351">RunConsoleAsync</span></span>

<span data-ttu-id="3d441-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="3d441-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="3d441-353">Spustit</span><span class="sxs-lookup"><span data-stu-id="3d441-353">Start</span></span>

<span data-ttu-id="3d441-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="3d441-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="3d441-355">Spuštění synchronizace</span><span class="sxs-lookup"><span data-stu-id="3d441-355">StartAsync</span></span>

<span data-ttu-id="3d441-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="3d441-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na `StartAsync`začátku , který čeká, až bude dokončena před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="3d441-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="3d441-358">To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.</span><span class="sxs-lookup"><span data-stu-id="3d441-358">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="3d441-359">StopAsync</span><span class="sxs-lookup"><span data-stu-id="3d441-359">StopAsync</span></span>

<span data-ttu-id="3d441-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.</span><span class="sxs-lookup"><span data-stu-id="3d441-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="3d441-361">Čekání na vypnutí</span><span class="sxs-lookup"><span data-stu-id="3d441-361">WaitForShutdown</span></span>

<span data-ttu-id="3d441-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud vypnutí je spuštěna IHostLifetime, například přes <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="3d441-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="3d441-363">Čekánínavypnutíasynchroniku</span><span class="sxs-lookup"><span data-stu-id="3d441-363">WaitForShutdownAsync</span></span>

<span data-ttu-id="3d441-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .</span><span class="sxs-lookup"><span data-stu-id="3d441-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="3d441-365">Externí ovládání</span><span class="sxs-lookup"><span data-stu-id="3d441-365">External control</span></span>

<span data-ttu-id="3d441-366">Příméřízení životnosti hostitele lze dosáhnout pomocí metod, které lze volat externě:</span><span class="sxs-lookup"><span data-stu-id="3d441-366">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="3d441-367">Tento článek představuje obecný hostitel .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny, jak jej používat.</span><span class="sxs-lookup"><span data-stu-id="3d441-367">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="3d441-368">Co je to hostitel?</span><span class="sxs-lookup"><span data-stu-id="3d441-368">What's a host?</span></span>

<span data-ttu-id="3d441-369">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="3d441-369">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="3d441-370">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="3d441-370">Dependency injection (DI)</span></span>
* <span data-ttu-id="3d441-371">Protokolování</span><span class="sxs-lookup"><span data-stu-id="3d441-371">Logging</span></span>
* <span data-ttu-id="3d441-372">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="3d441-372">Configuration</span></span>
* <span data-ttu-id="3d441-373">`IHostedService`Implementace</span><span class="sxs-lookup"><span data-stu-id="3d441-373">`IHostedService` implementations</span></span>

<span data-ttu-id="3d441-374">Při spuštění hostitele volá `IHostedService.StartAsync` na každou <xref:Microsoft.Extensions.Hosting.IHostedService> implementaci, která najde v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="3d441-374">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="3d441-375">Ve webové aplikaci je `IHostedService` jednou z implementací webová služba, která spouští [implementaci http serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="3d441-375">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="3d441-376">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je správa životnosti: kontrola nad spuštěním aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-376">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="3d441-377">Ve verzích ASP.NET Core starší než 3.0 se [webový hostitel](xref:fundamentals/host/web-host) používá pro úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="3d441-377">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="3d441-378">Webový hostitel se již nedoporučuje pro webové aplikace a zůstává k dispozici pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="3d441-378">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="3d441-379">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="3d441-379">Set up a host</span></span>

<span data-ttu-id="3d441-380">Hostitel je obvykle nakonfigurován, sestaven a spuštěn `Program` podle kódu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="3d441-380">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="3d441-381">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="3d441-381">The `Main` method:</span></span>

* <span data-ttu-id="3d441-382">Volá `CreateHostBuilder` metodu k vytvoření a konfiguraci objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="3d441-382">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="3d441-383">Volání `Build` `Run` a metody objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="3d441-383">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="3d441-384">Tady je *Program.cs* kód pro úlohy bez `IHostedService` http, s jedinou implementací přidanou do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="3d441-384">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="3d441-385">Pro úlohu HTTP `Main` je metoda `CreateHostBuilder` stejná, ale volá `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="3d441-385">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="3d441-386">Pokud aplikace používá Core entity frameworku, neměňte `CreateHostBuilder` název nebo podpis metody.</span><span class="sxs-lookup"><span data-stu-id="3d441-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="3d441-387">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že najdou metodu, `CreateHostBuilder` která konfiguruje hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="3d441-388">Další informace naleznete v [tématu Návrh-time DbContext Vytvoření](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="3d441-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="3d441-389">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="3d441-389">Default builder settings</span></span>

<span data-ttu-id="3d441-390">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="3d441-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="3d441-391">Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>cestu vrácenou programem .</span><span class="sxs-lookup"><span data-stu-id="3d441-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="3d441-392">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="3d441-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="3d441-393">Proměnné prostředí s `DOTNET_`předponou .</span><span class="sxs-lookup"><span data-stu-id="3d441-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="3d441-394">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3d441-394">Command-line arguments.</span></span>
* <span data-ttu-id="3d441-395">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="3d441-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="3d441-396">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3d441-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="3d441-397">*nastavení aplikace. {Prostředí}.json*.</span><span class="sxs-lookup"><span data-stu-id="3d441-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="3d441-398">[Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí.</span><span class="sxs-lookup"><span data-stu-id="3d441-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="3d441-399">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="3d441-399">Environment variables.</span></span>
  * <span data-ttu-id="3d441-400">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3d441-400">Command-line arguments.</span></span>
* <span data-ttu-id="3d441-401">Přidá následující zprostředkovatele [protokolování:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="3d441-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="3d441-402">Konzola</span><span class="sxs-lookup"><span data-stu-id="3d441-402">Console</span></span>
  * <span data-ttu-id="3d441-403">Ladit</span><span class="sxs-lookup"><span data-stu-id="3d441-403">Debug</span></span>
  * <span data-ttu-id="3d441-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="3d441-404">EventSource</span></span>
  * <span data-ttu-id="3d441-405">EventLog (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="3d441-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="3d441-406">Umožňuje [ověření oboru](xref:fundamentals/dependency-injection#scope-validation) a ověření [závislostí,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) když je prostředí Vývoj.</span><span class="sxs-lookup"><span data-stu-id="3d441-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="3d441-407">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="3d441-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="3d441-408">Načte konfiguraci hostitele z `ASPNETCORE_`proměnných prostředí s předponou .</span><span class="sxs-lookup"><span data-stu-id="3d441-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="3d441-409">Nastaví [server Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje jej pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="3d441-410">Výchozí možnosti serveru Kestrel naleznete <xref:fundamentals/servers/kestrel#kestrel-options>v tématu .</span><span class="sxs-lookup"><span data-stu-id="3d441-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="3d441-411">Přidá [middleware filtrování hostitelů](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="3d441-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="3d441-412">Přidá [middleware s předaných záhlaví,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se rovná . `true`</span><span class="sxs-lookup"><span data-stu-id="3d441-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="3d441-413">Umožňuje integraci iis.</span><span class="sxs-lookup"><span data-stu-id="3d441-413">Enables IIS integration.</span></span> <span data-ttu-id="3d441-414">Výchozí možnosti iis <xref:host-and-deploy/iis/index#iis-options>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="3d441-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="3d441-415">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a nastavení pro webové [aplikace](#settings-for-web-apps) části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="3d441-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="3d441-416">Rámcové služby</span><span class="sxs-lookup"><span data-stu-id="3d441-416">Framework-provided services</span></span>

<span data-ttu-id="3d441-417">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="3d441-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="3d441-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3d441-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="3d441-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3d441-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="3d441-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="3d441-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="3d441-421">Další informace o službách poskytovaných <xref:fundamentals/dependency-injection#framework-provided-services>rámcem naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="3d441-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="3d441-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3d441-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="3d441-423"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Vstříkněte `IApplicationLifetime`(dříve) službu do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="3d441-424">Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci metod spuštění aplikace a zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="3d441-425">Rozhraní také obsahuje `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="3d441-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="3d441-426">Následující příklad je `IHostedService` implementace, `IHostApplicationLifetime` která registruje události:</span><span class="sxs-lookup"><span data-stu-id="3d441-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="3d441-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3d441-427">IHostLifetime</span></span>

<span data-ttu-id="3d441-428">Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> řídí při spuštění hostitele a při jeho zastavení.</span><span class="sxs-lookup"><span data-stu-id="3d441-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="3d441-429">Používá se poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="3d441-429">The last implementation registered is used.</span></span>

<span data-ttu-id="3d441-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementace.</span><span class="sxs-lookup"><span data-stu-id="3d441-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="3d441-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="3d441-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="3d441-432">Naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="3d441-433">Odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="3d441-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="3d441-434">IHostŽivotní prostředí</span><span class="sxs-lookup"><span data-stu-id="3d441-434">IHostEnvironment</span></span>

<span data-ttu-id="3d441-435">Vložte <xref:Microsoft.Extensions.Hosting.IHostEnvironment> službu do třídy, abyste získali informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="3d441-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="3d441-436">Název_aplikace</span><span class="sxs-lookup"><span data-stu-id="3d441-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="3d441-437">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="3d441-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="3d441-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3d441-438">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="3d441-439">Webové aplikace `IWebHostEnvironment` implementují rozhraní, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="3d441-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="3d441-440">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="3d441-440">Host configuration</span></span>

<span data-ttu-id="3d441-441">Konfigurace hostitele se používá pro <xref:Microsoft.Extensions.Hosting.IHostEnvironment> vlastnosti implementace.</span><span class="sxs-lookup"><span data-stu-id="3d441-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="3d441-442">Konfigurace hostitele je k dispozici z <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř .</span><span class="sxs-lookup"><span data-stu-id="3d441-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="3d441-443">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , je nahrazen a konfigurátor aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="3d441-444">Chcete-li přidat <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> konfiguraci hostitele, volejte na . `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="3d441-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3d441-445">`ConfigureHostConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="3d441-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3d441-446">Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="3d441-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="3d441-447">Zprostředkovatel proměnných prostředí `DOTNET_` s argumenty předpony a `CreateDefaultBuilder`příkazového řádku jsou zahrnuty do .</span><span class="sxs-lookup"><span data-stu-id="3d441-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3d441-448">Pro webové aplikace je přidán poskytovatel `ASPNETCORE_` proměnných prostředí s předponou.</span><span class="sxs-lookup"><span data-stu-id="3d441-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="3d441-449">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="3d441-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="3d441-450">Například hodnota proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se stane `environment` hodnotou konfigurace hostitele pro klíč.</span><span class="sxs-lookup"><span data-stu-id="3d441-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="3d441-451">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="3d441-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="3d441-452">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="3d441-452">App configuration</span></span>

<span data-ttu-id="3d441-453">Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří `IHostBuilder`voláním na .</span><span class="sxs-lookup"><span data-stu-id="3d441-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3d441-454">`ConfigureAppConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="3d441-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3d441-455">Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="3d441-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="3d441-456">Konfigurace vytvořená `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako služba z DI.</span><span class="sxs-lookup"><span data-stu-id="3d441-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="3d441-457">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="3d441-458">Další informace naleznete [v tématu Konfigurace v ASP.NET jádru](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="3d441-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="3d441-459">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="3d441-459">Settings for all app types</span></span>

<span data-ttu-id="3d441-460">V této části jsou uvedena nastavení hostitele, která platí pro úlohy protokolu HTTP i jiné než http.</span><span class="sxs-lookup"><span data-stu-id="3d441-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="3d441-461">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="3d441-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="3d441-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="3d441-462">ApplicationName</span></span>

<span data-ttu-id="3d441-463">Vlastnost [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele.</span><span class="sxs-lookup"><span data-stu-id="3d441-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="3d441-464">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="3d441-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="3d441-465">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-465">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-466">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="3d441-467">**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="3d441-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="3d441-468">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="3d441-468">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="3d441-469">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3d441-469">ContentRootPath</span></span>

<span data-ttu-id="3d441-470">Vlastnost [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="3d441-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="3d441-471">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="3d441-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="3d441-472">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="3d441-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="3d441-473">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-473">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-474">**Výchozí**: Složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="3d441-475">**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="3d441-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="3d441-476">Chcete-li nastavit tuto hodnotu, `UseContentRoot` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="3d441-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="3d441-477">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="3d441-477">For more information, see:</span></span>

* [<span data-ttu-id="3d441-478">Základy: Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="3d441-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="3d441-479">Webroot</span><span class="sxs-lookup"><span data-stu-id="3d441-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="3d441-480">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="3d441-480">EnvironmentName</span></span>

<span data-ttu-id="3d441-481">Vlastnost [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="3d441-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="3d441-482">Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a .</span><span class="sxs-lookup"><span data-stu-id="3d441-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="3d441-483">Hodnoty nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="3d441-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="3d441-484">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="3d441-484">**Key**: `environment`</span></span>  
<span data-ttu-id="3d441-485">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-485">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-486">**Výchozí :**`Production`</span><span class="sxs-lookup"><span data-stu-id="3d441-486">**Default**: `Production`</span></span>  
<span data-ttu-id="3d441-487">**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="3d441-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="3d441-488">Chcete-li nastavit tuto hodnotu, `UseEnvironment` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="3d441-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="3d441-489">Timeout vypnutí</span><span class="sxs-lookup"><span data-stu-id="3d441-489">ShutdownTimeout</span></span>

<span data-ttu-id="3d441-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>výtok pro .</span><span class="sxs-lookup"><span data-stu-id="3d441-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="3d441-491">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="3d441-491">The default value is five seconds.</span></span>  <span data-ttu-id="3d441-492">Během časového období hostitel:</span><span class="sxs-lookup"><span data-stu-id="3d441-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="3d441-493">Aktivuje [iHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="3d441-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="3d441-494">Pokusí se zastavit hostované služby, protokolování chyb pro služby, které se nepodaří zastavit.</span><span class="sxs-lookup"><span data-stu-id="3d441-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="3d441-495">Pokud vyprší časový limit před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="3d441-496">Služby se zastaví, i když ještě nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="3d441-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="3d441-497">Pokud služby vyžadují další čas k zastavení, zvyšte časový čas.</span><span class="sxs-lookup"><span data-stu-id="3d441-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="3d441-498">**Klíč**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="3d441-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="3d441-499">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="3d441-499">**Type**: `int`</span></span>  
<span data-ttu-id="3d441-500">**Výchozí**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="3d441-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="3d441-501">**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="3d441-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="3d441-502">Chcete-li tuto hodnotu nastavit, `HostOptions`použijte proměnnou prostředí nebo nakonfigurujte .</span><span class="sxs-lookup"><span data-stu-id="3d441-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="3d441-503">Následující příklad nastaví časový čas na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="3d441-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="3d441-504">Nastavení webových aplikací</span><span class="sxs-lookup"><span data-stu-id="3d441-504">Settings for web apps</span></span>

<span data-ttu-id="3d441-505">Některá nastavení hostitele platí pouze pro úlohy PROTOKOLU HTTP.</span><span class="sxs-lookup"><span data-stu-id="3d441-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="3d441-506">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="3d441-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="3d441-507">Pro tato `IWebHostBuilder` nastavení jsou k dispozici metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="3d441-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="3d441-508">Ukázky kódu, které ukazují, `webBuilder` jak volat `IWebHostBuilder`metody rozšíření assume je instance , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3d441-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="3d441-509">Chyby po spuštění systému Capture</span><span class="sxs-lookup"><span data-stu-id="3d441-509">CaptureStartupErrors</span></span>

<span data-ttu-id="3d441-510">Když `false`, chyby při spuštění za následek ukončení hostitele.</span><span class="sxs-lookup"><span data-stu-id="3d441-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="3d441-511">Když `true`hostitel zachytí výjimky při spuštění a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="3d441-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="3d441-512">**Klíč**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="3d441-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="3d441-513">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="3d441-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3d441-514">**Výchozí**: Výchozí `false` nastavení, pokud aplikace běží s Kestrel `true`za IIS, kde je výchozí .</span><span class="sxs-lookup"><span data-stu-id="3d441-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="3d441-515">**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="3d441-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="3d441-516">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="3d441-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="3d441-517">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="3d441-517">DetailedErrors</span></span>

<span data-ttu-id="3d441-518">Pokud je povoleno nebo `Development`pokud je prostředí , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="3d441-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="3d441-519">**Klíč**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="3d441-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="3d441-520">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="3d441-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3d441-521">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="3d441-521">**Default**: `false`</span></span>  
<span data-ttu-id="3d441-522">**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="3d441-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="3d441-523">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3d441-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="3d441-524">Hostovánístartupových sestavení</span><span class="sxs-lookup"><span data-stu-id="3d441-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="3d441-525">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který se načte při spuštění.</span><span class="sxs-lookup"><span data-stu-id="3d441-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="3d441-526">Přestože hodnota konfigurace výchozí prázdný řetězec, hostování spouštěcí sestavení vždy součástí sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="3d441-527">Když jsou k dispozici hostování spouštěcísestavení, jsou přidány do sestavení aplikace pro načtení při aplikaci vytváří své běžné služby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="3d441-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="3d441-528">**Klíč**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="3d441-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="3d441-529">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-529">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-530">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="3d441-530">**Default**: Empty string</span></span>  
<span data-ttu-id="3d441-531">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3d441-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="3d441-532">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3d441-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="3d441-533">HostováníStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="3d441-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="3d441-534">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který má být při spuštění vyloučen.</span><span class="sxs-lookup"><span data-stu-id="3d441-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="3d441-535">**Klíč**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="3d441-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="3d441-536">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-536">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-537">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="3d441-537">**Default**: Empty string</span></span>  
<span data-ttu-id="3d441-538">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3d441-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="3d441-539">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3d441-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="3d441-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="3d441-540">HTTPS_Port</span></span>

<span data-ttu-id="3d441-541">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3d441-541">The HTTPS redirect port.</span></span> <span data-ttu-id="3d441-542">Používá se při [vynucování protokolu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="3d441-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3d441-543">**Klíč**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="3d441-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="3d441-544">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-544">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-545">**Výchozí**: Výchozí hodnota není nastavena.</span><span class="sxs-lookup"><span data-stu-id="3d441-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="3d441-546">**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="3d441-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="3d441-547">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="3d441-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="3d441-548">PreferhostingUrls</span><span class="sxs-lookup"><span data-stu-id="3d441-548">PreferHostingUrls</span></span>

<span data-ttu-id="3d441-549">Označuje, zda má hostitel naslouchat adresám `IWebHostBuilder` URL nakonfigurovaným s `IServer` namísto adres URL nakonfigurovaných s implementací.</span><span class="sxs-lookup"><span data-stu-id="3d441-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="3d441-550">**Klíč**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="3d441-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="3d441-551">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="3d441-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3d441-552">**Výchozí :**`true`</span><span class="sxs-lookup"><span data-stu-id="3d441-552">**Default**: `true`</span></span>  
<span data-ttu-id="3d441-553">**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="3d441-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="3d441-554">Chcete-li nastavit tuto hodnotu, `PreferHostingUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="3d441-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="3d441-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="3d441-555">PreventHostingStartup</span></span>

<span data-ttu-id="3d441-556">Zabraňuje automatickému načítání hostitelských spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="3d441-557">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3d441-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="3d441-558">**Klíč**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="3d441-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="3d441-559">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="3d441-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3d441-560">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="3d441-560">**Default**: `false`</span></span>  
<span data-ttu-id="3d441-561">**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="3d441-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="3d441-562">Chcete-li nastavit tuto hodnotu, `UseSetting` použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="3d441-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="3d441-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="3d441-563">StartupAssembly</span></span>

<span data-ttu-id="3d441-564">Sestavení k hledání `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="3d441-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="3d441-565">**Klíč**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="3d441-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="3d441-566">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-566">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-567">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="3d441-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="3d441-568">**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="3d441-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="3d441-569">Chcete-li nastavit tuto hodnotu, `UseStartup`použijte proměnnou prostředí nebo volání .</span><span class="sxs-lookup"><span data-stu-id="3d441-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="3d441-570">`UseStartup`může mít název`string`sestavení ( )`TStartup`nebo typ ( ).</span><span class="sxs-lookup"><span data-stu-id="3d441-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="3d441-571">Pokud `UseStartup` je voláno více metod, má přednost poslední metoda.</span><span class="sxs-lookup"><span data-stu-id="3d441-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="3d441-572">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="3d441-572">URLs</span></span>

<span data-ttu-id="3d441-573">Seznam adres IP oddělených středníkem nebo hostitelských adres s porty a protokoly, které by měl server naslouchat požadavkům.</span><span class="sxs-lookup"><span data-stu-id="3d441-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="3d441-574">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="3d441-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="3d441-575">Pomocí\*" " označuje, že server by měl naslouchat požadavkům na libovolnou adresu IP `http://*:5000`nebo název hostitele pomocí zadaného portu a protokolu (například ).</span><span class="sxs-lookup"><span data-stu-id="3d441-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="3d441-576">Protokol (`http://` `https://`nebo ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="3d441-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="3d441-577">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="3d441-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="3d441-578">**Klíč**:`urls`</span><span class="sxs-lookup"><span data-stu-id="3d441-578">**Key**: `urls`</span></span>  
<span data-ttu-id="3d441-579">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-579">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-580">**Výchozí** `http://localhost:5000` : a`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="3d441-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="3d441-581">**Proměnná prostředí**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="3d441-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="3d441-582">Chcete-li nastavit tuto hodnotu, `UseUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="3d441-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="3d441-583">Kestrel má vlastní konfigurační rozhraní API koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="3d441-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="3d441-584">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3d441-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="3d441-585">Webroot</span><span class="sxs-lookup"><span data-stu-id="3d441-585">WebRoot</span></span>

<span data-ttu-id="3d441-586">Relativní cesta ke statickým prostředkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-586">The relative path to the app's static assets.</span></span>

<span data-ttu-id="3d441-587">**Klíč**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="3d441-587">**Key**: `webroot`</span></span>  
<span data-ttu-id="3d441-588">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-588">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-589">**Výchozí**: Výchozí `wwwroot`hodnota je .</span><span class="sxs-lookup"><span data-stu-id="3d441-589">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="3d441-590">Cesta k *kořenovému adresáři obsahu}/www root* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="3d441-590">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="3d441-591">Pokud cesta neexistuje, použije se zprostředkovatel souboru bez operace.</span><span class="sxs-lookup"><span data-stu-id="3d441-591">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="3d441-592">**Proměnná prostředí**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="3d441-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="3d441-593">Chcete-li nastavit tuto hodnotu, `UseWebRoot`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="3d441-593">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="3d441-594">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="3d441-594">For more information, see:</span></span>

* [<span data-ttu-id="3d441-595">Základy: Kořen webu</span><span class="sxs-lookup"><span data-stu-id="3d441-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="3d441-596">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3d441-596">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="3d441-597">Správa doby trvání hostitele</span><span class="sxs-lookup"><span data-stu-id="3d441-597">Manage the host lifetime</span></span>

<span data-ttu-id="3d441-598">Volání metody na <xref:Microsoft.Extensions.Hosting.IHost> vytvořené implementace spustit a zastavit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3d441-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="3d441-599">Tyto metody <xref:Microsoft.Extensions.Hosting.IHostedService> ovlivňují všechny implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="3d441-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="3d441-600">Spusťte</span><span class="sxs-lookup"><span data-stu-id="3d441-600">Run</span></span>

<span data-ttu-id="3d441-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne.</span><span class="sxs-lookup"><span data-stu-id="3d441-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="3d441-602">Synchronizace runasync</span><span class="sxs-lookup"><span data-stu-id="3d441-602">RunAsync</span></span>

<span data-ttu-id="3d441-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="3d441-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="3d441-604">RunConsoleAsync</span></span>

<span data-ttu-id="3d441-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="3d441-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="3d441-606">Spustit</span><span class="sxs-lookup"><span data-stu-id="3d441-606">Start</span></span>

<span data-ttu-id="3d441-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="3d441-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="3d441-608">Spuštění synchronizace</span><span class="sxs-lookup"><span data-stu-id="3d441-608">StartAsync</span></span>

<span data-ttu-id="3d441-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="3d441-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na `StartAsync`začátku , který čeká, až bude dokončena před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="3d441-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="3d441-611">To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.</span><span class="sxs-lookup"><span data-stu-id="3d441-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="3d441-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="3d441-612">StopAsync</span></span>

<span data-ttu-id="3d441-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.</span><span class="sxs-lookup"><span data-stu-id="3d441-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="3d441-614">Čekání na vypnutí</span><span class="sxs-lookup"><span data-stu-id="3d441-614">WaitForShutdown</span></span>

<span data-ttu-id="3d441-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud vypnutí je spuštěna IHostLifetime, například přes <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="3d441-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="3d441-616">Čekánínavypnutíasynchroniku</span><span class="sxs-lookup"><span data-stu-id="3d441-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="3d441-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .</span><span class="sxs-lookup"><span data-stu-id="3d441-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="3d441-618">Externí ovládání</span><span class="sxs-lookup"><span data-stu-id="3d441-618">External control</span></span>

<span data-ttu-id="3d441-619">Příméřízení životnosti hostitele lze dosáhnout pomocí metod, které lze volat externě:</span><span class="sxs-lookup"><span data-stu-id="3d441-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="3d441-620">ASP.NET aplikace Core nakonfigurují a spouštějí hostitele.</span><span class="sxs-lookup"><span data-stu-id="3d441-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="3d441-621">Hostitel je zodpovědný za spuštění aplikace a správu životnosti.</span><span class="sxs-lookup"><span data-stu-id="3d441-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="3d441-622">Tento článek popisuje ASP.NET základní<xref:Microsoft.Extensions.Hosting.HostBuilder>obecný hostitel ( ), který se používá pro aplikace, které nezpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="3d441-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="3d441-623">Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele a povolit tak širší škálu hostitelských scénářů.</span><span class="sxs-lookup"><span data-stu-id="3d441-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="3d441-624">Zasílání zpráv, úlohy na pozadí a další úlohy bez protokolu HTTP založené na obecném hostiteli těží z průřezových funkcí, jako je konfigurace, vkládání závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="3d441-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="3d441-625">Obecný hostitel je nový v ASP.NET Core 2.1 a není vhodný pro scénáře webhostingu.</span><span class="sxs-lookup"><span data-stu-id="3d441-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="3d441-626">Pro scénáře webhostingu použijte [webový hostitel](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="3d441-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="3d441-627">Obecný hostitel nahradí webhostingu v budoucí verzi a bude fungovat jako primární rozhraní API hostitele ve scénářích HTTP i bez protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="3d441-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="3d441-628">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="3d441-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3d441-629">Při spuštění ukázkové aplikace v [kódu Visual Studia](https://code.visualstudio.com/)použijte externí nebo integrovaný *terminál*.</span><span class="sxs-lookup"><span data-stu-id="3d441-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="3d441-630">Nespouštějte ukázku `internalConsole`v .</span><span class="sxs-lookup"><span data-stu-id="3d441-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="3d441-631">Nastavení konzoly v kódu sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3d441-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="3d441-632">Otevřete soubor *.vscode/launch.json.*</span><span class="sxs-lookup"><span data-stu-id="3d441-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="3d441-633">V konfiguraci **spuštění jádra .NET (konzola)** vyhledejte položku **konzoly.**</span><span class="sxs-lookup"><span data-stu-id="3d441-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="3d441-634">Nastavte hodnotu `externalTerminal` na `integratedTerminal`buď nebo .</span><span class="sxs-lookup"><span data-stu-id="3d441-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="3d441-635">Úvod</span><span class="sxs-lookup"><span data-stu-id="3d441-635">Introduction</span></span>

<span data-ttu-id="3d441-636">Knihovna obecného hostitele <xref:Microsoft.Extensions.Hosting> je k dispozici v oboru názvů a je k dispozici v balíčku [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/)</span><span class="sxs-lookup"><span data-stu-id="3d441-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="3d441-637">Balíček `Microsoft.Extensions.Hosting` je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="3d441-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="3d441-638"><xref:Microsoft.Extensions.Hosting.IHostedService>je vstupní bod pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="3d441-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="3d441-639">Každá <xref:Microsoft.Extensions.Hosting.IHostedService> implementace je spuštěna v pořadí [registrace služby v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="3d441-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="3d441-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>je volána <xref:Microsoft.Extensions.Hosting.IHostedService> na každý při <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> spuštění hostitele a je volána v pořadí reverzní registrace při hostování řádně vypne.</span><span class="sxs-lookup"><span data-stu-id="3d441-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="3d441-641">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="3d441-641">Set up a host</span></span>

<span data-ttu-id="3d441-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder>je hlavní komponenta, kterou knihovny a aplikace používají k inicializaci, sestavení a spuštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="3d441-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="3d441-643">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3d441-643">Options</span></span>

<span data-ttu-id="3d441-644"><xref:Microsoft.Extensions.Hosting.HostOptions>konfigurovat možnosti <xref:Microsoft.Extensions.Hosting.IHost>pro rozhraní .</span><span class="sxs-lookup"><span data-stu-id="3d441-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="3d441-645">Časový čas vypnutí</span><span class="sxs-lookup"><span data-stu-id="3d441-645">Shutdown timeout</span></span>

<span data-ttu-id="3d441-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>čas pro .</span><span class="sxs-lookup"><span data-stu-id="3d441-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="3d441-647">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="3d441-647">The default value is five seconds.</span></span>

<span data-ttu-id="3d441-648">Následující konfigurace možností `Program.Main` zvyšuje výchozí pětisekundový časový limit vypnutí na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="3d441-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="3d441-649">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="3d441-649">Default services</span></span>

<span data-ttu-id="3d441-650">Následující služby jsou registrovány během inicializace hostitele:</span><span class="sxs-lookup"><span data-stu-id="3d441-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="3d441-651">[Životní](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>prostředí ( )</span><span class="sxs-lookup"><span data-stu-id="3d441-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="3d441-652">[Konfigurace](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )</span><span class="sxs-lookup"><span data-stu-id="3d441-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="3d441-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="3d441-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="3d441-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="3d441-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="3d441-655">[Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="3d441-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="3d441-656">[Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="3d441-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="3d441-657">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="3d441-657">Host configuration</span></span>

<span data-ttu-id="3d441-658">Konfigurace hostitele je vytvořena:</span><span class="sxs-lookup"><span data-stu-id="3d441-658">Host configuration is created by:</span></span>

* <span data-ttu-id="3d441-659">Volání metody <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozšíření na nastavení [kořenového obsahu](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="3d441-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="3d441-660">Čtení konfigurace od <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>zprostředkovatelů konfigurace v .</span><span class="sxs-lookup"><span data-stu-id="3d441-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="3d441-661">Metody rozšíření</span><span class="sxs-lookup"><span data-stu-id="3d441-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="3d441-662">Aplikační klíč (název)</span><span class="sxs-lookup"><span data-stu-id="3d441-662">Application key (name)</span></span>

<span data-ttu-id="3d441-663">Vlastnost [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele.</span><span class="sxs-lookup"><span data-stu-id="3d441-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="3d441-664">Chcete-li explicitně nastavit hodnotu, použijte [klíč HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="3d441-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="3d441-665">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="3d441-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="3d441-666">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-666">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-667">**Výchozí**: Název sestavení obsahujícího vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="3d441-668">**Nastavit pomocí**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="3d441-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="3d441-669">**Proměnná** `<PREFIX_>APPLICATIONNAME` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="3d441-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="3d441-670">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="3d441-670">Content root</span></span>

<span data-ttu-id="3d441-671">Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="3d441-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="3d441-672">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="3d441-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="3d441-673">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-673">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-674">**Výchozí**: Výchozí je složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="3d441-675">**Nastavit pomocí**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="3d441-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="3d441-676">**Proměnná** `<PREFIX_>CONTENTROOT` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="3d441-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="3d441-677">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="3d441-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="3d441-678">Další informace naleznete [v tématu Základy: Kořen obsahu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="3d441-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="3d441-679">Prostředí</span><span class="sxs-lookup"><span data-stu-id="3d441-679">Environment</span></span>

<span data-ttu-id="3d441-680">Nastaví [prostředí](xref:fundamentals/environments)aplikace .</span><span class="sxs-lookup"><span data-stu-id="3d441-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="3d441-681">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="3d441-681">**Key**: `environment`</span></span>  
<span data-ttu-id="3d441-682">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="3d441-682">**Type**: `string`</span></span>  
<span data-ttu-id="3d441-683">**Výchozí :**`Production`</span><span class="sxs-lookup"><span data-stu-id="3d441-683">**Default**: `Production`</span></span>  
<span data-ttu-id="3d441-684">**Nastavit pomocí**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="3d441-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="3d441-685">**Proměnná** `<PREFIX_>ENVIRONMENT` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="3d441-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="3d441-686">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="3d441-686">The environment can be set to any value.</span></span> <span data-ttu-id="3d441-687">Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a .</span><span class="sxs-lookup"><span data-stu-id="3d441-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="3d441-688">Hodnoty nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="3d441-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="3d441-689">Konfiguracekonfigurace host</span><span class="sxs-lookup"><span data-stu-id="3d441-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="3d441-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> an k <xref:Microsoft.Extensions.Configuration.IConfiguration> vytvoření hostitele.</span><span class="sxs-lookup"><span data-stu-id="3d441-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="3d441-691">Konfigurace hostitele se používá k <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> inicializaci pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="3d441-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="3d441-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="3d441-693">Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="3d441-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="3d441-694">Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé.</span><span class="sxs-lookup"><span data-stu-id="3d441-694">No providers are included by default.</span></span> <span data-ttu-id="3d441-695">Je nutné explicitně zadat, bez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>ohledu na to, jaké zprostředkovatele konfigurace aplikace vyžaduje v aplikaci , včetně:</span><span class="sxs-lookup"><span data-stu-id="3d441-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="3d441-696">Konfigurace souboru (například ze souboru *hostsettings.json).*</span><span class="sxs-lookup"><span data-stu-id="3d441-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="3d441-697">Konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="3d441-697">Environment variable configuration.</span></span>
* <span data-ttu-id="3d441-698">Konfigurace argumentu příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3d441-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="3d441-699">Jakékoli další požadované zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3d441-699">Any other required configuration providers.</span></span>

<span data-ttu-id="3d441-700">Konfigurace souboru hostitele je povolena zadáním základní `SetBasePath` cesty aplikace s následovaným voláním jednoho z [poskytovatelů konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="3d441-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="3d441-701">Ukázková aplikace používá soubor JSON, *hostsettings.json*a volání <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> využívat nastavení konfigurace hostitele souboru.</span><span class="sxs-lookup"><span data-stu-id="3d441-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="3d441-702">Chcete-li přidat [konfiguraci proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="3d441-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="3d441-703">`AddEnvironmentVariables`přijme volitelnou uživatelsky definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="3d441-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="3d441-704">Ukázková aplikace používá předponu `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="3d441-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="3d441-705">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="3d441-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="3d441-706">Když je nakonfigurován hostitel ukázkové aplikace, `PREFIX_ENVIRONMENT` hodnota proměnné prostředí pro `environment` se stane hodnotou konfigurace hostitele pro klíč.</span><span class="sxs-lookup"><span data-stu-id="3d441-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="3d441-707">Během vývoje při použití sady Visual `dotnet run` [Studio](https://visualstudio.microsoft.com) nebo spuštění aplikace s aplikací mohou být proměnné prostředí nastaveny v souboru *Vlastnosti/spuštěníNastavení.json.*</span><span class="sxs-lookup"><span data-stu-id="3d441-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="3d441-708">V [kódu sady Visual Studio](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *.vscode/launch.json* během vývoje.</span><span class="sxs-lookup"><span data-stu-id="3d441-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="3d441-709">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3d441-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3d441-710">[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>přidána voláním .</span><span class="sxs-lookup"><span data-stu-id="3d441-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="3d441-711">Konfigurace příkazového řádku je přidána jako poslední, aby bylo možné přepsat konfiguraci poskytovanou staršími poskytovateli konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3d441-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="3d441-712">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3d441-712">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="3d441-713">Další konfiguraci lze poskytnout s [applicationName](#application-key-name) a [contentRoot](#content-root) klíče.</span><span class="sxs-lookup"><span data-stu-id="3d441-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="3d441-714">Příklad `HostBuilder` konfigurace <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>pomocí :</span><span class="sxs-lookup"><span data-stu-id="3d441-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="3d441-715">Konfigurace konfigurace</span><span class="sxs-lookup"><span data-stu-id="3d441-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="3d441-716">Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří <xref:Microsoft.Extensions.Hosting.IHostBuilder> voláním na implementaci.</span><span class="sxs-lookup"><span data-stu-id="3d441-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="3d441-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> an k <xref:Microsoft.Extensions.Configuration.IConfiguration> vytvoření aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="3d441-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="3d441-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="3d441-719">Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="3d441-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="3d441-720">Konfigurace vytvořená <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uživatelem je k dispozici na adrese <xref:Microsoft.Extensions.Hosting.IHost.Services*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="3d441-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="3d441-721">Konfigurace aplikace automaticky přijímá konfiguraci hostitele poskytovanou [společností ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="3d441-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="3d441-722">Příklad konfigurace <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>aplikace pomocí :</span><span class="sxs-lookup"><span data-stu-id="3d441-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="3d441-723">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3d441-723">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="3d441-724">*nastavení aplikace. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="3d441-724">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="3d441-725">*nastavení aplikace. Production.json*:</span><span class="sxs-lookup"><span data-stu-id="3d441-725">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="3d441-726">Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="3d441-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="3d441-727">Ukázková aplikace přesune soubory nastavení aplikace JSON a *hostsettings.json* s následující `<Content>` položkou:</span><span class="sxs-lookup"><span data-stu-id="3d441-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="3d441-728">Metody rozšíření konfigurace, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> jako jsou například a vyžadují další balíčky NuGet, například [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="3d441-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="3d441-729">Pokud aplikace nepoužívá [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), musí být tyto balíčky přidány do projektu kromě základního balíčku [Microsoft.Extensions.Configuration.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)</span><span class="sxs-lookup"><span data-stu-id="3d441-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="3d441-730">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3d441-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="3d441-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="3d441-731">ConfigureServices</span></span>

<span data-ttu-id="3d441-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3d441-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="3d441-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="3d441-734">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3d441-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="3d441-735">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="3d441-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="3d441-736">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) `AddHostedService` používá metodu rozšíření k přidání `LifetimeEventsHostedService`služby pro události `TimedHostedService`životnosti a časované úlohy na pozadí , do aplikace:</span><span class="sxs-lookup"><span data-stu-id="3d441-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="3d441-737">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="3d441-737">ConfigureLogging</span></span>

<span data-ttu-id="3d441-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>přidá delegáta pro konfiguraci <xref:Microsoft.Extensions.Logging.ILoggingBuilder>poskytovaného .</span><span class="sxs-lookup"><span data-stu-id="3d441-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="3d441-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>mohou být volány vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="3d441-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="3d441-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="3d441-740">UseConsoleLifetime</span></span>

<span data-ttu-id="3d441-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="3d441-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="3d441-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="3d441-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je předběžně zaregistrována jako výchozí implementace životnosti.</span><span class="sxs-lookup"><span data-stu-id="3d441-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="3d441-744">Používá se poslední registrovaná životnost.</span><span class="sxs-lookup"><span data-stu-id="3d441-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="3d441-745">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="3d441-745">Container configuration</span></span>

<span data-ttu-id="3d441-746">Pro podporu připojení jiných kontejnerů může <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>hostitel přijmout soubor .</span><span class="sxs-lookup"><span data-stu-id="3d441-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="3d441-747">Poskytování factory není součástí registrace kontejneru DI, ale je hostitele vnitřní slouží k vytvoření kontejneru betonové DI.</span><span class="sxs-lookup"><span data-stu-id="3d441-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="3d441-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder)&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepíše výchozí továrnu použitou k vytvoření poskytovatele služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="3d441-749">Vlastní konfigurace kontejneru <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> je spravována metodou.</span><span class="sxs-lookup"><span data-stu-id="3d441-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="3d441-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>poskytuje prostředí silného typu pro konfiguraci kontejneru nad základní rozhraní API hostitele.</span><span class="sxs-lookup"><span data-stu-id="3d441-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="3d441-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="3d441-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="3d441-752">Vytvoření kontejneru služeb pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3d441-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="3d441-753">Poskytněte továrnu na servisní kontejnery:</span><span class="sxs-lookup"><span data-stu-id="3d441-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="3d441-754">Použijte továrnu a nakonfigurujte vlastní kontejner služeb pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3d441-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="3d441-755">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="3d441-755">Extensibility</span></span>

<span data-ttu-id="3d441-756">Rozšiřitelnost hostitele se provádí pomocí <xref:Microsoft.Extensions.Hosting.IHostBuilder>rozšiřujících metod na .</span><span class="sxs-lookup"><span data-stu-id="3d441-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="3d441-757">Následující příklad ukazuje, jak metoda <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozšíření rozšiřuje implementaci s [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) příklad demonstroval v <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="3d441-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="3d441-758">Aplikace vytvoří metodu `UseHostedService` rozšíření pro registraci hostované služby předané v `T`:</span><span class="sxs-lookup"><span data-stu-id="3d441-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="3d441-759">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="3d441-759">Manage the host</span></span>

<span data-ttu-id="3d441-760">Implementace <xref:Microsoft.Extensions.Hosting.IHost> je zodpovědná za spuštění <xref:Microsoft.Extensions.Hosting.IHostedService> a zastavení implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="3d441-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="3d441-761">Spusťte</span><span class="sxs-lookup"><span data-stu-id="3d441-761">Run</span></span>

<span data-ttu-id="3d441-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne:</span><span class="sxs-lookup"><span data-stu-id="3d441-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="3d441-763">Synchronizace runasync</span><span class="sxs-lookup"><span data-stu-id="3d441-763">RunAsync</span></span>

<span data-ttu-id="3d441-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, která se dokončí, když se aktivuje token zrušení nebo vypnutí:</span><span class="sxs-lookup"><span data-stu-id="3d441-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="3d441-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="3d441-765">RunConsoleAsync</span></span>

<span data-ttu-id="3d441-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="3d441-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="3d441-767">Spustit a zastavitsynchronizacu</span><span class="sxs-lookup"><span data-stu-id="3d441-767">Start and StopAsync</span></span>

<span data-ttu-id="3d441-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="3d441-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="3d441-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.</span><span class="sxs-lookup"><span data-stu-id="3d441-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="3d441-770">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="3d441-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="3d441-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3d441-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="3d441-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zastaví aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3d441-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="3d441-773">Čekání na vypnutí</span><span class="sxs-lookup"><span data-stu-id="3d441-773">WaitForShutdown</span></span>

<span data-ttu-id="3d441-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>se spouští <xref:Microsoft.Extensions.Hosting.IHostLifetime>přes `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` , například (naslouchá <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="3d441-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="3d441-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3d441-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="3d441-776">Čekánínavypnutíasynchroniku</span><span class="sxs-lookup"><span data-stu-id="3d441-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="3d441-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .</span><span class="sxs-lookup"><span data-stu-id="3d441-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="3d441-778">Externí ovládání</span><span class="sxs-lookup"><span data-stu-id="3d441-778">External control</span></span>

<span data-ttu-id="3d441-779">Externího řízení hostitele lze dosáhnout pomocí metod, které lze volat externě:</span><span class="sxs-lookup"><span data-stu-id="3d441-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="3d441-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>začátku , který čeká, až bude dokončena před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="3d441-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="3d441-781">To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.</span><span class="sxs-lookup"><span data-stu-id="3d441-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="3d441-782">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="3d441-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="3d441-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>poskytuje informace o hostitelském prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="3d441-784">Použijte [vstřikování konstruktoru](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> k získání, abyste mohli používat jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="3d441-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="3d441-785">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3d441-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="3d441-786">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3d441-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="3d441-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>umožňuje aktivity po spuštění a vypnutí, včetně řádného požadavku na vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="3d441-788">Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="3d441-789">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="3d441-789">Cancellation Token</span></span> | <span data-ttu-id="3d441-790">Aktivuje se při&#8230;</span><span class="sxs-lookup"><span data-stu-id="3d441-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="3d441-791">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="3d441-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="3d441-792">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="3d441-793">Všechny požadavky by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="3d441-793">All requests should be processed.</span></span> <span data-ttu-id="3d441-794">Vypnutí blokuje, dokud tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="3d441-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="3d441-795">Hostitel provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="3d441-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="3d441-796">Požadavky mohou být stále zpracování.</span><span class="sxs-lookup"><span data-stu-id="3d441-796">Requests may still be processing.</span></span> <span data-ttu-id="3d441-797">Vypnutí blokuje, dokud tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="3d441-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="3d441-798">Konstruktor vstříkne službu <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="3d441-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="3d441-799">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání `LifetimeEventsHostedService` konstruktoru <xref:Microsoft.Extensions.Hosting.IHostedService> do třídy (implementace) k registraci událostí.</span><span class="sxs-lookup"><span data-stu-id="3d441-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="3d441-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="3d441-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="3d441-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>požaduje ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3d441-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="3d441-802">Následující třída <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> používá k řádnému vypnutí aplikace při `Shutdown` volání metody třídy:</span><span class="sxs-lookup"><span data-stu-id="3d441-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="3d441-803">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3d441-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
