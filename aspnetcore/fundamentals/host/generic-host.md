---
title: Obecný hostitel rozhraní .NET
author: rick-anderson
description: Další informace o obecném hostiteli .NET Core, který je zodpovědný za spuštění aplikace a správu životnosti.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: b528a33fa52bfe56faaf9f3ff8c7e43db0d4e184
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384026"
---
# <a name="net-generic-host"></a><span data-ttu-id="b2e40-103">Obecný hostitel rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="b2e40-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="b2e40-104">Šablony ASP.NET Core vytvoří obecný hostitel jádra .NET (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="b2e40-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="b2e40-105">Definice hostitele</span><span class="sxs-lookup"><span data-stu-id="b2e40-105">Host definition</span></span>

<span data-ttu-id="b2e40-106">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="b2e40-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="b2e40-107">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="b2e40-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="b2e40-108">Protokolování</span><span class="sxs-lookup"><span data-stu-id="b2e40-108">Logging</span></span>
* <span data-ttu-id="b2e40-109">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="b2e40-109">Configuration</span></span>
* <span data-ttu-id="b2e40-110">`IHostedService`Implementace</span><span class="sxs-lookup"><span data-stu-id="b2e40-110">`IHostedService` implementations</span></span>

<span data-ttu-id="b2e40-111">Při spuštění hostitele volá `IHostedService.StartAsync` na každou <xref:Microsoft.Extensions.Hosting.IHostedService> implementaci, která najde v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="b2e40-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="b2e40-112">Ve webové aplikaci je `IHostedService` jednou z implementací webová služba, která spouští [implementaci http serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="b2e40-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="b2e40-113">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je správa životnosti: kontrola nad spuštěním aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="b2e40-114">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="b2e40-114">Set up a host</span></span>

<span data-ttu-id="b2e40-115">Hostitel je obvykle nakonfigurován, sestaven a spuštěn `Program` podle kódu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="b2e40-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="b2e40-116">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="b2e40-116">The `Main` method:</span></span>

* <span data-ttu-id="b2e40-117">Volá `CreateHostBuilder` metodu k vytvoření a konfiguraci objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="b2e40-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="b2e40-118">Volání `Build` `Run` a metody objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="b2e40-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="b2e40-119">Webové šablony ASP.NET Core generují následující kód pro vytvoření hostitele:</span><span class="sxs-lookup"><span data-stu-id="b2e40-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="b2e40-120">Následující kód vytvoří úlohu bez `IHostedService` protokolu HTTP s implementací přidanou do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="b2e40-120">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="b2e40-121">Pro úlohu HTTP `Main` je metoda `CreateHostBuilder` stejná, ale volá `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="b2e40-122">Pokud aplikace používá Core entity frameworku, neměňte `CreateHostBuilder` název nebo podpis metody.</span><span class="sxs-lookup"><span data-stu-id="b2e40-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="b2e40-123">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že najdou metodu, `CreateHostBuilder` která konfiguruje hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="b2e40-124">Další informace naleznete v [tématu Návrh-time DbContext Vytvoření](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="b2e40-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="b2e40-125">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="b2e40-125">Default builder settings</span></span>

<span data-ttu-id="b2e40-126">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="b2e40-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="b2e40-127">Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>cestu vrácenou programem .</span><span class="sxs-lookup"><span data-stu-id="b2e40-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="b2e40-128">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="b2e40-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="b2e40-129">Proměnné prostředí s `DOTNET_`předponou .</span><span class="sxs-lookup"><span data-stu-id="b2e40-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="b2e40-130">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b2e40-130">Command-line arguments.</span></span>
* <span data-ttu-id="b2e40-131">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="b2e40-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="b2e40-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b2e40-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="b2e40-133">*nastavení aplikace. {Prostředí}.json*.</span><span class="sxs-lookup"><span data-stu-id="b2e40-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="b2e40-134">[Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="b2e40-135">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-135">Environment variables.</span></span>
  * <span data-ttu-id="b2e40-136">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b2e40-136">Command-line arguments.</span></span>
* <span data-ttu-id="b2e40-137">Přidá následující zprostředkovatele [protokolování:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="b2e40-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="b2e40-138">Konzola</span><span class="sxs-lookup"><span data-stu-id="b2e40-138">Console</span></span>
  * <span data-ttu-id="b2e40-139">Ladit</span><span class="sxs-lookup"><span data-stu-id="b2e40-139">Debug</span></span>
  * <span data-ttu-id="b2e40-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="b2e40-140">EventSource</span></span>
  * <span data-ttu-id="b2e40-141">EventLog (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="b2e40-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="b2e40-142">Umožňuje [ověření oboru](xref:fundamentals/dependency-injection#scope-validation) a ověření [závislostí,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) když je prostředí Vývoj.</span><span class="sxs-lookup"><span data-stu-id="b2e40-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="b2e40-143">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="b2e40-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="b2e40-144">Načte konfiguraci hostitele z `ASPNETCORE_`proměnných prostředí s předponou .</span><span class="sxs-lookup"><span data-stu-id="b2e40-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="b2e40-145">Nastaví [server Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje jej pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="b2e40-146">Výchozí možnosti serveru Kestrel naleznete <xref:fundamentals/servers/kestrel#kestrel-options>v tématu .</span><span class="sxs-lookup"><span data-stu-id="b2e40-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="b2e40-147">Přidá [middleware filtrování hostitelů](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="b2e40-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="b2e40-148">Přidá [middleware s předaných záhlaví,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se rovná . `true`</span><span class="sxs-lookup"><span data-stu-id="b2e40-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="b2e40-149">Umožňuje integraci iis.</span><span class="sxs-lookup"><span data-stu-id="b2e40-149">Enables IIS integration.</span></span> <span data-ttu-id="b2e40-150">Výchozí možnosti iis <xref:host-and-deploy/iis/index#iis-options>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="b2e40-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="b2e40-151">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a nastavení pro webové [aplikace](#settings-for-web-apps) části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="b2e40-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b2e40-152">Rámcové služby</span><span class="sxs-lookup"><span data-stu-id="b2e40-152">Framework-provided services</span></span>

<span data-ttu-id="b2e40-153">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="b2e40-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="b2e40-154">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="b2e40-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="b2e40-155">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="b2e40-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="b2e40-156">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="b2e40-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="b2e40-157">Další informace o službách poskytovaných <xref:fundamentals/dependency-injection#framework-provided-services>rámcem naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="b2e40-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="b2e40-158">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="b2e40-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="b2e40-159"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Vstříkněte `IApplicationLifetime`(dříve) službu do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="b2e40-160">Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci metod spuštění aplikace a zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="b2e40-161">Rozhraní také obsahuje `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="b2e40-162">Následující příklad je `IHostedService` implementace, `IHostApplicationLifetime` která registruje události:</span><span class="sxs-lookup"><span data-stu-id="b2e40-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="b2e40-163">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="b2e40-163">IHostLifetime</span></span>

<span data-ttu-id="b2e40-164">Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> řídí při spuštění hostitele a při jeho zastavení.</span><span class="sxs-lookup"><span data-stu-id="b2e40-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="b2e40-165">Používá se poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-165">The last implementation registered is used.</span></span>

<span data-ttu-id="b2e40-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="b2e40-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="b2e40-168">Naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="b2e40-169">Odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="b2e40-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="b2e40-170">IHostŽivotní prostředí</span><span class="sxs-lookup"><span data-stu-id="b2e40-170">IHostEnvironment</span></span>

<span data-ttu-id="b2e40-171">Vložte <xref:Microsoft.Extensions.Hosting.IHostEnvironment> službu do třídy, abyste získali informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="b2e40-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="b2e40-172">Název_aplikace</span><span class="sxs-lookup"><span data-stu-id="b2e40-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="b2e40-173">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="b2e40-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="b2e40-174">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="b2e40-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="b2e40-175">Webové aplikace `IWebHostEnvironment` implementují rozhraní, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="b2e40-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="b2e40-176">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="b2e40-176">Host configuration</span></span>

<span data-ttu-id="b2e40-177">Konfigurace hostitele se používá pro <xref:Microsoft.Extensions.Hosting.IHostEnvironment> vlastnosti implementace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="b2e40-178">Konfigurace hostitele je k dispozici z <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř .</span><span class="sxs-lookup"><span data-stu-id="b2e40-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="b2e40-179">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , je nahrazen a konfigurátor aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="b2e40-180">Chcete-li přidat <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> konfiguraci hostitele, volejte na . `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="b2e40-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="b2e40-181">`ConfigureHostConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="b2e40-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="b2e40-182">Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="b2e40-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="b2e40-183">Zprostředkovatel proměnných prostředí `DOTNET_` s argumenty předpony a `CreateDefaultBuilder`příkazového řádku jsou zahrnuty do .</span><span class="sxs-lookup"><span data-stu-id="b2e40-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b2e40-184">Pro webové aplikace je přidán poskytovatel `ASPNETCORE_` proměnných prostředí s předponou.</span><span class="sxs-lookup"><span data-stu-id="b2e40-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="b2e40-185">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="b2e40-186">Například hodnota proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se stane `environment` hodnotou konfigurace hostitele pro klíč.</span><span class="sxs-lookup"><span data-stu-id="b2e40-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="b2e40-187">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="b2e40-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="b2e40-188">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="b2e40-188">App configuration</span></span>

<span data-ttu-id="b2e40-189">Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří `IHostBuilder`voláním na .</span><span class="sxs-lookup"><span data-stu-id="b2e40-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="b2e40-190">`ConfigureAppConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="b2e40-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="b2e40-191">Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="b2e40-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="b2e40-192">Konfigurace vytvořená `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako služba z DI.</span><span class="sxs-lookup"><span data-stu-id="b2e40-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="b2e40-193">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="b2e40-194">Další informace naleznete [v tématu Konfigurace v ASP.NET jádru](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="b2e40-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="b2e40-195">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="b2e40-195">Settings for all app types</span></span>

<span data-ttu-id="b2e40-196">V této části jsou uvedena nastavení hostitele, která platí pro úlohy protokolu HTTP i jiné než http.</span><span class="sxs-lookup"><span data-stu-id="b2e40-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="b2e40-197">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="b2e40-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="b2e40-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="b2e40-198">ApplicationName</span></span>

<span data-ttu-id="b2e40-199">Vlastnost [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele.</span><span class="sxs-lookup"><span data-stu-id="b2e40-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="b2e40-200">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="b2e40-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="b2e40-201">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-201">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-202">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="b2e40-203">**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="b2e40-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="b2e40-204">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="b2e40-205">Kořenová složka obsahu</span><span class="sxs-lookup"><span data-stu-id="b2e40-205">ContentRoot</span></span>

<span data-ttu-id="b2e40-206">Vlastnost [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="b2e40-207">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="b2e40-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="b2e40-208">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="b2e40-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="b2e40-209">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-209">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-210">**Výchozí**: Složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="b2e40-211">**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="b2e40-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="b2e40-212">Chcete-li nastavit tuto hodnotu, `UseContentRoot` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="b2e40-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="b2e40-213">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="b2e40-213">For more information, see:</span></span>

* [<span data-ttu-id="b2e40-214">Základy: Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="b2e40-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="b2e40-215">Webroot</span><span class="sxs-lookup"><span data-stu-id="b2e40-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="b2e40-216">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="b2e40-216">EnvironmentName</span></span>

<span data-ttu-id="b2e40-217">Vlastnost [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="b2e40-218">Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a .</span><span class="sxs-lookup"><span data-stu-id="b2e40-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="b2e40-219">Hodnoty nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b2e40-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="b2e40-220">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="b2e40-220">**Key**: `environment`</span></span>  
<span data-ttu-id="b2e40-221">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-221">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-222">**Výchozí :**`Production`</span><span class="sxs-lookup"><span data-stu-id="b2e40-222">**Default**: `Production`</span></span>  
<span data-ttu-id="b2e40-223">**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="b2e40-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="b2e40-224">Chcete-li nastavit tuto hodnotu, `UseEnvironment` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="b2e40-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="b2e40-225">Timeout vypnutí</span><span class="sxs-lookup"><span data-stu-id="b2e40-225">ShutdownTimeout</span></span>

<span data-ttu-id="b2e40-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>výtok pro .</span><span class="sxs-lookup"><span data-stu-id="b2e40-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="b2e40-227">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="b2e40-227">The default value is five seconds.</span></span>  <span data-ttu-id="b2e40-228">Během časového období hostitel:</span><span class="sxs-lookup"><span data-stu-id="b2e40-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="b2e40-229">Aktivuje [iHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="b2e40-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="b2e40-230">Pokusí se zastavit hostované služby, protokolování chyb pro služby, které se nepodaří zastavit.</span><span class="sxs-lookup"><span data-stu-id="b2e40-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="b2e40-231">Pokud vyprší časový limit před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="b2e40-232">Služby se zastaví, i když ještě nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="b2e40-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="b2e40-233">Pokud služby vyžadují další čas k zastavení, zvyšte časový čas.</span><span class="sxs-lookup"><span data-stu-id="b2e40-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="b2e40-234">**Klíč**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="b2e40-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="b2e40-235">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="b2e40-235">**Type**: `int`</span></span>  
<span data-ttu-id="b2e40-236">**Výchozí**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="b2e40-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="b2e40-237">**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="b2e40-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="b2e40-238">Chcete-li tuto hodnotu nastavit, `HostOptions`použijte proměnnou prostředí nebo nakonfigurujte .</span><span class="sxs-lookup"><span data-stu-id="b2e40-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="b2e40-239">Následující příklad nastaví časový čas na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="b2e40-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="b2e40-240">Nastavení webových aplikací</span><span class="sxs-lookup"><span data-stu-id="b2e40-240">Settings for web apps</span></span>

<span data-ttu-id="b2e40-241">Některá nastavení hostitele platí pouze pro úlohy PROTOKOLU HTTP.</span><span class="sxs-lookup"><span data-stu-id="b2e40-241">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="b2e40-242">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="b2e40-242">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="b2e40-243">Pro tato `IWebHostBuilder` nastavení jsou k dispozici metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="b2e40-243">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="b2e40-244">Ukázky kódu, které ukazují, `webBuilder` jak volat `IWebHostBuilder`metody rozšíření assume je instance , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b2e40-244">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="b2e40-245">Chyby po spuštění systému Capture</span><span class="sxs-lookup"><span data-stu-id="b2e40-245">CaptureStartupErrors</span></span>

<span data-ttu-id="b2e40-246">Když `false`, chyby při spuštění za následek ukončení hostitele.</span><span class="sxs-lookup"><span data-stu-id="b2e40-246">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="b2e40-247">Když `true`hostitel zachytí výjimky při spuštění a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="b2e40-247">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="b2e40-248">**Klíč**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="b2e40-248">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="b2e40-249">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="b2e40-249">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b2e40-250">**Výchozí**: Výchozí `false` nastavení, pokud aplikace běží s Kestrel `true`za IIS, kde je výchozí .</span><span class="sxs-lookup"><span data-stu-id="b2e40-250">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="b2e40-251">**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="b2e40-251">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="b2e40-252">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-252">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="b2e40-253">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="b2e40-253">DetailedErrors</span></span>

<span data-ttu-id="b2e40-254">Pokud je povoleno nebo `Development`pokud je prostředí , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="b2e40-254">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="b2e40-255">**Klíč**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="b2e40-255">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="b2e40-256">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="b2e40-256">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b2e40-257">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="b2e40-257">**Default**: `false`</span></span>  
<span data-ttu-id="b2e40-258">**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="b2e40-258">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="b2e40-259">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-259">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="b2e40-260">Hostovánístartupových sestavení</span><span class="sxs-lookup"><span data-stu-id="b2e40-260">HostingStartupAssemblies</span></span>

<span data-ttu-id="b2e40-261">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který se načte při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b2e40-261">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="b2e40-262">Přestože hodnota konfigurace výchozí prázdný řetězec, hostování spouštěcí sestavení vždy součástí sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-262">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="b2e40-263">Když jsou k dispozici hostování spouštěcísestavení, jsou přidány do sestavení aplikace pro načtení při aplikaci vytváří své běžné služby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b2e40-263">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="b2e40-264">**Klíč**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="b2e40-264">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="b2e40-265">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-265">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-266">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="b2e40-266">**Default**: Empty string</span></span>  
<span data-ttu-id="b2e40-267">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="b2e40-267">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="b2e40-268">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-268">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="b2e40-269">HostováníStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="b2e40-269">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="b2e40-270">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který má být při spuštění vyloučen.</span><span class="sxs-lookup"><span data-stu-id="b2e40-270">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="b2e40-271">**Klíč**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="b2e40-271">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="b2e40-272">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-272">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-273">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="b2e40-273">**Default**: Empty string</span></span>  
<span data-ttu-id="b2e40-274">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="b2e40-274">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="b2e40-275">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-275">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="b2e40-276">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="b2e40-276">HTTPS_Port</span></span>

<span data-ttu-id="b2e40-277">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b2e40-277">The HTTPS redirect port.</span></span> <span data-ttu-id="b2e40-278">Používá se při [vynucování protokolu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="b2e40-278">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="b2e40-279">**Klíč**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="b2e40-279">**Key**: `https_port`</span></span>  
<span data-ttu-id="b2e40-280">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-280">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-281">**Výchozí**: Výchozí hodnota není nastavena.</span><span class="sxs-lookup"><span data-stu-id="b2e40-281">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="b2e40-282">**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="b2e40-282">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="b2e40-283">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-283">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="b2e40-284">PreferhostingUrls</span><span class="sxs-lookup"><span data-stu-id="b2e40-284">PreferHostingUrls</span></span>

<span data-ttu-id="b2e40-285">Označuje, zda má hostitel naslouchat adresám `IWebHostBuilder` URL nakonfigurovaným s `IServer` namísto adres URL nakonfigurovaných s implementací.</span><span class="sxs-lookup"><span data-stu-id="b2e40-285">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="b2e40-286">**Klíč**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="b2e40-286">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="b2e40-287">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="b2e40-287">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b2e40-288">**Výchozí :**`true`</span><span class="sxs-lookup"><span data-stu-id="b2e40-288">**Default**: `true`</span></span>  
<span data-ttu-id="b2e40-289">**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="b2e40-289">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="b2e40-290">Chcete-li nastavit tuto hodnotu, `PreferHostingUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="b2e40-290">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="b2e40-291">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="b2e40-291">PreventHostingStartup</span></span>

<span data-ttu-id="b2e40-292">Zabraňuje automatickému načítání hostitelských spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-292">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="b2e40-293">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b2e40-293">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="b2e40-294">**Klíč**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="b2e40-294">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="b2e40-295">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="b2e40-295">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b2e40-296">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="b2e40-296">**Default**: `false`</span></span>  
<span data-ttu-id="b2e40-297">**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="b2e40-297">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="b2e40-298">Chcete-li nastavit tuto hodnotu, `UseSetting` použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="b2e40-298">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="b2e40-299">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="b2e40-299">StartupAssembly</span></span>

<span data-ttu-id="b2e40-300">Sestavení k hledání `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="b2e40-300">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="b2e40-301">**Klíč**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="b2e40-301">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="b2e40-302">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-302">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-303">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="b2e40-303">**Default**: The app's assembly</span></span>  
<span data-ttu-id="b2e40-304">**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="b2e40-304">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="b2e40-305">Chcete-li nastavit tuto hodnotu, `UseStartup`použijte proměnnou prostředí nebo volání .</span><span class="sxs-lookup"><span data-stu-id="b2e40-305">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="b2e40-306">`UseStartup`může mít název`string`sestavení ( )`TStartup`nebo typ ( ).</span><span class="sxs-lookup"><span data-stu-id="b2e40-306">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="b2e40-307">Pokud `UseStartup` je voláno více metod, má přednost poslední metoda.</span><span class="sxs-lookup"><span data-stu-id="b2e40-307">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="b2e40-308">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="b2e40-308">URLs</span></span>

<span data-ttu-id="b2e40-309">Seznam adres IP oddělených středníkem nebo hostitelských adres s porty a protokoly, které by měl server naslouchat požadavkům.</span><span class="sxs-lookup"><span data-stu-id="b2e40-309">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="b2e40-310">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="b2e40-310">For example, `http://localhost:123`.</span></span> <span data-ttu-id="b2e40-311">Pomocí\*" " označuje, že server by měl naslouchat požadavkům na libovolnou adresu IP `http://*:5000`nebo název hostitele pomocí zadaného portu a protokolu (například ).</span><span class="sxs-lookup"><span data-stu-id="b2e40-311">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="b2e40-312">Protokol (`http://` `https://`nebo ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="b2e40-312">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="b2e40-313">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="b2e40-313">Supported formats vary among servers.</span></span>

<span data-ttu-id="b2e40-314">**Klíč**:`urls`</span><span class="sxs-lookup"><span data-stu-id="b2e40-314">**Key**: `urls`</span></span>  
<span data-ttu-id="b2e40-315">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-315">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-316">**Výchozí** `http://localhost:5000` : a`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="b2e40-316">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="b2e40-317">**Proměnná prostředí**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="b2e40-317">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="b2e40-318">Chcete-li nastavit tuto hodnotu, `UseUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="b2e40-318">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="b2e40-319">Kestrel má vlastní konfigurační rozhraní API koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-319">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="b2e40-320">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b2e40-320">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="b2e40-321">Webroot</span><span class="sxs-lookup"><span data-stu-id="b2e40-321">WebRoot</span></span>

<span data-ttu-id="b2e40-322">Vlastnost [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) určuje relativní cestu ke statickým prostředkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-322">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="b2e40-323">Pokud cesta neexistuje, použije se zprostředkovatel souboru bez operace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-323">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="b2e40-324">**Klíč**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="b2e40-324">**Key**: `webroot`</span></span>  
<span data-ttu-id="b2e40-325">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-325">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-326">**Výchozí**: Výchozí `wwwroot`hodnota je .</span><span class="sxs-lookup"><span data-stu-id="b2e40-326">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="b2e40-327">Cesta k *kořenovému adresáři obsahu}/www root* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="b2e40-327">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="b2e40-328">**Proměnná prostředí**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="b2e40-328">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="b2e40-329">Chcete-li nastavit tuto hodnotu, `UseWebRoot` `IWebHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="b2e40-329">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="b2e40-330">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="b2e40-330">For more information, see:</span></span>

* [<span data-ttu-id="b2e40-331">Základy: Kořen webu</span><span class="sxs-lookup"><span data-stu-id="b2e40-331">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="b2e40-332">Kořenová složka obsahu</span><span class="sxs-lookup"><span data-stu-id="b2e40-332">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="b2e40-333">Správa doby trvání hostitele</span><span class="sxs-lookup"><span data-stu-id="b2e40-333">Manage the host lifetime</span></span>

<span data-ttu-id="b2e40-334">Volání metody na <xref:Microsoft.Extensions.Hosting.IHost> vytvořené implementace spustit a zastavit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b2e40-334">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="b2e40-335">Tyto metody <xref:Microsoft.Extensions.Hosting.IHostedService> ovlivňují všechny implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b2e40-335">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="b2e40-336">Spusťte</span><span class="sxs-lookup"><span data-stu-id="b2e40-336">Run</span></span>

<span data-ttu-id="b2e40-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne.</span><span class="sxs-lookup"><span data-stu-id="b2e40-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="b2e40-338">Synchronizace runasync</span><span class="sxs-lookup"><span data-stu-id="b2e40-338">RunAsync</span></span>

<span data-ttu-id="b2e40-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="b2e40-340">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="b2e40-340">RunConsoleAsync</span></span>

<span data-ttu-id="b2e40-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="b2e40-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="b2e40-342">Spustit</span><span class="sxs-lookup"><span data-stu-id="b2e40-342">Start</span></span>

<span data-ttu-id="b2e40-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="b2e40-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="b2e40-344">Spuštění synchronizace</span><span class="sxs-lookup"><span data-stu-id="b2e40-344">StartAsync</span></span>

<span data-ttu-id="b2e40-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="b2e40-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na `StartAsync`začátku , který čeká, až bude dokončena před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="b2e40-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="b2e40-347">To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-347">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="b2e40-348">StopAsync</span><span class="sxs-lookup"><span data-stu-id="b2e40-348">StopAsync</span></span>

<span data-ttu-id="b2e40-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.</span><span class="sxs-lookup"><span data-stu-id="b2e40-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="b2e40-350">Čekání na vypnutí</span><span class="sxs-lookup"><span data-stu-id="b2e40-350">WaitForShutdown</span></span>

<span data-ttu-id="b2e40-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud vypnutí je spuštěna IHostLifetime, například přes <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="b2e40-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="b2e40-352">Čekánínavypnutíasynchroniku</span><span class="sxs-lookup"><span data-stu-id="b2e40-352">WaitForShutdownAsync</span></span>

<span data-ttu-id="b2e40-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .</span><span class="sxs-lookup"><span data-stu-id="b2e40-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="b2e40-354">Externí ovládání</span><span class="sxs-lookup"><span data-stu-id="b2e40-354">External control</span></span>

<span data-ttu-id="b2e40-355">Příméřízení životnosti hostitele lze dosáhnout pomocí metod, které lze volat externě:</span><span class="sxs-lookup"><span data-stu-id="b2e40-355">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="b2e40-356">ASP.NET aplikace Core nakonfigurují a spouštějí hostitele.</span><span class="sxs-lookup"><span data-stu-id="b2e40-356">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="b2e40-357">Hostitel je zodpovědný za spuštění aplikace a správu životnosti.</span><span class="sxs-lookup"><span data-stu-id="b2e40-357">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="b2e40-358">Tento článek popisuje ASP.NET základní<xref:Microsoft.Extensions.Hosting.HostBuilder>obecný hostitel ( ), který se používá pro aplikace, které nezpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="b2e40-358">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="b2e40-359">Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele a povolit tak širší škálu hostitelských scénářů.</span><span class="sxs-lookup"><span data-stu-id="b2e40-359">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="b2e40-360">Zasílání zpráv, úlohy na pozadí a další úlohy bez protokolu HTTP založené na obecném hostiteli těží z průřezových funkcí, jako je konfigurace, vkládání závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="b2e40-360">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="b2e40-361">Obecný hostitel je nový v ASP.NET Core 2.1 a není vhodný pro scénáře webhostingu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-361">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="b2e40-362">Pro scénáře webhostingu použijte [webový hostitel](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="b2e40-362">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="b2e40-363">Obecný hostitel nahradí webhostingu v budoucí verzi a bude fungovat jako primární rozhraní API hostitele ve scénářích HTTP i bez protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b2e40-363">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="b2e40-364">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b2e40-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b2e40-365">Při spuštění ukázkové aplikace v [kódu Visual Studia](https://code.visualstudio.com/)použijte externí nebo integrovaný *terminál*.</span><span class="sxs-lookup"><span data-stu-id="b2e40-365">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="b2e40-366">Nespouštějte ukázku `internalConsole`v .</span><span class="sxs-lookup"><span data-stu-id="b2e40-366">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="b2e40-367">Nastavení konzoly v kódu sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b2e40-367">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="b2e40-368">Otevřete soubor *.vscode/launch.json.*</span><span class="sxs-lookup"><span data-stu-id="b2e40-368">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="b2e40-369">V konfiguraci **spuštění jádra .NET (konzola)** vyhledejte položku **konzoly.**</span><span class="sxs-lookup"><span data-stu-id="b2e40-369">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="b2e40-370">Nastavte hodnotu `externalTerminal` na `integratedTerminal`buď nebo .</span><span class="sxs-lookup"><span data-stu-id="b2e40-370">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="b2e40-371">Úvod</span><span class="sxs-lookup"><span data-stu-id="b2e40-371">Introduction</span></span>

<span data-ttu-id="b2e40-372">Knihovna obecného hostitele <xref:Microsoft.Extensions.Hosting> je k dispozici v oboru názvů a je k dispozici v balíčku [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/)</span><span class="sxs-lookup"><span data-stu-id="b2e40-372">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="b2e40-373">Balíček `Microsoft.Extensions.Hosting` je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="b2e40-373">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="b2e40-374"><xref:Microsoft.Extensions.Hosting.IHostedService>je vstupní bod pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-374"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="b2e40-375">Každá <xref:Microsoft.Extensions.Hosting.IHostedService> implementace je spuštěna v pořadí [registrace služby v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="b2e40-375">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="b2e40-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>je volána <xref:Microsoft.Extensions.Hosting.IHostedService> na každý při <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> spuštění hostitele a je volána v pořadí reverzní registrace při hostování řádně vypne.</span><span class="sxs-lookup"><span data-stu-id="b2e40-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="b2e40-377">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="b2e40-377">Set up a host</span></span>

<span data-ttu-id="b2e40-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder>je hlavní komponenta, kterou knihovny a aplikace používají k inicializaci, sestavení a spuštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="b2e40-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="b2e40-379">Možnosti</span><span class="sxs-lookup"><span data-stu-id="b2e40-379">Options</span></span>

<span data-ttu-id="b2e40-380"><xref:Microsoft.Extensions.Hosting.HostOptions>konfigurovat možnosti <xref:Microsoft.Extensions.Hosting.IHost>pro rozhraní .</span><span class="sxs-lookup"><span data-stu-id="b2e40-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="b2e40-381">Časový čas vypnutí</span><span class="sxs-lookup"><span data-stu-id="b2e40-381">Shutdown timeout</span></span>

<span data-ttu-id="b2e40-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>čas pro .</span><span class="sxs-lookup"><span data-stu-id="b2e40-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="b2e40-383">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="b2e40-383">The default value is five seconds.</span></span>

<span data-ttu-id="b2e40-384">Následující konfigurace možností `Program.Main` zvyšuje výchozí pětisekundový časový limit vypnutí na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="b2e40-384">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="b2e40-385">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="b2e40-385">Default services</span></span>

<span data-ttu-id="b2e40-386">Následující služby jsou registrovány během inicializace hostitele:</span><span class="sxs-lookup"><span data-stu-id="b2e40-386">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="b2e40-387">[Životní](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>prostředí ( )</span><span class="sxs-lookup"><span data-stu-id="b2e40-387">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="b2e40-388">[Konfigurace](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )</span><span class="sxs-lookup"><span data-stu-id="b2e40-388">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="b2e40-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="b2e40-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="b2e40-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="b2e40-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="b2e40-391">[Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="b2e40-391">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="b2e40-392">[Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="b2e40-392">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="b2e40-393">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="b2e40-393">Host configuration</span></span>

<span data-ttu-id="b2e40-394">Konfigurace hostitele je vytvořena:</span><span class="sxs-lookup"><span data-stu-id="b2e40-394">Host configuration is created by:</span></span>

* <span data-ttu-id="b2e40-395">Volání metody <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozšíření na nastavení [kořenového obsahu](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="b2e40-395">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="b2e40-396">Čtení konfigurace od <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>zprostředkovatelů konfigurace v .</span><span class="sxs-lookup"><span data-stu-id="b2e40-396">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="b2e40-397">Metody rozšíření</span><span class="sxs-lookup"><span data-stu-id="b2e40-397">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="b2e40-398">Aplikační klíč (název)</span><span class="sxs-lookup"><span data-stu-id="b2e40-398">Application key (name)</span></span>

<span data-ttu-id="b2e40-399">Vlastnost [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele.</span><span class="sxs-lookup"><span data-stu-id="b2e40-399">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="b2e40-400">Chcete-li explicitně nastavit hodnotu, použijte [klíč HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="b2e40-400">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="b2e40-401">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="b2e40-401">**Key**: `applicationName`</span></span>  
<span data-ttu-id="b2e40-402">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-402">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-403">**Výchozí**: Název sestavení obsahujícího vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-403">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="b2e40-404">**Nastavit pomocí**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="b2e40-404">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="b2e40-405">**Proměnná** `<PREFIX_>APPLICATIONNAME` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="b2e40-405">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="b2e40-406">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="b2e40-406">Content root</span></span>

<span data-ttu-id="b2e40-407">Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-407">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="b2e40-408">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="b2e40-408">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="b2e40-409">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-409">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-410">**Výchozí**: Výchozí je složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-410">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="b2e40-411">**Nastavit pomocí**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="b2e40-411">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="b2e40-412">**Proměnná** `<PREFIX_>CONTENTROOT` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="b2e40-412">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="b2e40-413">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="b2e40-413">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="b2e40-414">Další informace naleznete [v tématu Základy: Kořen obsahu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="b2e40-414">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="b2e40-415">Prostředí</span><span class="sxs-lookup"><span data-stu-id="b2e40-415">Environment</span></span>

<span data-ttu-id="b2e40-416">Nastaví [prostředí](xref:fundamentals/environments)aplikace .</span><span class="sxs-lookup"><span data-stu-id="b2e40-416">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b2e40-417">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="b2e40-417">**Key**: `environment`</span></span>  
<span data-ttu-id="b2e40-418">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-418">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-419">**Výchozí :**`Production`</span><span class="sxs-lookup"><span data-stu-id="b2e40-419">**Default**: `Production`</span></span>  
<span data-ttu-id="b2e40-420">**Nastavit pomocí**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="b2e40-420">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="b2e40-421">**Proměnná** `<PREFIX_>ENVIRONMENT` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="b2e40-421">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="b2e40-422">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-422">The environment can be set to any value.</span></span> <span data-ttu-id="b2e40-423">Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a .</span><span class="sxs-lookup"><span data-stu-id="b2e40-423">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="b2e40-424">Hodnoty nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b2e40-424">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="b2e40-425">Konfiguracekonfigurace host</span><span class="sxs-lookup"><span data-stu-id="b2e40-425">ConfigureHostConfiguration</span></span>

<span data-ttu-id="b2e40-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> an k <xref:Microsoft.Extensions.Configuration.IConfiguration> vytvoření hostitele.</span><span class="sxs-lookup"><span data-stu-id="b2e40-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="b2e40-427">Konfigurace hostitele se používá k <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> inicializaci pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-427">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="b2e40-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="b2e40-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="b2e40-429">Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="b2e40-429">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="b2e40-430">Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé.</span><span class="sxs-lookup"><span data-stu-id="b2e40-430">No providers are included by default.</span></span> <span data-ttu-id="b2e40-431">Je nutné explicitně zadat, bez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>ohledu na to, jaké zprostředkovatele konfigurace aplikace vyžaduje v aplikaci , včetně:</span><span class="sxs-lookup"><span data-stu-id="b2e40-431">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="b2e40-432">Konfigurace souboru (například ze souboru *hostsettings.json).*</span><span class="sxs-lookup"><span data-stu-id="b2e40-432">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="b2e40-433">Konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-433">Environment variable configuration.</span></span>
* <span data-ttu-id="b2e40-434">Konfigurace argumentu příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b2e40-434">Command-line argument configuration.</span></span>
* <span data-ttu-id="b2e40-435">Jakékoli další požadované zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-435">Any other required configuration providers.</span></span>

<span data-ttu-id="b2e40-436">Konfigurace souboru hostitele je povolena zadáním základní `SetBasePath` cesty aplikace s následovaným voláním jednoho z [poskytovatelů konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b2e40-436">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="b2e40-437">Ukázková aplikace používá soubor JSON, *hostsettings.json*a volání <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> využívat nastavení konfigurace hostitele souboru.</span><span class="sxs-lookup"><span data-stu-id="b2e40-437">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="b2e40-438">Chcete-li přidat [konfiguraci proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="b2e40-438">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="b2e40-439">`AddEnvironmentVariables`přijme volitelnou uživatelsky definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-439">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="b2e40-440">Ukázková aplikace používá předponu `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="b2e40-440">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="b2e40-441">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-441">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="b2e40-442">Když je nakonfigurován hostitel ukázkové aplikace, `PREFIX_ENVIRONMENT` hodnota proměnné prostředí pro `environment` se stane hodnotou konfigurace hostitele pro klíč.</span><span class="sxs-lookup"><span data-stu-id="b2e40-442">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="b2e40-443">Během vývoje při použití sady Visual `dotnet run` [Studio](https://visualstudio.microsoft.com) nebo spuštění aplikace s aplikací mohou být proměnné prostředí nastaveny v souboru *Vlastnosti/spuštěníNastavení.json.*</span><span class="sxs-lookup"><span data-stu-id="b2e40-443">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="b2e40-444">V [kódu sady Visual Studio](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *.vscode/launch.json* během vývoje.</span><span class="sxs-lookup"><span data-stu-id="b2e40-444">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="b2e40-445">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b2e40-445">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b2e40-446">[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>přidána voláním .</span><span class="sxs-lookup"><span data-stu-id="b2e40-446">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="b2e40-447">Konfigurace příkazového řádku je přidána jako poslední, aby bylo možné přepsat konfiguraci poskytovanou staršími poskytovateli konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-447">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="b2e40-448">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b2e40-448">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="b2e40-449">Další konfiguraci lze poskytnout s [applicationName](#application-key-name) a [contentRoot](#content-root) klíče.</span><span class="sxs-lookup"><span data-stu-id="b2e40-449">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="b2e40-450">Příklad `HostBuilder` konfigurace <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>pomocí :</span><span class="sxs-lookup"><span data-stu-id="b2e40-450">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="b2e40-451">Konfigurace konfigurace</span><span class="sxs-lookup"><span data-stu-id="b2e40-451">ConfigureAppConfiguration</span></span>

<span data-ttu-id="b2e40-452">Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří <xref:Microsoft.Extensions.Hosting.IHostBuilder> voláním na implementaci.</span><span class="sxs-lookup"><span data-stu-id="b2e40-452">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="b2e40-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> an k <xref:Microsoft.Extensions.Configuration.IConfiguration> vytvoření aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="b2e40-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="b2e40-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="b2e40-455">Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="b2e40-455">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="b2e40-456">Konfigurace vytvořená <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uživatelem je k dispozici na adrese <xref:Microsoft.Extensions.Hosting.IHost.Services*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="b2e40-456">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="b2e40-457">Konfigurace aplikace automaticky přijímá konfiguraci hostitele poskytovanou [společností ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="b2e40-457">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="b2e40-458">Příklad konfigurace <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>aplikace pomocí :</span><span class="sxs-lookup"><span data-stu-id="b2e40-458">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="b2e40-459">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b2e40-459">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="b2e40-460">*nastavení aplikace. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="b2e40-460">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="b2e40-461">*nastavení aplikace. Production.json*:</span><span class="sxs-lookup"><span data-stu-id="b2e40-461">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="b2e40-462">Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-462">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="b2e40-463">Ukázková aplikace přesune soubory nastavení aplikace JSON a *hostsettings.json* s následující `<Content>` položkou:</span><span class="sxs-lookup"><span data-stu-id="b2e40-463">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="b2e40-464">Metody rozšíření konfigurace, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> jako jsou například a vyžadují další balíčky NuGet, například [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="b2e40-464">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="b2e40-465">Pokud aplikace nepoužívá [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), musí být tyto balíčky přidány do projektu kromě základního balíčku [Microsoft.Extensions.Configuration.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)</span><span class="sxs-lookup"><span data-stu-id="b2e40-465">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="b2e40-466">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b2e40-466">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="b2e40-467">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="b2e40-467">ConfigureServices</span></span>

<span data-ttu-id="b2e40-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b2e40-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="b2e40-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="b2e40-470">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b2e40-470">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="b2e40-471">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="b2e40-471">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="b2e40-472">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) `AddHostedService` používá metodu rozšíření k přidání `LifetimeEventsHostedService`služby pro události `TimedHostedService`životnosti a časované úlohy na pozadí , do aplikace:</span><span class="sxs-lookup"><span data-stu-id="b2e40-472">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="b2e40-473">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="b2e40-473">ConfigureLogging</span></span>

<span data-ttu-id="b2e40-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>přidá delegáta pro konfiguraci <xref:Microsoft.Extensions.Logging.ILoggingBuilder>poskytovaného .</span><span class="sxs-lookup"><span data-stu-id="b2e40-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="b2e40-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>mohou být volány vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="b2e40-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="b2e40-476">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="b2e40-476">UseConsoleLifetime</span></span>

<span data-ttu-id="b2e40-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="b2e40-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="b2e40-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="b2e40-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je předběžně zaregistrována jako výchozí implementace životnosti.</span><span class="sxs-lookup"><span data-stu-id="b2e40-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="b2e40-480">Používá se poslední registrovaná životnost.</span><span class="sxs-lookup"><span data-stu-id="b2e40-480">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="b2e40-481">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="b2e40-481">Container configuration</span></span>

<span data-ttu-id="b2e40-482">Pro podporu připojení jiných kontejnerů může <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>hostitel přijmout soubor .</span><span class="sxs-lookup"><span data-stu-id="b2e40-482">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="b2e40-483">Poskytování factory není součástí registrace kontejneru DI, ale je hostitele vnitřní slouží k vytvoření kontejneru betonové DI.</span><span class="sxs-lookup"><span data-stu-id="b2e40-483">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="b2e40-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder)&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepíše výchozí továrnu použitou k vytvoření poskytovatele služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="b2e40-485">Vlastní konfigurace kontejneru <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> je spravována metodou.</span><span class="sxs-lookup"><span data-stu-id="b2e40-485">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="b2e40-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>poskytuje prostředí silného typu pro konfiguraci kontejneru nad základní rozhraní API hostitele.</span><span class="sxs-lookup"><span data-stu-id="b2e40-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="b2e40-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="b2e40-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="b2e40-488">Vytvoření kontejneru služeb pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="b2e40-488">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="b2e40-489">Poskytněte továrnu na servisní kontejnery:</span><span class="sxs-lookup"><span data-stu-id="b2e40-489">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="b2e40-490">Použijte továrnu a nakonfigurujte vlastní kontejner služeb pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="b2e40-490">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="b2e40-491">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="b2e40-491">Extensibility</span></span>

<span data-ttu-id="b2e40-492">Rozšiřitelnost hostitele se provádí pomocí <xref:Microsoft.Extensions.Hosting.IHostBuilder>rozšiřujících metod na .</span><span class="sxs-lookup"><span data-stu-id="b2e40-492">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="b2e40-493">Následující příklad ukazuje, jak metoda <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozšíření rozšiřuje implementaci s [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) příklad demonstroval v <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="b2e40-493">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="b2e40-494">Aplikace vytvoří metodu `UseHostedService` rozšíření pro registraci hostované služby předané v `T`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-494">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="b2e40-495">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="b2e40-495">Manage the host</span></span>

<span data-ttu-id="b2e40-496">Implementace <xref:Microsoft.Extensions.Hosting.IHost> je zodpovědná za spuštění <xref:Microsoft.Extensions.Hosting.IHostedService> a zastavení implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b2e40-496">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="b2e40-497">Spusťte</span><span class="sxs-lookup"><span data-stu-id="b2e40-497">Run</span></span>

<span data-ttu-id="b2e40-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne:</span><span class="sxs-lookup"><span data-stu-id="b2e40-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="b2e40-499">Synchronizace runasync</span><span class="sxs-lookup"><span data-stu-id="b2e40-499">RunAsync</span></span>

<span data-ttu-id="b2e40-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, která se dokončí, když se aktivuje token zrušení nebo vypnutí:</span><span class="sxs-lookup"><span data-stu-id="b2e40-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="b2e40-501">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="b2e40-501">RunConsoleAsync</span></span>

<span data-ttu-id="b2e40-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="b2e40-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="b2e40-503">Spustit a zastavitsynchronizacu</span><span class="sxs-lookup"><span data-stu-id="b2e40-503">Start and StopAsync</span></span>

<span data-ttu-id="b2e40-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="b2e40-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="b2e40-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.</span><span class="sxs-lookup"><span data-stu-id="b2e40-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="b2e40-506">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="b2e40-506">StartAsync and StopAsync</span></span>

<span data-ttu-id="b2e40-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b2e40-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="b2e40-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zastaví aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b2e40-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="b2e40-509">Čekání na vypnutí</span><span class="sxs-lookup"><span data-stu-id="b2e40-509">WaitForShutdown</span></span>

<span data-ttu-id="b2e40-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>se spouští <xref:Microsoft.Extensions.Hosting.IHostLifetime>přes `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` , například (naslouchá <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="b2e40-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="b2e40-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="b2e40-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="b2e40-512">Čekánínavypnutíasynchroniku</span><span class="sxs-lookup"><span data-stu-id="b2e40-512">WaitForShutdownAsync</span></span>

<span data-ttu-id="b2e40-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .</span><span class="sxs-lookup"><span data-stu-id="b2e40-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="b2e40-514">Externí ovládání</span><span class="sxs-lookup"><span data-stu-id="b2e40-514">External control</span></span>

<span data-ttu-id="b2e40-515">Externího řízení hostitele lze dosáhnout pomocí metod, které lze volat externě:</span><span class="sxs-lookup"><span data-stu-id="b2e40-515">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="b2e40-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>začátku , který čeká, až bude dokončena před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="b2e40-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="b2e40-517">To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-517">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="b2e40-518">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="b2e40-518">IHostingEnvironment interface</span></span>

<span data-ttu-id="b2e40-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>poskytuje informace o hostitelském prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="b2e40-520">Použijte [vstřikování konstruktoru](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> k získání, abyste mohli používat jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b2e40-520">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="b2e40-521">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b2e40-521">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="b2e40-522">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="b2e40-522">IApplicationLifetime interface</span></span>

<span data-ttu-id="b2e40-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>umožňuje aktivity po spuštění a vypnutí, včetně řádného požadavku na vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="b2e40-524">Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-524">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="b2e40-525">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="b2e40-525">Cancellation Token</span></span> | <span data-ttu-id="b2e40-526">Aktivuje se při&#8230;</span><span class="sxs-lookup"><span data-stu-id="b2e40-526">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="b2e40-527">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="b2e40-527">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="b2e40-528">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-528">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="b2e40-529">Všechny požadavky by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="b2e40-529">All requests should be processed.</span></span> <span data-ttu-id="b2e40-530">Vypnutí blokuje, dokud tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-530">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="b2e40-531">Hostitel provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-531">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="b2e40-532">Požadavky mohou být stále zpracování.</span><span class="sxs-lookup"><span data-stu-id="b2e40-532">Requests may still be processing.</span></span> <span data-ttu-id="b2e40-533">Vypnutí blokuje, dokud tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-533">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="b2e40-534">Konstruktor vstříkne službu <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="b2e40-534">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="b2e40-535">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání `LifetimeEventsHostedService` konstruktoru <xref:Microsoft.Extensions.Hosting.IHostedService> do třídy (implementace) k registraci událostí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-535">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="b2e40-536">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="b2e40-536">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="b2e40-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>požaduje ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="b2e40-538">Následující třída <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> používá k řádnému vypnutí aplikace při `Shutdown` volání metody třídy:</span><span class="sxs-lookup"><span data-stu-id="b2e40-538">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b2e40-539">Šablony ASP.NET Core vytvoří obecný hostitel jádra .NET (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="b2e40-539">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="b2e40-540">Definice hostitele</span><span class="sxs-lookup"><span data-stu-id="b2e40-540">Host definition</span></span>

<span data-ttu-id="b2e40-541">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="b2e40-541">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="b2e40-542">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="b2e40-542">Dependency injection (DI)</span></span>
* <span data-ttu-id="b2e40-543">Protokolování</span><span class="sxs-lookup"><span data-stu-id="b2e40-543">Logging</span></span>
* <span data-ttu-id="b2e40-544">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="b2e40-544">Configuration</span></span>
* <span data-ttu-id="b2e40-545">`IHostedService`Implementace</span><span class="sxs-lookup"><span data-stu-id="b2e40-545">`IHostedService` implementations</span></span>

<span data-ttu-id="b2e40-546">Při spuštění hostitele volá `IHostedService.StartAsync` na každou <xref:Microsoft.Extensions.Hosting.IHostedService> implementaci, která najde v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="b2e40-546">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="b2e40-547">Ve webové aplikaci je `IHostedService` jednou z implementací webová služba, která spouští [implementaci http serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="b2e40-547">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="b2e40-548">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je správa životnosti: kontrola nad spuštěním aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-548">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="b2e40-549">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="b2e40-549">Set up a host</span></span>

<span data-ttu-id="b2e40-550">Hostitel je obvykle nakonfigurován, sestaven a spuštěn `Program` podle kódu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="b2e40-550">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="b2e40-551">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="b2e40-551">The `Main` method:</span></span>

* <span data-ttu-id="b2e40-552">Volá `CreateHostBuilder` metodu k vytvoření a konfiguraci objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="b2e40-552">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="b2e40-553">Volání `Build` `Run` a metody objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="b2e40-553">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="b2e40-554">Webové šablony ASP.NET Core generují následující kód pro vytvoření hostitele:</span><span class="sxs-lookup"><span data-stu-id="b2e40-554">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="b2e40-555">Následující kód vytvoří úlohu bez `IHostedService` protokolu HTTP s implementací přidanou do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="b2e40-555">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="b2e40-556">Pro úlohu HTTP `Main` je metoda `CreateHostBuilder` stejná, ale volá `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-556">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="b2e40-557">Pokud aplikace používá Core entity frameworku, neměňte `CreateHostBuilder` název nebo podpis metody.</span><span class="sxs-lookup"><span data-stu-id="b2e40-557">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="b2e40-558">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že najdou metodu, `CreateHostBuilder` která konfiguruje hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-558">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="b2e40-559">Další informace naleznete v [tématu Návrh-time DbContext Vytvoření](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="b2e40-559">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="b2e40-560">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="b2e40-560">Default builder settings</span></span>

<span data-ttu-id="b2e40-561">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="b2e40-561">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="b2e40-562">Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>cestu vrácenou programem .</span><span class="sxs-lookup"><span data-stu-id="b2e40-562">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="b2e40-563">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="b2e40-563">Loads host configuration from:</span></span>
  * <span data-ttu-id="b2e40-564">Proměnné prostředí s `DOTNET_`předponou .</span><span class="sxs-lookup"><span data-stu-id="b2e40-564">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="b2e40-565">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b2e40-565">Command-line arguments.</span></span>
* <span data-ttu-id="b2e40-566">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="b2e40-566">Loads app configuration from:</span></span>
  * <span data-ttu-id="b2e40-567">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b2e40-567">*appsettings.json*.</span></span>
  * <span data-ttu-id="b2e40-568">*nastavení aplikace. {Prostředí}.json*.</span><span class="sxs-lookup"><span data-stu-id="b2e40-568">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="b2e40-569">[Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-569">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="b2e40-570">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-570">Environment variables.</span></span>
  * <span data-ttu-id="b2e40-571">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b2e40-571">Command-line arguments.</span></span>
* <span data-ttu-id="b2e40-572">Přidá následující zprostředkovatele [protokolování:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="b2e40-572">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="b2e40-573">Konzola</span><span class="sxs-lookup"><span data-stu-id="b2e40-573">Console</span></span>
  * <span data-ttu-id="b2e40-574">Ladit</span><span class="sxs-lookup"><span data-stu-id="b2e40-574">Debug</span></span>
  * <span data-ttu-id="b2e40-575">EventSource</span><span class="sxs-lookup"><span data-stu-id="b2e40-575">EventSource</span></span>
  * <span data-ttu-id="b2e40-576">EventLog (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="b2e40-576">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="b2e40-577">Umožňuje [ověření oboru](xref:fundamentals/dependency-injection#scope-validation) a ověření [závislostí,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) když je prostředí Vývoj.</span><span class="sxs-lookup"><span data-stu-id="b2e40-577">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="b2e40-578">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="b2e40-578">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="b2e40-579">Načte konfiguraci hostitele z `ASPNETCORE_`proměnných prostředí s předponou .</span><span class="sxs-lookup"><span data-stu-id="b2e40-579">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="b2e40-580">Nastaví [server Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje jej pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-580">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="b2e40-581">Výchozí možnosti serveru Kestrel naleznete <xref:fundamentals/servers/kestrel#kestrel-options>v tématu .</span><span class="sxs-lookup"><span data-stu-id="b2e40-581">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="b2e40-582">Přidá [middleware filtrování hostitelů](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="b2e40-582">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="b2e40-583">Přidá [middleware s předaných záhlaví,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se rovná . `true`</span><span class="sxs-lookup"><span data-stu-id="b2e40-583">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="b2e40-584">Umožňuje integraci iis.</span><span class="sxs-lookup"><span data-stu-id="b2e40-584">Enables IIS integration.</span></span> <span data-ttu-id="b2e40-585">Výchozí možnosti iis <xref:host-and-deploy/iis/index#iis-options>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="b2e40-585">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="b2e40-586">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a nastavení pro webové [aplikace](#settings-for-web-apps) části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="b2e40-586">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b2e40-587">Rámcové služby</span><span class="sxs-lookup"><span data-stu-id="b2e40-587">Framework-provided services</span></span>

<span data-ttu-id="b2e40-588">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="b2e40-588">The following services are registered automatically:</span></span>

* [<span data-ttu-id="b2e40-589">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="b2e40-589">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="b2e40-590">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="b2e40-590">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="b2e40-591">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="b2e40-591">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="b2e40-592">Další informace o službách poskytovaných <xref:fundamentals/dependency-injection#framework-provided-services>rámcem naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="b2e40-592">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="b2e40-593">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="b2e40-593">IHostApplicationLifetime</span></span>

<span data-ttu-id="b2e40-594"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Vstříkněte `IApplicationLifetime`(dříve) službu do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-594">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="b2e40-595">Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci metod spuštění aplikace a zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-595">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="b2e40-596">Rozhraní také obsahuje `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-596">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="b2e40-597">Následující příklad je `IHostedService` implementace, `IHostApplicationLifetime` která registruje události:</span><span class="sxs-lookup"><span data-stu-id="b2e40-597">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="b2e40-598">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="b2e40-598">IHostLifetime</span></span>

<span data-ttu-id="b2e40-599">Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> řídí při spuštění hostitele a při jeho zastavení.</span><span class="sxs-lookup"><span data-stu-id="b2e40-599">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="b2e40-600">Používá se poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-600">The last implementation registered is used.</span></span>

<span data-ttu-id="b2e40-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="b2e40-602">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-602">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="b2e40-603">Naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-603">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="b2e40-604">Odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="b2e40-604">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="b2e40-605">IHostŽivotní prostředí</span><span class="sxs-lookup"><span data-stu-id="b2e40-605">IHostEnvironment</span></span>

<span data-ttu-id="b2e40-606">Vložte <xref:Microsoft.Extensions.Hosting.IHostEnvironment> službu do třídy, abyste získali informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="b2e40-606">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="b2e40-607">Název_aplikace</span><span class="sxs-lookup"><span data-stu-id="b2e40-607">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="b2e40-608">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="b2e40-608">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="b2e40-609">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="b2e40-609">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="b2e40-610">Webové aplikace `IWebHostEnvironment` implementují rozhraní, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="b2e40-610">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="b2e40-611">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="b2e40-611">Host configuration</span></span>

<span data-ttu-id="b2e40-612">Konfigurace hostitele se používá pro <xref:Microsoft.Extensions.Hosting.IHostEnvironment> vlastnosti implementace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-612">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="b2e40-613">Konfigurace hostitele je k dispozici z <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř .</span><span class="sxs-lookup"><span data-stu-id="b2e40-613">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="b2e40-614">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , je nahrazen a konfigurátor aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-614">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="b2e40-615">Chcete-li přidat <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> konfiguraci hostitele, volejte na . `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="b2e40-615">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="b2e40-616">`ConfigureHostConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="b2e40-616">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="b2e40-617">Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="b2e40-617">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="b2e40-618">Zprostředkovatel proměnných prostředí `DOTNET_` s argumenty předpony a `CreateDefaultBuilder`příkazového řádku jsou zahrnuty do .</span><span class="sxs-lookup"><span data-stu-id="b2e40-618">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b2e40-619">Pro webové aplikace je přidán poskytovatel `ASPNETCORE_` proměnných prostředí s předponou.</span><span class="sxs-lookup"><span data-stu-id="b2e40-619">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="b2e40-620">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-620">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="b2e40-621">Například hodnota proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se stane `environment` hodnotou konfigurace hostitele pro klíč.</span><span class="sxs-lookup"><span data-stu-id="b2e40-621">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="b2e40-622">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="b2e40-622">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="b2e40-623">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="b2e40-623">App configuration</span></span>

<span data-ttu-id="b2e40-624">Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří `IHostBuilder`voláním na .</span><span class="sxs-lookup"><span data-stu-id="b2e40-624">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="b2e40-625">`ConfigureAppConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="b2e40-625">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="b2e40-626">Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="b2e40-626">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="b2e40-627">Konfigurace vytvořená `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako služba z DI.</span><span class="sxs-lookup"><span data-stu-id="b2e40-627">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="b2e40-628">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-628">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="b2e40-629">Další informace naleznete [v tématu Konfigurace v ASP.NET jádru](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="b2e40-629">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="b2e40-630">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="b2e40-630">Settings for all app types</span></span>

<span data-ttu-id="b2e40-631">V této části jsou uvedena nastavení hostitele, která platí pro úlohy protokolu HTTP i jiné než http.</span><span class="sxs-lookup"><span data-stu-id="b2e40-631">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="b2e40-632">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="b2e40-632">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="b2e40-633">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="b2e40-633">ApplicationName</span></span>

<span data-ttu-id="b2e40-634">Vlastnost [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele.</span><span class="sxs-lookup"><span data-stu-id="b2e40-634">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="b2e40-635">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="b2e40-635">**Key**: `applicationName`</span></span>  
<span data-ttu-id="b2e40-636">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-636">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-637">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-637">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="b2e40-638">**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="b2e40-638">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="b2e40-639">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-639">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="b2e40-640">Kořenová složka obsahu</span><span class="sxs-lookup"><span data-stu-id="b2e40-640">ContentRoot</span></span>

<span data-ttu-id="b2e40-641">Vlastnost [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-641">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="b2e40-642">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="b2e40-642">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="b2e40-643">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="b2e40-643">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="b2e40-644">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-644">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-645">**Výchozí**: Složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-645">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="b2e40-646">**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="b2e40-646">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="b2e40-647">Chcete-li nastavit tuto hodnotu, `UseContentRoot` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="b2e40-647">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="b2e40-648">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="b2e40-648">For more information, see:</span></span>

* [<span data-ttu-id="b2e40-649">Základy: Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="b2e40-649">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="b2e40-650">Webroot</span><span class="sxs-lookup"><span data-stu-id="b2e40-650">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="b2e40-651">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="b2e40-651">EnvironmentName</span></span>

<span data-ttu-id="b2e40-652">Vlastnost [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-652">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="b2e40-653">Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a .</span><span class="sxs-lookup"><span data-stu-id="b2e40-653">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="b2e40-654">Hodnoty nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b2e40-654">Values aren't case-sensitive.</span></span>

<span data-ttu-id="b2e40-655">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="b2e40-655">**Key**: `environment`</span></span>  
<span data-ttu-id="b2e40-656">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-656">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-657">**Výchozí :**`Production`</span><span class="sxs-lookup"><span data-stu-id="b2e40-657">**Default**: `Production`</span></span>  
<span data-ttu-id="b2e40-658">**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="b2e40-658">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="b2e40-659">Chcete-li nastavit tuto hodnotu, `UseEnvironment` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="b2e40-659">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="b2e40-660">Timeout vypnutí</span><span class="sxs-lookup"><span data-stu-id="b2e40-660">ShutdownTimeout</span></span>

<span data-ttu-id="b2e40-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>výtok pro .</span><span class="sxs-lookup"><span data-stu-id="b2e40-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="b2e40-662">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="b2e40-662">The default value is five seconds.</span></span>  <span data-ttu-id="b2e40-663">Během časového období hostitel:</span><span class="sxs-lookup"><span data-stu-id="b2e40-663">During the timeout period, the host:</span></span>

* <span data-ttu-id="b2e40-664">Aktivuje [iHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="b2e40-664">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="b2e40-665">Pokusí se zastavit hostované služby, protokolování chyb pro služby, které se nepodaří zastavit.</span><span class="sxs-lookup"><span data-stu-id="b2e40-665">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="b2e40-666">Pokud vyprší časový limit před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-666">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="b2e40-667">Služby se zastaví, i když ještě nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="b2e40-667">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="b2e40-668">Pokud služby vyžadují další čas k zastavení, zvyšte časový čas.</span><span class="sxs-lookup"><span data-stu-id="b2e40-668">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="b2e40-669">**Klíč**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="b2e40-669">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="b2e40-670">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="b2e40-670">**Type**: `int`</span></span>  
<span data-ttu-id="b2e40-671">**Výchozí**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="b2e40-671">**Default**: 5 seconds</span></span>  
<span data-ttu-id="b2e40-672">**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="b2e40-672">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="b2e40-673">Chcete-li tuto hodnotu nastavit, `HostOptions`použijte proměnnou prostředí nebo nakonfigurujte .</span><span class="sxs-lookup"><span data-stu-id="b2e40-673">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="b2e40-674">Následující příklad nastaví časový čas na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="b2e40-674">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="b2e40-675">Zakázat opětovné načtení konfigurace aplikace při změně</span><span class="sxs-lookup"><span data-stu-id="b2e40-675">Disable app configuration reload on change</span></span>

<span data-ttu-id="b2e40-676">[Ve výchozím nastavení](xref:fundamentals/configuration/index#default), *appsettings.json* a *appsettings.{ Prostředí}.json* jsou znovu načteny při změně souboru.</span><span class="sxs-lookup"><span data-stu-id="b2e40-676">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="b2e40-677">Chcete-li zakázat toto chování opětovného načtení v `hostBuilder:reloadConfigOnChange` ASP.NET `false`Core 5.0 Preview 3 nebo novější, nastavte klíč na .</span><span class="sxs-lookup"><span data-stu-id="b2e40-677">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="b2e40-678">**Klíč**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="b2e40-678">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="b2e40-679">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="b2e40-679">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b2e40-680">**Výchozí :**`true`</span><span class="sxs-lookup"><span data-stu-id="b2e40-680">**Default**: `true`</span></span>  
<span data-ttu-id="b2e40-681">**Argument příkazového řádku**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="b2e40-681">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="b2e40-682">**Proměnná prostředí**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="b2e40-682">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="b2e40-683">Oddělovač`:`dvojtečky ( ) nefunguje s hierarchickými klíči proměnné prostředí na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="b2e40-683">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="b2e40-684">Další informace naleznete v tématu [Proměnné prostředí](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="b2e40-684">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="b2e40-685">Nastavení webových aplikací</span><span class="sxs-lookup"><span data-stu-id="b2e40-685">Settings for web apps</span></span>

<span data-ttu-id="b2e40-686">Některá nastavení hostitele platí pouze pro úlohy PROTOKOLU HTTP.</span><span class="sxs-lookup"><span data-stu-id="b2e40-686">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="b2e40-687">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="b2e40-687">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="b2e40-688">Pro tato `IWebHostBuilder` nastavení jsou k dispozici metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="b2e40-688">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="b2e40-689">Ukázky kódu, které ukazují, `webBuilder` jak volat `IWebHostBuilder`metody rozšíření assume je instance , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b2e40-689">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="b2e40-690">Chyby po spuštění systému Capture</span><span class="sxs-lookup"><span data-stu-id="b2e40-690">CaptureStartupErrors</span></span>

<span data-ttu-id="b2e40-691">Když `false`, chyby při spuštění za následek ukončení hostitele.</span><span class="sxs-lookup"><span data-stu-id="b2e40-691">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="b2e40-692">Když `true`hostitel zachytí výjimky při spuštění a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="b2e40-692">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="b2e40-693">**Klíč**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="b2e40-693">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="b2e40-694">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="b2e40-694">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b2e40-695">**Výchozí**: Výchozí `false` nastavení, pokud aplikace běží s Kestrel `true`za IIS, kde je výchozí .</span><span class="sxs-lookup"><span data-stu-id="b2e40-695">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="b2e40-696">**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="b2e40-696">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="b2e40-697">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-697">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="b2e40-698">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="b2e40-698">DetailedErrors</span></span>

<span data-ttu-id="b2e40-699">Pokud je povoleno nebo `Development`pokud je prostředí , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="b2e40-699">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="b2e40-700">**Klíč**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="b2e40-700">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="b2e40-701">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="b2e40-701">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b2e40-702">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="b2e40-702">**Default**: `false`</span></span>  
<span data-ttu-id="b2e40-703">**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="b2e40-703">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="b2e40-704">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-704">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="b2e40-705">Hostovánístartupových sestavení</span><span class="sxs-lookup"><span data-stu-id="b2e40-705">HostingStartupAssemblies</span></span>

<span data-ttu-id="b2e40-706">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který se načte při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b2e40-706">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="b2e40-707">Přestože hodnota konfigurace výchozí prázdný řetězec, hostování spouštěcí sestavení vždy součástí sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-707">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="b2e40-708">Když jsou k dispozici hostování spouštěcísestavení, jsou přidány do sestavení aplikace pro načtení při aplikaci vytváří své běžné služby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b2e40-708">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="b2e40-709">**Klíč**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="b2e40-709">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="b2e40-710">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-710">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-711">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="b2e40-711">**Default**: Empty string</span></span>  
<span data-ttu-id="b2e40-712">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="b2e40-712">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="b2e40-713">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-713">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="b2e40-714">HostováníStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="b2e40-714">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="b2e40-715">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který má být při spuštění vyloučen.</span><span class="sxs-lookup"><span data-stu-id="b2e40-715">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="b2e40-716">**Klíč**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="b2e40-716">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="b2e40-717">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-717">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-718">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="b2e40-718">**Default**: Empty string</span></span>  
<span data-ttu-id="b2e40-719">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="b2e40-719">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="b2e40-720">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-720">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="b2e40-721">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="b2e40-721">HTTPS_Port</span></span>

<span data-ttu-id="b2e40-722">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b2e40-722">The HTTPS redirect port.</span></span> <span data-ttu-id="b2e40-723">Používá se při [vynucování protokolu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="b2e40-723">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="b2e40-724">**Klíč**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="b2e40-724">**Key**: `https_port`</span></span>  
<span data-ttu-id="b2e40-725">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-725">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-726">**Výchozí**: Výchozí hodnota není nastavena.</span><span class="sxs-lookup"><span data-stu-id="b2e40-726">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="b2e40-727">**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="b2e40-727">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="b2e40-728">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="b2e40-728">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="b2e40-729">PreferhostingUrls</span><span class="sxs-lookup"><span data-stu-id="b2e40-729">PreferHostingUrls</span></span>

<span data-ttu-id="b2e40-730">Označuje, zda má hostitel naslouchat adresám `IWebHostBuilder` URL nakonfigurovaným s `IServer` namísto adres URL nakonfigurovaných s implementací.</span><span class="sxs-lookup"><span data-stu-id="b2e40-730">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="b2e40-731">**Klíč**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="b2e40-731">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="b2e40-732">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="b2e40-732">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b2e40-733">**Výchozí :**`true`</span><span class="sxs-lookup"><span data-stu-id="b2e40-733">**Default**: `true`</span></span>  
<span data-ttu-id="b2e40-734">**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="b2e40-734">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="b2e40-735">Chcete-li nastavit tuto hodnotu, `PreferHostingUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="b2e40-735">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="b2e40-736">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="b2e40-736">PreventHostingStartup</span></span>

<span data-ttu-id="b2e40-737">Zabraňuje automatickému načítání hostitelských spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-737">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="b2e40-738">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b2e40-738">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="b2e40-739">**Klíč**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="b2e40-739">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="b2e40-740">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="b2e40-740">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="b2e40-741">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="b2e40-741">**Default**: `false`</span></span>  
<span data-ttu-id="b2e40-742">**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="b2e40-742">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="b2e40-743">Chcete-li nastavit tuto hodnotu, `UseSetting` použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="b2e40-743">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="b2e40-744">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="b2e40-744">StartupAssembly</span></span>

<span data-ttu-id="b2e40-745">Sestavení k hledání `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="b2e40-745">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="b2e40-746">**Klíč**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="b2e40-746">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="b2e40-747">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-747">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-748">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="b2e40-748">**Default**: The app's assembly</span></span>  
<span data-ttu-id="b2e40-749">**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="b2e40-749">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="b2e40-750">Chcete-li nastavit tuto hodnotu, `UseStartup`použijte proměnnou prostředí nebo volání .</span><span class="sxs-lookup"><span data-stu-id="b2e40-750">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="b2e40-751">`UseStartup`může mít název`string`sestavení ( )`TStartup`nebo typ ( ).</span><span class="sxs-lookup"><span data-stu-id="b2e40-751">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="b2e40-752">Pokud `UseStartup` je voláno více metod, má přednost poslední metoda.</span><span class="sxs-lookup"><span data-stu-id="b2e40-752">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="b2e40-753">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="b2e40-753">URLs</span></span>

<span data-ttu-id="b2e40-754">Seznam adres IP oddělených středníkem nebo hostitelských adres s porty a protokoly, které by měl server naslouchat požadavkům.</span><span class="sxs-lookup"><span data-stu-id="b2e40-754">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="b2e40-755">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="b2e40-755">For example, `http://localhost:123`.</span></span> <span data-ttu-id="b2e40-756">Pomocí\*" " označuje, že server by měl naslouchat požadavkům na libovolnou adresu IP `http://*:5000`nebo název hostitele pomocí zadaného portu a protokolu (například ).</span><span class="sxs-lookup"><span data-stu-id="b2e40-756">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="b2e40-757">Protokol (`http://` `https://`nebo ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="b2e40-757">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="b2e40-758">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="b2e40-758">Supported formats vary among servers.</span></span>

<span data-ttu-id="b2e40-759">**Klíč**:`urls`</span><span class="sxs-lookup"><span data-stu-id="b2e40-759">**Key**: `urls`</span></span>  
<span data-ttu-id="b2e40-760">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-760">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-761">**Výchozí** `http://localhost:5000` : a`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="b2e40-761">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="b2e40-762">**Proměnná prostředí**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="b2e40-762">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="b2e40-763">Chcete-li nastavit tuto hodnotu, `UseUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="b2e40-763">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="b2e40-764">Kestrel má vlastní konfigurační rozhraní API koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="b2e40-764">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="b2e40-765">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b2e40-765">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="b2e40-766">Webroot</span><span class="sxs-lookup"><span data-stu-id="b2e40-766">WebRoot</span></span>

<span data-ttu-id="b2e40-767">Vlastnost [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) určuje relativní cestu ke statickým prostředkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-767">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="b2e40-768">Pokud cesta neexistuje, použije se zprostředkovatel souboru bez operace.</span><span class="sxs-lookup"><span data-stu-id="b2e40-768">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="b2e40-769">**Klíč**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="b2e40-769">**Key**: `webroot`</span></span>  
<span data-ttu-id="b2e40-770">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="b2e40-770">**Type**: `string`</span></span>  
<span data-ttu-id="b2e40-771">**Výchozí**: Výchozí `wwwroot`hodnota je .</span><span class="sxs-lookup"><span data-stu-id="b2e40-771">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="b2e40-772">Cesta k *kořenovému adresáři obsahu}/www root* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="b2e40-772">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="b2e40-773">**Proměnná prostředí**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="b2e40-773">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="b2e40-774">Chcete-li nastavit tuto hodnotu, `UseWebRoot` `IWebHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="b2e40-774">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="b2e40-775">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="b2e40-775">For more information, see:</span></span>

* [<span data-ttu-id="b2e40-776">Základy: Kořen webu</span><span class="sxs-lookup"><span data-stu-id="b2e40-776">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="b2e40-777">Kořenová složka obsahu</span><span class="sxs-lookup"><span data-stu-id="b2e40-777">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="b2e40-778">Správa doby trvání hostitele</span><span class="sxs-lookup"><span data-stu-id="b2e40-778">Manage the host lifetime</span></span>

<span data-ttu-id="b2e40-779">Volání metody na <xref:Microsoft.Extensions.Hosting.IHost> vytvořené implementace spustit a zastavit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b2e40-779">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="b2e40-780">Tyto metody <xref:Microsoft.Extensions.Hosting.IHostedService> ovlivňují všechny implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b2e40-780">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="b2e40-781">Spusťte</span><span class="sxs-lookup"><span data-stu-id="b2e40-781">Run</span></span>

<span data-ttu-id="b2e40-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne.</span><span class="sxs-lookup"><span data-stu-id="b2e40-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="b2e40-783">Synchronizace runasync</span><span class="sxs-lookup"><span data-stu-id="b2e40-783">RunAsync</span></span>

<span data-ttu-id="b2e40-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="b2e40-785">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="b2e40-785">RunConsoleAsync</span></span>

<span data-ttu-id="b2e40-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="b2e40-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="b2e40-787">Spustit</span><span class="sxs-lookup"><span data-stu-id="b2e40-787">Start</span></span>

<span data-ttu-id="b2e40-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="b2e40-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="b2e40-789">Spuštění synchronizace</span><span class="sxs-lookup"><span data-stu-id="b2e40-789">StartAsync</span></span>

<span data-ttu-id="b2e40-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="b2e40-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na `StartAsync`začátku , který čeká, až bude dokončena před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="b2e40-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="b2e40-792">To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.</span><span class="sxs-lookup"><span data-stu-id="b2e40-792">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="b2e40-793">StopAsync</span><span class="sxs-lookup"><span data-stu-id="b2e40-793">StopAsync</span></span>

<span data-ttu-id="b2e40-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.</span><span class="sxs-lookup"><span data-stu-id="b2e40-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="b2e40-795">Čekání na vypnutí</span><span class="sxs-lookup"><span data-stu-id="b2e40-795">WaitForShutdown</span></span>

<span data-ttu-id="b2e40-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud vypnutí je spuštěna IHostLifetime, například přes <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="b2e40-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="b2e40-797">Čekánínavypnutíasynchroniku</span><span class="sxs-lookup"><span data-stu-id="b2e40-797">WaitForShutdownAsync</span></span>

<span data-ttu-id="b2e40-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .</span><span class="sxs-lookup"><span data-stu-id="b2e40-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="b2e40-799">Externí ovládání</span><span class="sxs-lookup"><span data-stu-id="b2e40-799">External control</span></span>

<span data-ttu-id="b2e40-800">Příméřízení životnosti hostitele lze dosáhnout pomocí metod, které lze volat externě:</span><span class="sxs-lookup"><span data-stu-id="b2e40-800">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="b2e40-801">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b2e40-801">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
