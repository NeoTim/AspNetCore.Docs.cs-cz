---
title: Obecný hostitel rozhraní .NET
author: rick-anderson
description: Další informace o obecném hostiteli .NET Core, který je zodpovědný za spuštění aplikace a správu životnosti.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 46a56c278e889778e58a1fbb41ec217aaf023b13
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488771"
---
# <a name="net-generic-host"></a><span data-ttu-id="8ca4d-103">Obecný hostitel rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="8ca4d-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="8ca4d-104">Šablony ASP.NET Core vytvoří obecný hostitel jádra .NET . <xref:Microsoft.Extensions.Hosting.HostBuilder></span><span class="sxs-lookup"><span data-stu-id="8ca4d-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="8ca4d-105">Definice hostitele</span><span class="sxs-lookup"><span data-stu-id="8ca4d-105">Host definition</span></span>

<span data-ttu-id="8ca4d-106">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="8ca4d-107">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="8ca4d-108">Protokolování</span><span class="sxs-lookup"><span data-stu-id="8ca4d-108">Logging</span></span>
* <span data-ttu-id="8ca4d-109">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="8ca4d-109">Configuration</span></span>
* <span data-ttu-id="8ca4d-110">`IHostedService`Implementace</span><span class="sxs-lookup"><span data-stu-id="8ca4d-110">`IHostedService` implementations</span></span>

<span data-ttu-id="8ca4d-111">Při spuštění hostitele volá `IHostedService.StartAsync` na každou <xref:Microsoft.Extensions.Hosting.IHostedService> implementaci, která najde v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="8ca4d-112">Ve webové aplikaci je `IHostedService` jednou z implementací webová služba, která spouští [implementaci http serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="8ca4d-113">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je správa životnosti: kontrola nad spuštěním aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="8ca4d-114">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="8ca4d-114">Set up a host</span></span>

<span data-ttu-id="8ca4d-115">Hostitel je obvykle nakonfigurován, sestaven a spuštěn `Program` podle kódu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="8ca4d-116">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-116">The `Main` method:</span></span>

* <span data-ttu-id="8ca4d-117">Volá `CreateHostBuilder` metodu k vytvoření a konfiguraci objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="8ca4d-118">Volání `Build` `Run` a metody objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="8ca4d-119">Webové šablony ASP.NET Core generují následující kód pro vytvoření obecného hostitele:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="8ca4d-120">Následující kód vytvoří obecný hostitel pomocí úlohy bez protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="8ca4d-121">Implementace `IHostedService` je přidána do kontejneru DI:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="8ca4d-122">Pro úlohu HTTP `Main` je metoda `CreateHostBuilder` stejná, ale volá `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="8ca4d-123">Předchozí kód je generován šablonami ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="8ca4d-124">Pokud aplikace používá Core entity frameworku, neměňte `CreateHostBuilder` název nebo podpis metody.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="8ca4d-125">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že najdou metodu, `CreateHostBuilder` která konfiguruje hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="8ca4d-126">Další informace naleznete v [tématu Návrh-time DbContext Vytvoření](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="8ca4d-127">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="8ca4d-127">Default builder settings</span></span>

<span data-ttu-id="8ca4d-128">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="8ca4d-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="8ca4d-129">Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>cestu vrácenou programem .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="8ca4d-130">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="8ca4d-131">Proměnné prostředí s `DOTNET_`předponou .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="8ca4d-132">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-132">Command-line arguments.</span></span>
* <span data-ttu-id="8ca4d-133">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="8ca4d-134">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="8ca4d-135">*nastavení aplikace. {Prostředí}.json*.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="8ca4d-136">[Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="8ca4d-137">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-137">Environment variables.</span></span>
  * <span data-ttu-id="8ca4d-138">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-138">Command-line arguments.</span></span>
* <span data-ttu-id="8ca4d-139">Přidá následující zprostředkovatele [protokolování:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="8ca4d-140">Konzola</span><span class="sxs-lookup"><span data-stu-id="8ca4d-140">Console</span></span>
  * <span data-ttu-id="8ca4d-141">Ladit</span><span class="sxs-lookup"><span data-stu-id="8ca4d-141">Debug</span></span>
  * <span data-ttu-id="8ca4d-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="8ca4d-142">EventSource</span></span>
  * <span data-ttu-id="8ca4d-143">EventLog (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="8ca4d-144">Umožňuje [ověření oboru](xref:fundamentals/dependency-injection#scope-validation) a ověření [závislostí,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) když je prostředí Vývoj.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="8ca4d-145">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="8ca4d-146">Načte konfiguraci hostitele z `ASPNETCORE_`proměnných prostředí s předponou .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="8ca4d-147">Nastaví [server Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje jej pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="8ca4d-148">Výchozí možnosti serveru Kestrel naleznete <xref:fundamentals/servers/kestrel#kestrel-options>v tématu .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="8ca4d-149">Přidá [middleware filtrování hostitelů](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="8ca4d-150">Přidá [middleware s předaných záhlaví,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se rovná . `true`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="8ca4d-151">Umožňuje integraci iis.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-151">Enables IIS integration.</span></span> <span data-ttu-id="8ca4d-152">Výchozí možnosti iis <xref:host-and-deploy/iis/index#iis-options>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="8ca4d-153">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a nastavení pro webové [aplikace](#settings-for-web-apps) části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="8ca4d-154">Rámcové služby</span><span class="sxs-lookup"><span data-stu-id="8ca4d-154">Framework-provided services</span></span>

<span data-ttu-id="8ca4d-155">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="8ca4d-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="8ca4d-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="8ca4d-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="8ca4d-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="8ca4d-158">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="8ca4d-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="8ca4d-159">Další informace o službách poskytovaných <xref:fundamentals/dependency-injection#framework-provided-services>rámcem naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="8ca4d-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="8ca4d-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="8ca4d-161"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Vstříkněte `IApplicationLifetime`(dříve) službu do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="8ca4d-162">Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci metod spuštění aplikace a zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="8ca4d-163">Rozhraní také obsahuje `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="8ca4d-164">Následující příklad je `IHostedService` implementace, `IHostApplicationLifetime` která registruje události:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="8ca4d-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="8ca4d-165">IHostLifetime</span></span>

<span data-ttu-id="8ca4d-166">Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> řídí při spuštění hostitele a při jeho zastavení.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="8ca4d-167">Používá se poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-167">The last implementation registered is used.</span></span>

<span data-ttu-id="8ca4d-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="8ca4d-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="8ca4d-170">Naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="8ca4d-171">Odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="8ca4d-172">IHostŽivotní prostředí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-172">IHostEnvironment</span></span>

<span data-ttu-id="8ca4d-173">Vložte <xref:Microsoft.Extensions.Hosting.IHostEnvironment> službu do třídy, abyste získali informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="8ca4d-174">Název_aplikace</span><span class="sxs-lookup"><span data-stu-id="8ca4d-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="8ca4d-175">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="8ca4d-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="8ca4d-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="8ca4d-177">Webové aplikace `IWebHostEnvironment` implementují rozhraní, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="8ca4d-178">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="8ca4d-178">Host configuration</span></span>

<span data-ttu-id="8ca4d-179">Konfigurace hostitele se používá pro <xref:Microsoft.Extensions.Hosting.IHostEnvironment> vlastnosti implementace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="8ca4d-180">Konfigurace hostitele je k dispozici z <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="8ca4d-181">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , je nahrazen a konfigurátor aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="8ca4d-182">Chcete-li přidat <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> konfiguraci hostitele, volejte na . `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="8ca4d-183">`ConfigureHostConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="8ca4d-184">Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="8ca4d-185">Zprostředkovatel proměnných prostředí `DOTNET_` s argumenty předpony a `CreateDefaultBuilder`příkazového řádku jsou zahrnuty do .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8ca4d-186">Pro webové aplikace je přidán poskytovatel `ASPNETCORE_` proměnných prostředí s předponou.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="8ca4d-187">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="8ca4d-188">Například hodnota proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se stane `environment` hodnotou konfigurace hostitele pro klíč.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="8ca4d-189">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="8ca4d-190">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="8ca4d-190">App configuration</span></span>

<span data-ttu-id="8ca4d-191">Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří `IHostBuilder`voláním na .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="8ca4d-192">`ConfigureAppConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="8ca4d-193">Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="8ca4d-194">Konfigurace vytvořená `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako služba z DI.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="8ca4d-195">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="8ca4d-196">Další informace naleznete [v tématu Konfigurace v ASP.NET jádru](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="8ca4d-197">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="8ca4d-197">Settings for all app types</span></span>

<span data-ttu-id="8ca4d-198">V této části jsou uvedena nastavení hostitele, která platí pro úlohy protokolu HTTP i jiné než http.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="8ca4d-199">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="8ca4d-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="8ca4d-200">ApplicationName</span></span>

<span data-ttu-id="8ca4d-201">Vlastnost [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="8ca4d-202">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="8ca4d-203">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-203">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-204">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="8ca4d-205">**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="8ca4d-206">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="8ca4d-207">Kořenová složka obsahu</span><span class="sxs-lookup"><span data-stu-id="8ca4d-207">ContentRoot</span></span>

<span data-ttu-id="8ca4d-208">Vlastnost [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="8ca4d-209">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="8ca4d-210">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="8ca4d-211">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-211">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-212">**Výchozí**: Složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="8ca4d-213">**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="8ca4d-214">Chcete-li nastavit tuto hodnotu, `UseContentRoot` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="8ca4d-215">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-215">For more information, see:</span></span>

* [<span data-ttu-id="8ca4d-216">Základy: Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="8ca4d-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="8ca4d-217">Webroot</span><span class="sxs-lookup"><span data-stu-id="8ca4d-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="8ca4d-218">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-218">EnvironmentName</span></span>

<span data-ttu-id="8ca4d-219">Vlastnost [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="8ca4d-220">Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="8ca4d-221">Hodnoty nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="8ca4d-222">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-222">**Key**: `environment`</span></span>  
<span data-ttu-id="8ca4d-223">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-223">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-224">**Výchozí :**`Production`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-224">**Default**: `Production`</span></span>  
<span data-ttu-id="8ca4d-225">**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="8ca4d-226">Chcete-li nastavit tuto hodnotu, `UseEnvironment` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="8ca4d-227">Timeout vypnutí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-227">ShutdownTimeout</span></span>

<span data-ttu-id="8ca4d-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>výtok pro .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="8ca4d-229">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-229">The default value is five seconds.</span></span>  <span data-ttu-id="8ca4d-230">Během časového období hostitel:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="8ca4d-231">Aktivuje [iHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="8ca4d-232">Pokusí se zastavit hostované služby, protokolování chyb pro služby, které se nepodaří zastavit.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="8ca4d-233">Pokud vyprší časový limit před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="8ca4d-234">Služby se zastaví, i když ještě nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="8ca4d-235">Pokud služby vyžadují další čas k zastavení, zvyšte časový čas.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="8ca4d-236">**Klíč**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="8ca4d-237">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-237">**Type**: `int`</span></span>  
<span data-ttu-id="8ca4d-238">**Výchozí**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="8ca4d-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="8ca4d-239">**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="8ca4d-240">Chcete-li tuto hodnotu nastavit, `HostOptions`použijte proměnnou prostředí nebo nakonfigurujte .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="8ca4d-241">Následující příklad nastaví časový čas na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="8ca4d-242">Nastavení webových aplikací</span><span class="sxs-lookup"><span data-stu-id="8ca4d-242">Settings for web apps</span></span>

<span data-ttu-id="8ca4d-243">Některá nastavení hostitele platí pouze pro úlohy PROTOKOLU HTTP.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="8ca4d-244">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="8ca4d-245">Pro tato `IWebHostBuilder` nastavení jsou k dispozici metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="8ca4d-246">Ukázky kódu, které ukazují, `webBuilder` jak volat `IWebHostBuilder`metody rozšíření assume je instance , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="8ca4d-247">Chyby po spuštění systému Capture</span><span class="sxs-lookup"><span data-stu-id="8ca4d-247">CaptureStartupErrors</span></span>

<span data-ttu-id="8ca4d-248">Když `false`, chyby při spuštění za následek ukončení hostitele.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="8ca4d-249">Když `true`hostitel zachytí výjimky při spuštění a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="8ca4d-250">**Klíč**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="8ca4d-251">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="8ca4d-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8ca4d-252">**Výchozí**: Výchozí `false` nastavení, pokud aplikace běží s Kestrel `true`za IIS, kde je výchozí .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="8ca4d-253">**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="8ca4d-254">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="8ca4d-255">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="8ca4d-255">DetailedErrors</span></span>

<span data-ttu-id="8ca4d-256">Pokud je povoleno nebo `Development`pokud je prostředí , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="8ca4d-257">**Klíč**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="8ca4d-258">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="8ca4d-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8ca4d-259">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-259">**Default**: `false`</span></span>  
<span data-ttu-id="8ca4d-260">**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="8ca4d-261">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="8ca4d-262">Hostovánístartupových sestavení</span><span class="sxs-lookup"><span data-stu-id="8ca4d-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="8ca4d-263">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který se načte při spuštění.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="8ca4d-264">Přestože hodnota konfigurace výchozí prázdný řetězec, hostování spouštěcí sestavení vždy součástí sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="8ca4d-265">Když jsou k dispozici hostování spouštěcísestavení, jsou přidány do sestavení aplikace pro načtení při aplikaci vytváří své běžné služby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="8ca4d-266">**Klíč**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="8ca4d-267">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-267">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-268">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="8ca4d-268">**Default**: Empty string</span></span>  
<span data-ttu-id="8ca4d-269">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="8ca4d-270">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="8ca4d-271">HostováníStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="8ca4d-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="8ca4d-272">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který má být při spuštění vyloučen.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="8ca4d-273">**Klíč**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="8ca4d-274">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-274">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-275">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="8ca4d-275">**Default**: Empty string</span></span>  
<span data-ttu-id="8ca4d-276">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="8ca4d-277">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="8ca4d-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="8ca4d-278">HTTPS_Port</span></span>

<span data-ttu-id="8ca4d-279">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-279">The HTTPS redirect port.</span></span> <span data-ttu-id="8ca4d-280">Používá se při [vynucování protokolu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="8ca4d-281">**Klíč**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="8ca4d-282">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-282">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-283">**Výchozí**: Výchozí hodnota není nastavena.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="8ca4d-284">**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="8ca4d-285">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="8ca4d-286">PreferhostingUrls</span><span class="sxs-lookup"><span data-stu-id="8ca4d-286">PreferHostingUrls</span></span>

<span data-ttu-id="8ca4d-287">Označuje, zda má hostitel naslouchat adresám `IWebHostBuilder` URL nakonfigurovaným s `IServer` namísto adres URL nakonfigurovaných s implementací.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="8ca4d-288">**Klíč**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="8ca4d-289">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="8ca4d-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8ca4d-290">**Výchozí :**`true`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-290">**Default**: `true`</span></span>  
<span data-ttu-id="8ca4d-291">**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="8ca4d-292">Chcete-li nastavit tuto hodnotu, `PreferHostingUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="8ca4d-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="8ca4d-293">PreventHostingStartup</span></span>

<span data-ttu-id="8ca4d-294">Zabraňuje automatickému načítání hostitelských spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="8ca4d-295">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="8ca4d-296">**Klíč**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="8ca4d-297">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="8ca4d-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8ca4d-298">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-298">**Default**: `false`</span></span>  
<span data-ttu-id="8ca4d-299">**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="8ca4d-300">Chcete-li nastavit tuto hodnotu, `UseSetting` použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="8ca4d-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="8ca4d-301">StartupAssembly</span></span>

<span data-ttu-id="8ca4d-302">Sestavení k hledání `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="8ca4d-303">**Klíč**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="8ca4d-304">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-304">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-305">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="8ca4d-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="8ca4d-306">**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="8ca4d-307">Chcete-li nastavit tuto hodnotu, `UseStartup`použijte proměnnou prostředí nebo volání .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="8ca4d-308">`UseStartup`může mít název`string`sestavení ( )`TStartup`nebo typ ( ).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="8ca4d-309">Pokud `UseStartup` je voláno více metod, má přednost poslední metoda.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="8ca4d-310">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="8ca4d-310">URLs</span></span>

<span data-ttu-id="8ca4d-311">Seznam adres IP oddělených středníkem nebo hostitelských adres s porty a protokoly, které by měl server naslouchat požadavkům.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="8ca4d-312">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="8ca4d-313">Pomocí\*" " označuje, že server by měl naslouchat požadavkům na libovolnou adresu IP `http://*:5000`nebo název hostitele pomocí zadaného portu a protokolu (například ).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="8ca4d-314">Protokol (`http://` `https://`nebo ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="8ca4d-315">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="8ca4d-316">**Klíč**:`urls`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-316">**Key**: `urls`</span></span>  
<span data-ttu-id="8ca4d-317">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-317">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-318">**Výchozí** `http://localhost:5000` : a`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="8ca4d-319">**Proměnná prostředí**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="8ca4d-320">Chcete-li nastavit tuto hodnotu, `UseUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="8ca4d-321">Kestrel má vlastní konfigurační rozhraní API koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="8ca4d-322">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="8ca4d-323">Webroot</span><span class="sxs-lookup"><span data-stu-id="8ca4d-323">WebRoot</span></span>

<span data-ttu-id="8ca4d-324">Vlastnost [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) určuje relativní cestu ke statickým prostředkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="8ca4d-325">Pokud cesta neexistuje, použije se zprostředkovatel souboru bez operace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="8ca4d-326">**Klíč**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="8ca4d-327">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-327">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-328">**Výchozí**: Výchozí `wwwroot`hodnota je .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="8ca4d-329">Cesta k *kořenovému adresáři obsahu}/www root* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="8ca4d-330">**Proměnná prostředí**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="8ca4d-331">Chcete-li nastavit tuto hodnotu, `UseWebRoot` `IWebHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="8ca4d-332">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-332">For more information, see:</span></span>

* [<span data-ttu-id="8ca4d-333">Základy: Kořen webu</span><span class="sxs-lookup"><span data-stu-id="8ca4d-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="8ca4d-334">Kořenová složka obsahu</span><span class="sxs-lookup"><span data-stu-id="8ca4d-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="8ca4d-335">Správa doby trvání hostitele</span><span class="sxs-lookup"><span data-stu-id="8ca4d-335">Manage the host lifetime</span></span>

<span data-ttu-id="8ca4d-336">Volání metody na <xref:Microsoft.Extensions.Hosting.IHost> vytvořené implementace spustit a zastavit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="8ca4d-337">Tyto metody <xref:Microsoft.Extensions.Hosting.IHostedService> ovlivňují všechny implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="8ca4d-338">Spusťte</span><span class="sxs-lookup"><span data-stu-id="8ca4d-338">Run</span></span>

<span data-ttu-id="8ca4d-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="8ca4d-340">Synchronizace runasync</span><span class="sxs-lookup"><span data-stu-id="8ca4d-340">RunAsync</span></span>

<span data-ttu-id="8ca4d-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="8ca4d-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="8ca4d-342">RunConsoleAsync</span></span>

<span data-ttu-id="8ca4d-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="8ca4d-344">Spustit</span><span class="sxs-lookup"><span data-stu-id="8ca4d-344">Start</span></span>

<span data-ttu-id="8ca4d-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="8ca4d-346">Spuštění synchronizace</span><span class="sxs-lookup"><span data-stu-id="8ca4d-346">StartAsync</span></span>

<span data-ttu-id="8ca4d-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="8ca4d-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na `StartAsync`začátku , který čeká, až bude dokončena před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="8ca4d-349">To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="8ca4d-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="8ca4d-350">StopAsync</span></span>

<span data-ttu-id="8ca4d-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="8ca4d-352">Čekání na vypnutí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-352">WaitForShutdown</span></span>

<span data-ttu-id="8ca4d-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud vypnutí je spuštěna IHostLifetime, například přes <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="8ca4d-354">Čekánínavypnutíasynchroniku</span><span class="sxs-lookup"><span data-stu-id="8ca4d-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="8ca4d-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="8ca4d-356">Externí ovládání</span><span class="sxs-lookup"><span data-stu-id="8ca4d-356">External control</span></span>

<span data-ttu-id="8ca4d-357">Příméřízení životnosti hostitele lze dosáhnout pomocí metod, které lze volat externě:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="8ca4d-358">ASP.NET aplikace Core nakonfigurují a spouštějí hostitele.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="8ca4d-359">Hostitel je zodpovědný za spuštění aplikace a správu životnosti.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="8ca4d-360">Tento článek popisuje ASP.NET základní<xref:Microsoft.Extensions.Hosting.HostBuilder>obecný hostitel ( ), který se používá pro aplikace, které nezpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="8ca4d-361">Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele a povolit tak širší škálu hostitelských scénářů.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="8ca4d-362">Zasílání zpráv, úlohy na pozadí a další úlohy bez protokolu HTTP založené na obecném hostiteli těží z průřezových funkcí, jako je konfigurace, vkládání závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="8ca4d-363">Obecný hostitel je nový v ASP.NET Core 2.1 a není vhodný pro scénáře webhostingu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="8ca4d-364">Pro scénáře webhostingu použijte [webový hostitel](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8ca4d-365">Obecný hostitel nahradí webhostingu v budoucí verzi a bude fungovat jako primární rozhraní API hostitele ve scénářích HTTP i bez protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="8ca4d-366">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8ca4d-367">Při spuštění ukázkové aplikace v [kódu Visual Studia](https://code.visualstudio.com/)použijte externí nebo integrovaný *terminál*.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="8ca4d-368">Nespouštějte ukázku `internalConsole`v .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="8ca4d-369">Nastavení konzoly v kódu sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="8ca4d-370">Otevřete soubor *.vscode/launch.json.*</span><span class="sxs-lookup"><span data-stu-id="8ca4d-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="8ca4d-371">V konfiguraci **spuštění jádra .NET (konzola)** vyhledejte položku **konzoly.**</span><span class="sxs-lookup"><span data-stu-id="8ca4d-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="8ca4d-372">Nastavte hodnotu `externalTerminal` na `integratedTerminal`buď nebo .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="8ca4d-373">Úvod</span><span class="sxs-lookup"><span data-stu-id="8ca4d-373">Introduction</span></span>

<span data-ttu-id="8ca4d-374">Knihovna obecného hostitele <xref:Microsoft.Extensions.Hosting> je k dispozici v oboru názvů a je k dispozici v balíčku [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="8ca4d-375">Balíček `Microsoft.Extensions.Hosting` je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="8ca4d-376"><xref:Microsoft.Extensions.Hosting.IHostedService>je vstupní bod pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="8ca4d-377">Každá <xref:Microsoft.Extensions.Hosting.IHostedService> implementace je spuštěna v pořadí [registrace služby v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="8ca4d-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>je volána <xref:Microsoft.Extensions.Hosting.IHostedService> na každý při <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> spuštění hostitele a je volána v pořadí reverzní registrace při hostování řádně vypne.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="8ca4d-379">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="8ca4d-379">Set up a host</span></span>

<span data-ttu-id="8ca4d-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder>je hlavní komponenta, kterou knihovny a aplikace používají k inicializaci, sestavení a spuštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="8ca4d-381">Možnosti</span><span class="sxs-lookup"><span data-stu-id="8ca4d-381">Options</span></span>

<span data-ttu-id="8ca4d-382"><xref:Microsoft.Extensions.Hosting.HostOptions>konfigurovat možnosti <xref:Microsoft.Extensions.Hosting.IHost>pro rozhraní .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="8ca4d-383">Časový čas vypnutí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-383">Shutdown timeout</span></span>

<span data-ttu-id="8ca4d-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>čas pro .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="8ca4d-385">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-385">The default value is five seconds.</span></span>

<span data-ttu-id="8ca4d-386">Následující konfigurace možností `Program.Main` zvyšuje výchozí pětisekundový časový limit vypnutí na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="8ca4d-387">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="8ca4d-387">Default services</span></span>

<span data-ttu-id="8ca4d-388">Následující služby jsou registrovány během inicializace hostitele:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="8ca4d-389">[Životní](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>prostředí ( )</span><span class="sxs-lookup"><span data-stu-id="8ca4d-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="8ca4d-390">[Konfigurace](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )</span><span class="sxs-lookup"><span data-stu-id="8ca4d-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="8ca4d-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="8ca4d-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="8ca4d-393">[Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="8ca4d-394">[Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="8ca4d-395">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="8ca4d-395">Host configuration</span></span>

<span data-ttu-id="8ca4d-396">Konfigurace hostitele je vytvořena:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-396">Host configuration is created by:</span></span>

* <span data-ttu-id="8ca4d-397">Volání metody <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozšíření na nastavení [kořenového obsahu](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="8ca4d-398">Čtení konfigurace od <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>zprostředkovatelů konfigurace v .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="8ca4d-399">Metody rozšíření</span><span class="sxs-lookup"><span data-stu-id="8ca4d-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="8ca4d-400">Aplikační klíč (název)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-400">Application key (name)</span></span>

<span data-ttu-id="8ca4d-401">Vlastnost [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="8ca4d-402">Chcete-li explicitně nastavit hodnotu, použijte [klíč HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="8ca4d-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="8ca4d-403">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="8ca4d-404">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-404">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-405">**Výchozí**: Název sestavení obsahujícího vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="8ca4d-406">**Nastavit pomocí**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="8ca4d-407">**Proměnná** `<PREFIX_>APPLICATIONNAME` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="8ca4d-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="8ca4d-408">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="8ca4d-408">Content root</span></span>

<span data-ttu-id="8ca4d-409">Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="8ca4d-410">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="8ca4d-411">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-411">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-412">**Výchozí**: Výchozí je složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="8ca4d-413">**Nastavit pomocí**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="8ca4d-414">**Proměnná** `<PREFIX_>CONTENTROOT` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="8ca4d-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="8ca4d-415">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="8ca4d-416">Další informace naleznete [v tématu Základy: Kořen obsahu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="8ca4d-417">Prostředí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-417">Environment</span></span>

<span data-ttu-id="8ca4d-418">Nastaví [prostředí](xref:fundamentals/environments)aplikace .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="8ca4d-419">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-419">**Key**: `environment`</span></span>  
<span data-ttu-id="8ca4d-420">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-420">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-421">**Výchozí :**`Production`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-421">**Default**: `Production`</span></span>  
<span data-ttu-id="8ca4d-422">**Nastavit pomocí**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="8ca4d-423">**Proměnná** `<PREFIX_>ENVIRONMENT` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="8ca4d-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="8ca4d-424">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-424">The environment can be set to any value.</span></span> <span data-ttu-id="8ca4d-425">Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="8ca4d-426">Hodnoty nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="8ca4d-427">Konfiguracekonfigurace host</span><span class="sxs-lookup"><span data-stu-id="8ca4d-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="8ca4d-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> an k <xref:Microsoft.Extensions.Configuration.IConfiguration> vytvoření hostitele.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="8ca4d-429">Konfigurace hostitele se používá k <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> inicializaci pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="8ca4d-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="8ca4d-431">Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="8ca4d-432">Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-432">No providers are included by default.</span></span> <span data-ttu-id="8ca4d-433">Je nutné explicitně zadat, bez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>ohledu na to, jaké zprostředkovatele konfigurace aplikace vyžaduje v aplikaci , včetně:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="8ca4d-434">Konfigurace souboru (například ze souboru *hostsettings.json).*</span><span class="sxs-lookup"><span data-stu-id="8ca4d-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="8ca4d-435">Konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-435">Environment variable configuration.</span></span>
* <span data-ttu-id="8ca4d-436">Konfigurace argumentu příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="8ca4d-437">Jakékoli další požadované zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-437">Any other required configuration providers.</span></span>

<span data-ttu-id="8ca4d-438">Konfigurace souboru hostitele je povolena zadáním základní `SetBasePath` cesty aplikace s následovaným voláním jednoho z [poskytovatelů konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="8ca4d-439">Ukázková aplikace používá soubor JSON, *hostsettings.json*a volání <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> využívat nastavení konfigurace hostitele souboru.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="8ca4d-440">Chcete-li přidat [konfiguraci proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="8ca4d-441">`AddEnvironmentVariables`přijme volitelnou uživatelsky definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="8ca4d-442">Ukázková aplikace používá předponu `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="8ca4d-443">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="8ca4d-444">Když je nakonfigurován hostitel ukázkové aplikace, `PREFIX_ENVIRONMENT` hodnota proměnné prostředí pro `environment` se stane hodnotou konfigurace hostitele pro klíč.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="8ca4d-445">Během vývoje při použití sady Visual `dotnet run` [Studio](https://visualstudio.microsoft.com) nebo spuštění aplikace s aplikací mohou být proměnné prostředí nastaveny v souboru *Vlastnosti/spuštěníNastavení.json.*</span><span class="sxs-lookup"><span data-stu-id="8ca4d-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="8ca4d-446">V [kódu sady Visual Studio](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *.vscode/launch.json* během vývoje.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="8ca4d-447">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8ca4d-448">[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>přidána voláním .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="8ca4d-449">Konfigurace příkazového řádku je přidána jako poslední, aby bylo možné přepsat konfiguraci poskytovanou staršími poskytovateli konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="8ca4d-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="8ca4d-451">Další konfiguraci lze poskytnout s [applicationName](#application-key-name) a [contentRoot](#content-root) klíče.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="8ca4d-452">Příklad `HostBuilder` konfigurace <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>pomocí :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="8ca4d-453">Konfigurace konfigurace</span><span class="sxs-lookup"><span data-stu-id="8ca4d-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8ca4d-454">Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří <xref:Microsoft.Extensions.Hosting.IHostBuilder> voláním na implementaci.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="8ca4d-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> an k <xref:Microsoft.Extensions.Configuration.IConfiguration> vytvoření aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="8ca4d-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="8ca4d-457">Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="8ca4d-458">Konfigurace vytvořená <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uživatelem je k dispozici na adrese <xref:Microsoft.Extensions.Hosting.IHost.Services*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="8ca4d-459">Konfigurace aplikace automaticky přijímá konfiguraci hostitele poskytovanou [společností ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="8ca4d-460">Příklad konfigurace <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>aplikace pomocí :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="8ca4d-461">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="8ca4d-462">*nastavení aplikace. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="8ca4d-463">*nastavení aplikace. Production.json*:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="8ca4d-464">Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="8ca4d-465">Ukázková aplikace přesune soubory nastavení aplikace JSON a *hostsettings.json* s následující `<Content>` položkou:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="8ca4d-466">Metody rozšíření konfigurace, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> jako jsou například a vyžadují další balíčky NuGet, například [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="8ca4d-467">Pokud aplikace nepoužívá [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), musí být tyto balíčky přidány do projektu kromě základního balíčku [Microsoft.Extensions.Configuration.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="8ca4d-468">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="8ca4d-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="8ca4d-469">ConfigureServices</span></span>

<span data-ttu-id="8ca4d-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8ca4d-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="8ca4d-472">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="8ca4d-473">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="8ca4d-474">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) `AddHostedService` používá metodu rozšíření k přidání `LifetimeEventsHostedService`služby pro události `TimedHostedService`životnosti a časované úlohy na pozadí , do aplikace:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="8ca4d-475">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="8ca4d-475">ConfigureLogging</span></span>

<span data-ttu-id="8ca4d-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>přidá delegáta pro konfiguraci <xref:Microsoft.Extensions.Logging.ILoggingBuilder>poskytovaného .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="8ca4d-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>mohou být volány vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="8ca4d-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="8ca4d-478">UseConsoleLifetime</span></span>

<span data-ttu-id="8ca4d-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="8ca4d-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="8ca4d-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je předběžně zaregistrována jako výchozí implementace životnosti.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="8ca4d-482">Používá se poslední registrovaná životnost.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="8ca4d-483">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="8ca4d-483">Container configuration</span></span>

<span data-ttu-id="8ca4d-484">Pro podporu připojení jiných kontejnerů může <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>hostitel přijmout soubor .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="8ca4d-485">Poskytování factory není součástí registrace kontejneru DI, ale je hostitele vnitřní slouží k vytvoření kontejneru betonové DI.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="8ca4d-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder)&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepíše výchozí továrnu použitou k vytvoření poskytovatele služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="8ca4d-487">Vlastní konfigurace kontejneru <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> je spravována metodou.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="8ca4d-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>poskytuje prostředí silného typu pro konfiguraci kontejneru nad základní rozhraní API hostitele.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="8ca4d-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="8ca4d-490">Vytvoření kontejneru služeb pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="8ca4d-491">Poskytněte továrnu na servisní kontejnery:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="8ca4d-492">Použijte továrnu a nakonfigurujte vlastní kontejner služeb pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="8ca4d-493">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="8ca4d-493">Extensibility</span></span>

<span data-ttu-id="8ca4d-494">Rozšiřitelnost hostitele se provádí pomocí <xref:Microsoft.Extensions.Hosting.IHostBuilder>rozšiřujících metod na .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="8ca4d-495">Následující příklad ukazuje, jak metoda <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozšíření rozšiřuje implementaci s [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) příklad demonstroval v <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="8ca4d-496">Aplikace vytvoří metodu `UseHostedService` rozšíření pro registraci hostované služby předané v `T`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="8ca4d-497">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="8ca4d-497">Manage the host</span></span>

<span data-ttu-id="8ca4d-498">Implementace <xref:Microsoft.Extensions.Hosting.IHost> je zodpovědná za spuštění <xref:Microsoft.Extensions.Hosting.IHostedService> a zastavení implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="8ca4d-499">Spusťte</span><span class="sxs-lookup"><span data-stu-id="8ca4d-499">Run</span></span>

<span data-ttu-id="8ca4d-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="8ca4d-501">Synchronizace runasync</span><span class="sxs-lookup"><span data-stu-id="8ca4d-501">RunAsync</span></span>

<span data-ttu-id="8ca4d-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, která se dokončí, když se aktivuje token zrušení nebo vypnutí:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="8ca4d-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="8ca4d-503">RunConsoleAsync</span></span>

<span data-ttu-id="8ca4d-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="8ca4d-505">Spustit a zastavitsynchronizacu</span><span class="sxs-lookup"><span data-stu-id="8ca4d-505">Start and StopAsync</span></span>

<span data-ttu-id="8ca4d-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="8ca4d-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="8ca4d-508">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="8ca4d-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="8ca4d-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="8ca4d-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zastaví aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="8ca4d-511">Čekání na vypnutí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-511">WaitForShutdown</span></span>

<span data-ttu-id="8ca4d-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>se spouští <xref:Microsoft.Extensions.Hosting.IHostLifetime>přes `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` , například (naslouchá <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="8ca4d-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="8ca4d-514">Čekánínavypnutíasynchroniku</span><span class="sxs-lookup"><span data-stu-id="8ca4d-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="8ca4d-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="8ca4d-516">Externí ovládání</span><span class="sxs-lookup"><span data-stu-id="8ca4d-516">External control</span></span>

<span data-ttu-id="8ca4d-517">Externího řízení hostitele lze dosáhnout pomocí metod, které lze volat externě:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="8ca4d-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>začátku , který čeká, až bude dokončena před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="8ca4d-519">To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="8ca4d-520">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="8ca4d-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="8ca4d-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>poskytuje informace o hostitelském prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="8ca4d-522">Použijte [vstřikování konstruktoru](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> k získání, abyste mohli používat jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="8ca4d-523">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="8ca4d-524">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="8ca4d-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="8ca4d-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>umožňuje aktivity po spuštění a vypnutí, včetně řádného požadavku na vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="8ca4d-526">Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="8ca4d-527">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="8ca4d-527">Cancellation Token</span></span> | <span data-ttu-id="8ca4d-528">Aktivuje se při&#8230;</span><span class="sxs-lookup"><span data-stu-id="8ca4d-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="8ca4d-529">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="8ca4d-530">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="8ca4d-531">Všechny požadavky by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-531">All requests should be processed.</span></span> <span data-ttu-id="8ca4d-532">Vypnutí blokuje, dokud tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="8ca4d-533">Hostitel provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="8ca4d-534">Požadavky mohou být stále zpracování.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-534">Requests may still be processing.</span></span> <span data-ttu-id="8ca4d-535">Vypnutí blokuje, dokud tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="8ca4d-536">Konstruktor vstříkne službu <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="8ca4d-537">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání `LifetimeEventsHostedService` konstruktoru <xref:Microsoft.Extensions.Hosting.IHostedService> do třídy (implementace) k registraci událostí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="8ca4d-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="8ca4d-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>požaduje ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="8ca4d-540">Následující třída <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> používá k řádnému vypnutí aplikace při `Shutdown` volání metody třídy:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="8ca4d-541">Šablony ASP.NET Core vytvoří obecný hostitel jádra .NET (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="8ca4d-542">Definice hostitele</span><span class="sxs-lookup"><span data-stu-id="8ca4d-542">Host definition</span></span>

<span data-ttu-id="8ca4d-543">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="8ca4d-544">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="8ca4d-545">Protokolování</span><span class="sxs-lookup"><span data-stu-id="8ca4d-545">Logging</span></span>
* <span data-ttu-id="8ca4d-546">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="8ca4d-546">Configuration</span></span>
* <span data-ttu-id="8ca4d-547">`IHostedService`Implementace</span><span class="sxs-lookup"><span data-stu-id="8ca4d-547">`IHostedService` implementations</span></span>

<span data-ttu-id="8ca4d-548">Při spuštění hostitele volá `IHostedService.StartAsync` na každou <xref:Microsoft.Extensions.Hosting.IHostedService> implementaci, která najde v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-548">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="8ca4d-549">Ve webové aplikaci je `IHostedService` jednou z implementací webová služba, která spouští [implementaci http serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="8ca4d-550">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je správa životnosti: kontrola nad spuštěním aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="8ca4d-551">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="8ca4d-551">Set up a host</span></span>

<span data-ttu-id="8ca4d-552">Hostitel je obvykle nakonfigurován, sestaven a spuštěn `Program` podle kódu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="8ca4d-553">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-553">The `Main` method:</span></span>

* <span data-ttu-id="8ca4d-554">Volá `CreateHostBuilder` metodu k vytvoření a konfiguraci objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="8ca4d-555">Volání `Build` `Run` a metody objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="8ca4d-556">Webové šablony ASP.NET Core generují následující kód pro vytvoření hostitele:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="8ca4d-557">Následující kód vytvoří úlohu bez `IHostedService` protokolu HTTP s implementací přidanou do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="8ca4d-558">Pro úlohu HTTP `Main` je metoda `CreateHostBuilder` stejná, ale volá `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="8ca4d-559">Pokud aplikace používá Core entity frameworku, neměňte `CreateHostBuilder` název nebo podpis metody.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="8ca4d-560">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že najdou metodu, `CreateHostBuilder` která konfiguruje hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="8ca4d-561">Další informace naleznete v [tématu Návrh-time DbContext Vytvoření](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="8ca4d-562">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="8ca4d-562">Default builder settings</span></span>

<span data-ttu-id="8ca4d-563">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="8ca4d-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="8ca4d-564">Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>cestu vrácenou programem .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="8ca4d-565">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="8ca4d-566">Proměnné prostředí s `DOTNET_`předponou .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="8ca4d-567">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-567">Command-line arguments.</span></span>
* <span data-ttu-id="8ca4d-568">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="8ca4d-569">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="8ca4d-570">*nastavení aplikace. {Prostředí}.json*.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="8ca4d-571">[Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="8ca4d-572">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-572">Environment variables.</span></span>
  * <span data-ttu-id="8ca4d-573">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-573">Command-line arguments.</span></span>
* <span data-ttu-id="8ca4d-574">Přidá následující zprostředkovatele [protokolování:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="8ca4d-575">Konzola</span><span class="sxs-lookup"><span data-stu-id="8ca4d-575">Console</span></span>
  * <span data-ttu-id="8ca4d-576">Ladit</span><span class="sxs-lookup"><span data-stu-id="8ca4d-576">Debug</span></span>
  * <span data-ttu-id="8ca4d-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="8ca4d-577">EventSource</span></span>
  * <span data-ttu-id="8ca4d-578">EventLog (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="8ca4d-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="8ca4d-579">Umožňuje [ověření oboru](xref:fundamentals/dependency-injection#scope-validation) a ověření [závislostí,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) když je prostředí Vývoj.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="8ca4d-580">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="8ca4d-581">Načte konfiguraci hostitele z `ASPNETCORE_`proměnných prostředí s předponou .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="8ca4d-582">Nastaví [server Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje jej pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="8ca4d-583">Výchozí možnosti serveru Kestrel naleznete <xref:fundamentals/servers/kestrel#kestrel-options>v tématu .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="8ca4d-584">Přidá [middleware filtrování hostitelů](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="8ca4d-585">Přidá [middleware s předaných záhlaví,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se rovná . `true`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="8ca4d-586">Umožňuje integraci iis.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-586">Enables IIS integration.</span></span> <span data-ttu-id="8ca4d-587">Výchozí možnosti iis <xref:host-and-deploy/iis/index#iis-options>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="8ca4d-588">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a nastavení pro webové [aplikace](#settings-for-web-apps) části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="8ca4d-589">Rámcové služby</span><span class="sxs-lookup"><span data-stu-id="8ca4d-589">Framework-provided services</span></span>

<span data-ttu-id="8ca4d-590">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="8ca4d-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="8ca4d-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="8ca4d-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="8ca4d-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="8ca4d-593">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="8ca4d-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="8ca4d-594">Další informace o službách poskytovaných <xref:fundamentals/dependency-injection#framework-provided-services>rámcem naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="8ca4d-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="8ca4d-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="8ca4d-596"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Vstříkněte `IApplicationLifetime`(dříve) službu do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="8ca4d-597">Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci metod spuštění aplikace a zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="8ca4d-598">Rozhraní také obsahuje `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="8ca4d-599">Následující příklad je `IHostedService` implementace, `IHostApplicationLifetime` která registruje události:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="8ca4d-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="8ca4d-600">IHostLifetime</span></span>

<span data-ttu-id="8ca4d-601">Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> řídí při spuštění hostitele a při jeho zastavení.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="8ca4d-602">Používá se poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-602">The last implementation registered is used.</span></span>

<span data-ttu-id="8ca4d-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="8ca4d-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="8ca4d-605">Naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="8ca4d-606">Odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="8ca4d-607">IHostŽivotní prostředí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-607">IHostEnvironment</span></span>

<span data-ttu-id="8ca4d-608">Vložte <xref:Microsoft.Extensions.Hosting.IHostEnvironment> službu do třídy, abyste získali informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="8ca4d-609">Název_aplikace</span><span class="sxs-lookup"><span data-stu-id="8ca4d-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="8ca4d-610">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="8ca4d-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="8ca4d-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="8ca4d-612">Webové aplikace `IWebHostEnvironment` implementují rozhraní, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="8ca4d-613">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="8ca4d-613">Host configuration</span></span>

<span data-ttu-id="8ca4d-614">Konfigurace hostitele se používá pro <xref:Microsoft.Extensions.Hosting.IHostEnvironment> vlastnosti implementace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="8ca4d-615">Konfigurace hostitele je k dispozici z <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="8ca4d-616">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , je nahrazen a konfigurátor aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="8ca4d-617">Chcete-li přidat <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> konfiguraci hostitele, volejte na . `IHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="8ca4d-618">`ConfigureHostConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="8ca4d-619">Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="8ca4d-620">Zprostředkovatel proměnných prostředí `DOTNET_` s argumenty předpony a `CreateDefaultBuilder`příkazového řádku jsou zahrnuty do .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8ca4d-621">Pro webové aplikace je přidán poskytovatel `ASPNETCORE_` proměnných prostředí s předponou.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="8ca4d-622">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="8ca4d-623">Například hodnota proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se stane `environment` hodnotou konfigurace hostitele pro klíč.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="8ca4d-624">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="8ca4d-625">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="8ca4d-625">App configuration</span></span>

<span data-ttu-id="8ca4d-626">Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří `IHostBuilder`voláním na .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="8ca4d-627">`ConfigureAppConfiguration`lze volat vícekrát s aditivními výsledky.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="8ca4d-628">Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="8ca4d-629">Konfigurace vytvořená `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako služba z DI.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="8ca4d-630">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="8ca4d-631">Další informace naleznete [v tématu Konfigurace v ASP.NET jádru](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="8ca4d-632">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="8ca4d-632">Settings for all app types</span></span>

<span data-ttu-id="8ca4d-633">V této části jsou uvedena nastavení hostitele, která platí pro úlohy protokolu HTTP i jiné než http.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="8ca4d-634">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="8ca4d-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="8ca4d-635">ApplicationName</span></span>

<span data-ttu-id="8ca4d-636">Vlastnost [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="8ca4d-637">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="8ca4d-638">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-638">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-639">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="8ca4d-640">**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="8ca4d-641">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="8ca4d-642">Kořenová složka obsahu</span><span class="sxs-lookup"><span data-stu-id="8ca4d-642">ContentRoot</span></span>

<span data-ttu-id="8ca4d-643">Vlastnost [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="8ca4d-644">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="8ca4d-645">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="8ca4d-646">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-646">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-647">**Výchozí**: Složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="8ca4d-648">**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="8ca4d-649">Chcete-li nastavit tuto hodnotu, `UseContentRoot` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="8ca4d-650">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-650">For more information, see:</span></span>

* [<span data-ttu-id="8ca4d-651">Základy: Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="8ca4d-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="8ca4d-652">Webroot</span><span class="sxs-lookup"><span data-stu-id="8ca4d-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="8ca4d-653">Název prostředí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-653">EnvironmentName</span></span>

<span data-ttu-id="8ca4d-654">Vlastnost [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="8ca4d-655">Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="8ca4d-656">Hodnoty nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="8ca4d-657">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-657">**Key**: `environment`</span></span>  
<span data-ttu-id="8ca4d-658">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-658">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-659">**Výchozí :**`Production`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-659">**Default**: `Production`</span></span>  
<span data-ttu-id="8ca4d-660">**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="8ca4d-661">Chcete-li nastavit tuto hodnotu, `UseEnvironment` `IHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="8ca4d-662">Timeout vypnutí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-662">ShutdownTimeout</span></span>

<span data-ttu-id="8ca4d-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>výtok pro .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="8ca4d-664">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-664">The default value is five seconds.</span></span>  <span data-ttu-id="8ca4d-665">Během časového období hostitel:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="8ca4d-666">Aktivuje [iHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="8ca4d-667">Pokusí se zastavit hostované služby, protokolování chyb pro služby, které se nepodaří zastavit.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="8ca4d-668">Pokud vyprší časový limit před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="8ca4d-669">Služby se zastaví, i když ještě nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="8ca4d-670">Pokud služby vyžadují další čas k zastavení, zvyšte časový čas.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="8ca4d-671">**Klíč**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="8ca4d-672">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-672">**Type**: `int`</span></span>  
<span data-ttu-id="8ca4d-673">**Výchozí**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="8ca4d-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="8ca4d-674">**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="8ca4d-675">Chcete-li tuto hodnotu nastavit, `HostOptions`použijte proměnnou prostředí nebo nakonfigurujte .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="8ca4d-676">Následující příklad nastaví časový čas na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="8ca4d-677">Zakázat opětovné načtení konfigurace aplikace při změně</span><span class="sxs-lookup"><span data-stu-id="8ca4d-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="8ca4d-678">[Ve výchozím nastavení](xref:fundamentals/configuration/index#default), *appsettings.json* a *appsettings.{ Prostředí}.json* jsou znovu načteny při změně souboru.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="8ca4d-679">Chcete-li zakázat toto chování opětovného načtení v `hostBuilder:reloadConfigOnChange` ASP.NET `false`Core 5.0 Preview 3 nebo novější, nastavte klíč na .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="8ca4d-680">**Klíč**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="8ca4d-681">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="8ca4d-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8ca4d-682">**Výchozí :**`true`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-682">**Default**: `true`</span></span>  
<span data-ttu-id="8ca4d-683">**Argument příkazového řádku**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="8ca4d-684">**Proměnná prostředí**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="8ca4d-685">Oddělovač`:`dvojtečky ( ) nefunguje s hierarchickými klíči proměnné prostředí na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="8ca4d-686">Další informace naleznete v tématu [Proměnné prostředí](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="8ca4d-687">Nastavení webových aplikací</span><span class="sxs-lookup"><span data-stu-id="8ca4d-687">Settings for web apps</span></span>

<span data-ttu-id="8ca4d-688">Některá nastavení hostitele platí pouze pro úlohy PROTOKOLU HTTP.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="8ca4d-689">Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="8ca4d-690">Pro tato `IWebHostBuilder` nastavení jsou k dispozici metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="8ca4d-691">Ukázky kódu, které ukazují, `webBuilder` jak volat `IWebHostBuilder`metody rozšíření assume je instance , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="8ca4d-692">Chyby po spuštění systému Capture</span><span class="sxs-lookup"><span data-stu-id="8ca4d-692">CaptureStartupErrors</span></span>

<span data-ttu-id="8ca4d-693">Když `false`, chyby při spuštění za následek ukončení hostitele.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="8ca4d-694">Když `true`hostitel zachytí výjimky při spuštění a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="8ca4d-695">**Klíč**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="8ca4d-696">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="8ca4d-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8ca4d-697">**Výchozí**: Výchozí `false` nastavení, pokud aplikace běží s Kestrel `true`za IIS, kde je výchozí .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="8ca4d-698">**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="8ca4d-699">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="8ca4d-700">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="8ca4d-700">DetailedErrors</span></span>

<span data-ttu-id="8ca4d-701">Pokud je povoleno nebo `Development`pokud je prostředí , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="8ca4d-702">**Klíč**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="8ca4d-703">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="8ca4d-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8ca4d-704">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-704">**Default**: `false`</span></span>  
<span data-ttu-id="8ca4d-705">**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="8ca4d-706">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="8ca4d-707">Hostovánístartupových sestavení</span><span class="sxs-lookup"><span data-stu-id="8ca4d-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="8ca4d-708">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který se načte při spuštění.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="8ca4d-709">Přestože hodnota konfigurace výchozí prázdný řetězec, hostování spouštěcí sestavení vždy součástí sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="8ca4d-710">Když jsou k dispozici hostování spouštěcísestavení, jsou přidány do sestavení aplikace pro načtení při aplikaci vytváří své běžné služby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="8ca4d-711">**Klíč**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="8ca4d-712">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-712">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-713">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="8ca4d-713">**Default**: Empty string</span></span>  
<span data-ttu-id="8ca4d-714">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="8ca4d-715">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="8ca4d-716">HostováníStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="8ca4d-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="8ca4d-717">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který má být při spuštění vyloučen.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="8ca4d-718">**Klíč**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="8ca4d-719">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-719">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-720">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="8ca4d-720">**Default**: Empty string</span></span>  
<span data-ttu-id="8ca4d-721">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="8ca4d-722">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="8ca4d-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="8ca4d-723">HTTPS_Port</span></span>

<span data-ttu-id="8ca4d-724">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-724">The HTTPS redirect port.</span></span> <span data-ttu-id="8ca4d-725">Používá se při [vynucování protokolu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="8ca4d-726">**Klíč**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="8ca4d-727">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-727">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-728">**Výchozí**: Výchozí hodnota není nastavena.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="8ca4d-729">**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="8ca4d-730">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="8ca4d-731">PreferhostingUrls</span><span class="sxs-lookup"><span data-stu-id="8ca4d-731">PreferHostingUrls</span></span>

<span data-ttu-id="8ca4d-732">Označuje, zda má hostitel naslouchat adresám `IWebHostBuilder` URL nakonfigurovaným s `IServer` namísto adres URL nakonfigurovaných s implementací.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="8ca4d-733">**Klíč**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="8ca4d-734">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="8ca4d-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8ca4d-735">**Výchozí :**`true`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-735">**Default**: `true`</span></span>  
<span data-ttu-id="8ca4d-736">**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="8ca4d-737">Chcete-li nastavit tuto hodnotu, `PreferHostingUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="8ca4d-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="8ca4d-738">PreventHostingStartup</span></span>

<span data-ttu-id="8ca4d-739">Zabraňuje automatickému načítání hostitelských spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="8ca4d-740">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="8ca4d-741">**Klíč**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="8ca4d-742">**Typ** `bool` :`true` `1`( ( nebo )</span><span class="sxs-lookup"><span data-stu-id="8ca4d-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8ca4d-743">**Výchozí :**`false`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-743">**Default**: `false`</span></span>  
<span data-ttu-id="8ca4d-744">**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="8ca4d-745">Chcete-li nastavit tuto hodnotu, `UseSetting` použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="8ca4d-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="8ca4d-746">StartupAssembly</span></span>

<span data-ttu-id="8ca4d-747">Sestavení k hledání `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="8ca4d-748">**Klíč**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="8ca4d-749">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-749">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-750">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="8ca4d-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="8ca4d-751">**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="8ca4d-752">Chcete-li nastavit tuto hodnotu, `UseStartup`použijte proměnnou prostředí nebo volání .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="8ca4d-753">`UseStartup`může mít název`string`sestavení ( )`TStartup`nebo typ ( ).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="8ca4d-754">Pokud `UseStartup` je voláno více metod, má přednost poslední metoda.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="8ca4d-755">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="8ca4d-755">URLs</span></span>

<span data-ttu-id="8ca4d-756">Seznam adres IP oddělených středníkem nebo hostitelských adres s porty a protokoly, které by měl server naslouchat požadavkům.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="8ca4d-757">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="8ca4d-758">Pomocí\*" " označuje, že server by měl naslouchat požadavkům na libovolnou adresu IP `http://*:5000`nebo název hostitele pomocí zadaného portu a protokolu (například ).</span><span class="sxs-lookup"><span data-stu-id="8ca4d-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="8ca4d-759">Protokol (`http://` `https://`nebo ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="8ca4d-760">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="8ca4d-761">**Klíč**:`urls`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-761">**Key**: `urls`</span></span>  
<span data-ttu-id="8ca4d-762">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-762">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-763">**Výchozí** `http://localhost:5000` : a`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="8ca4d-764">**Proměnná prostředí**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="8ca4d-765">Chcete-li nastavit tuto hodnotu, `UseUrls`použijte proměnnou prostředí nebo volání :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="8ca4d-766">Kestrel má vlastní konfigurační rozhraní API koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="8ca4d-767">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="8ca4d-768">Webroot</span><span class="sxs-lookup"><span data-stu-id="8ca4d-768">WebRoot</span></span>

<span data-ttu-id="8ca4d-769">Vlastnost [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) určuje relativní cestu ke statickým prostředkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="8ca4d-770">Pokud cesta neexistuje, použije se zprostředkovatel souboru bez operace.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="8ca4d-771">**Klíč**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="8ca4d-772">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-772">**Type**: `string`</span></span>  
<span data-ttu-id="8ca4d-773">**Výchozí**: Výchozí `wwwroot`hodnota je .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="8ca4d-774">Cesta k *kořenovému adresáři obsahu}/www root* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="8ca4d-775">**Proměnná prostředí**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="8ca4d-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="8ca4d-776">Chcete-li nastavit tuto hodnotu, `UseWebRoot` `IWebHostBuilder`použijte proměnnou prostředí nebo volejte :</span><span class="sxs-lookup"><span data-stu-id="8ca4d-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="8ca4d-777">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-777">For more information, see:</span></span>

* [<span data-ttu-id="8ca4d-778">Základy: Kořen webu</span><span class="sxs-lookup"><span data-stu-id="8ca4d-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="8ca4d-779">Kořenová složka obsahu</span><span class="sxs-lookup"><span data-stu-id="8ca4d-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="8ca4d-780">Správa doby trvání hostitele</span><span class="sxs-lookup"><span data-stu-id="8ca4d-780">Manage the host lifetime</span></span>

<span data-ttu-id="8ca4d-781">Volání metody na <xref:Microsoft.Extensions.Hosting.IHost> vytvořené implementace spustit a zastavit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="8ca4d-782">Tyto metody <xref:Microsoft.Extensions.Hosting.IHostedService> ovlivňují všechny implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="8ca4d-783">Spusťte</span><span class="sxs-lookup"><span data-stu-id="8ca4d-783">Run</span></span>

<span data-ttu-id="8ca4d-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="8ca4d-785">Synchronizace runasync</span><span class="sxs-lookup"><span data-stu-id="8ca4d-785">RunAsync</span></span>

<span data-ttu-id="8ca4d-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="8ca4d-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="8ca4d-787">RunConsoleAsync</span></span>

<span data-ttu-id="8ca4d-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="8ca4d-789">Spustit</span><span class="sxs-lookup"><span data-stu-id="8ca4d-789">Start</span></span>

<span data-ttu-id="8ca4d-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="8ca4d-791">Spuštění synchronizace</span><span class="sxs-lookup"><span data-stu-id="8ca4d-791">StartAsync</span></span>

<span data-ttu-id="8ca4d-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="8ca4d-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na `StartAsync`začátku , který čeká, až bude dokončena před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="8ca4d-794">To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="8ca4d-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="8ca4d-795">StopAsync</span></span>

<span data-ttu-id="8ca4d-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="8ca4d-797">Čekání na vypnutí</span><span class="sxs-lookup"><span data-stu-id="8ca4d-797">WaitForShutdown</span></span>

<span data-ttu-id="8ca4d-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud vypnutí je spuštěna IHostLifetime, například přes <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="8ca4d-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="8ca4d-799">Čekánínavypnutíasynchroniku</span><span class="sxs-lookup"><span data-stu-id="8ca4d-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="8ca4d-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .</span><span class="sxs-lookup"><span data-stu-id="8ca4d-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="8ca4d-801">Externí ovládání</span><span class="sxs-lookup"><span data-stu-id="8ca4d-801">External control</span></span>

<span data-ttu-id="8ca4d-802">Příméřízení životnosti hostitele lze dosáhnout pomocí metod, které lze volat externě:</span><span class="sxs-lookup"><span data-stu-id="8ca4d-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="8ca4d-803">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8ca4d-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
