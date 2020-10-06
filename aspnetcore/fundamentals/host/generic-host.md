---
title: Obecný hostitel .NET v ASP.NET Core
author: rick-anderson
description: Použijte obecného hostitele .NET Core v aplikacích ASP.NET Core.  Obecný hostitel zodpovídá za spuštění aplikace a správu životního cyklu.
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
ms.openlocfilehash: d3de81ce7248372279b423da865513ee5db73c79
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762318"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="0897c-104">Obecný hostitel .NET v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0897c-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0897c-105">Šablony ASP.NET Core vytvoří obecného hostitele .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="0897c-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="0897c-106">Toto téma poskytuje informace o použití obecného hostitele .NET v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0897c-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="0897c-107">Informace o použití obecného hostitele .NET v konzolových aplikacích najdete v tématu [obecný hostitel .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="0897c-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="0897c-108">Definice hostitele</span><span class="sxs-lookup"><span data-stu-id="0897c-108">Host definition</span></span>

<span data-ttu-id="0897c-109">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="0897c-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="0897c-110">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="0897c-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="0897c-111">Protokolování</span><span class="sxs-lookup"><span data-stu-id="0897c-111">Logging</span></span>
* <span data-ttu-id="0897c-112">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="0897c-112">Configuration</span></span>
* <span data-ttu-id="0897c-113">`IHostedService` implementaci</span><span class="sxs-lookup"><span data-stu-id="0897c-113">`IHostedService` implementations</span></span>

<span data-ttu-id="0897c-114">Při spuštění hostitele zavolá <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> každou implementaci <xref:Microsoft.Extensions.Hosting.IHostedService> zaregistrovanou v kolekci hostovaných služeb kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0897c-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="0897c-115">V rámci webové aplikace je jednou z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="0897c-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="0897c-116">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0897c-117">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="0897c-117">Set up a host</span></span>

<span data-ttu-id="0897c-118">Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve `Program` třídě.</span><span class="sxs-lookup"><span data-stu-id="0897c-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="0897c-119">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="0897c-119">The `Main` method:</span></span>

* <span data-ttu-id="0897c-120">Volá `CreateHostBuilder` metodu pro vytvoření a konfiguraci objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="0897c-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="0897c-121">Volání `Build` a `Run` metody objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="0897c-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="0897c-122">Webové šablony ASP.NET Core generují následující kód pro vytvoření hostitele:</span><span class="sxs-lookup"><span data-stu-id="0897c-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="0897c-123">Následující kód vytvoří úlohu jiného typu než HTTP s `IHostedService` implementací přidanou do kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="0897c-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="0897c-124">Pro úlohy HTTP `Main` je metoda stejná, ale `CreateHostBuilder` volání `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="0897c-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="0897c-125">Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="0897c-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="0897c-126">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že budou nalezeny `CreateHostBuilder` metody, které nakonfigurují hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="0897c-127">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="0897c-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="0897c-128">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="0897c-128">Default builder settings</span></span>

<span data-ttu-id="0897c-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="0897c-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="0897c-130">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="0897c-131">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="0897c-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="0897c-132">Proměnné prostředí s předponou `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="0897c-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="0897c-133">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="0897c-133">Command-line arguments.</span></span>
* <span data-ttu-id="0897c-134">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="0897c-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="0897c-135">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="0897c-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="0897c-136">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0897c-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="0897c-137">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="0897c-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="0897c-138">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0897c-138">Environment variables.</span></span>
  * <span data-ttu-id="0897c-139">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="0897c-139">Command-line arguments.</span></span>
* <span data-ttu-id="0897c-140">Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="0897c-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="0897c-141">Konzola</span><span class="sxs-lookup"><span data-stu-id="0897c-141">Console</span></span>
  * <span data-ttu-id="0897c-142">Ladit</span><span class="sxs-lookup"><span data-stu-id="0897c-142">Debug</span></span>
  * <span data-ttu-id="0897c-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="0897c-143">EventSource</span></span>
  * <span data-ttu-id="0897c-144">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="0897c-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="0897c-145">Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.</span><span class="sxs-lookup"><span data-stu-id="0897c-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="0897c-146">`ConfigureWebHostDefaults`Metoda:</span><span class="sxs-lookup"><span data-stu-id="0897c-146">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="0897c-147">Načte konfiguraci hostitele z proměnných prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0897c-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="0897c-148">Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="0897c-149">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="0897c-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="0897c-150">Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="0897c-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="0897c-151">Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , pokud je rovno `true` .</span><span class="sxs-lookup"><span data-stu-id="0897c-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="0897c-152">Povolí integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0897c-152">Enables IIS integration.</span></span> <span data-ttu-id="0897c-153">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="0897c-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="0897c-154">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="0897c-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0897c-155">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="0897c-155">Framework-provided services</span></span>

<span data-ttu-id="0897c-156">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="0897c-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="0897c-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="0897c-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="0897c-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="0897c-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="0897c-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="0897c-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="0897c-160">Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="0897c-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="0897c-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="0897c-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="0897c-162">Vložení <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> služby (dříve `IApplicationLifetime` ) do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="0897c-163">Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="0897c-164">Rozhraní obsahuje také `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="0897c-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="0897c-165">Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:</span><span class="sxs-lookup"><span data-stu-id="0897c-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="0897c-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="0897c-166">IHostLifetime</span></span>

<span data-ttu-id="0897c-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementace určuje, kdy se hostitel spustí a kdy se zastaví.</span><span class="sxs-lookup"><span data-stu-id="0897c-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="0897c-168">Použije se Poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="0897c-168">The last implementation registered is used.</span></span>

<span data-ttu-id="0897c-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je výchozí `IHostLifetime` implementací.</span><span class="sxs-lookup"><span data-stu-id="0897c-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="0897c-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="0897c-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="0897c-171">Naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="0897c-172">Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="0897c-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="0897c-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="0897c-173">IHostEnvironment</span></span>

<span data-ttu-id="0897c-174">Vložením <xref:Microsoft.Extensions.Hosting.IHostEnvironment> služby do třídy získáte informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="0897c-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="0897c-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0897c-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="0897c-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0897c-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="0897c-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="0897c-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="0897c-178">Webové aplikace implementují `IWebHostEnvironment` rozhraní, které dědí `IHostEnvironment` a přidává rozhraní [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="0897c-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0897c-179">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="0897c-179">Host configuration</span></span>

<span data-ttu-id="0897c-180">Konfigurace hostitele se používá pro vlastnosti <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementace.</span><span class="sxs-lookup"><span data-stu-id="0897c-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="0897c-181">Konfigurace hostitele je k dispozici z [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="0897c-182">Po `ConfigureAppConfiguration` nahrazení se v `HostBuilderContext.Configuration` konfiguraci aplikace nahradí.</span><span class="sxs-lookup"><span data-stu-id="0897c-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="0897c-183">Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> na `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0897c-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0897c-184">`ConfigureHostConfiguration` dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="0897c-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0897c-185">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="0897c-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0897c-186">Zprostředkovatel proměnné prostředí s argumenty předpony `DOTNET_` a příkazového řádku jsou obsaženy v `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0897c-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0897c-187">U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0897c-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="0897c-188">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="0897c-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0897c-189">Například hodnota proměnné prostředí pro se nastaví jako `ASPNETCORE_ENVIRONMENT` hodnota konfigurace hostitele pro `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="0897c-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0897c-190">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="0897c-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="0897c-191">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="0897c-191">App configuration</span></span>

<span data-ttu-id="0897c-192">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> metody `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0897c-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0897c-193">`ConfigureAppConfiguration` dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="0897c-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0897c-194">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="0897c-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="0897c-195">Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di.</span><span class="sxs-lookup"><span data-stu-id="0897c-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="0897c-196">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="0897c-197">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="0897c-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="0897c-198">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="0897c-198">Settings for all app types</span></span>

<span data-ttu-id="0897c-199">V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP.</span><span class="sxs-lookup"><span data-stu-id="0897c-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="0897c-200">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="0897c-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="0897c-201">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0897c-201">ApplicationName</span></span>

<span data-ttu-id="0897c-202">Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-202">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="0897c-203">**Klíč**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="0897c-203">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0897c-204">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-204">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-205">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-205">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="0897c-206">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="0897c-206">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="0897c-207">K nastavení této hodnoty použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="0897c-207">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="0897c-208">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0897c-208">ContentRoot</span></span>

<span data-ttu-id="0897c-209">Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="0897c-209">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="0897c-210">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="0897c-210">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="0897c-211">**Klíč**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0897c-211">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0897c-212">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-212">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-213">**Výchozí**: složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-213">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="0897c-214">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="0897c-214">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="0897c-215">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseContentRoot` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0897c-215">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="0897c-216">Další informace naleznete v tématech:</span><span class="sxs-lookup"><span data-stu-id="0897c-216">For more information, see:</span></span>

* [<span data-ttu-id="0897c-217">Základy: kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="0897c-217">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="0897c-218">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0897c-218">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="0897c-219">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0897c-219">EnvironmentName</span></span>

<span data-ttu-id="0897c-220">Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="0897c-220">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="0897c-221">Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` .</span><span class="sxs-lookup"><span data-stu-id="0897c-221">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0897c-222">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="0897c-222">Values aren't case-sensitive.</span></span>

<span data-ttu-id="0897c-223">**Klíč**: `environment`</span><span class="sxs-lookup"><span data-stu-id="0897c-223">**Key**: `environment`</span></span>  
<span data-ttu-id="0897c-224">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-224">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-225">**Výchozí**: `Production`</span><span class="sxs-lookup"><span data-stu-id="0897c-225">**Default**: `Production`</span></span>  
<span data-ttu-id="0897c-226">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="0897c-226">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="0897c-227">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseEnvironment` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0897c-227">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="0897c-228">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="0897c-228">ShutdownTimeout</span></span>

<span data-ttu-id="0897c-229">[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0897c-230">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="0897c-230">The default value is five seconds.</span></span>  <span data-ttu-id="0897c-231">Po uplynutí časového limitu hostitel:</span><span class="sxs-lookup"><span data-stu-id="0897c-231">During the timeout period, the host:</span></span>

* <span data-ttu-id="0897c-232">Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="0897c-232">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="0897c-233">Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="0897c-233">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="0897c-234">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="0897c-234">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="0897c-235">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="0897c-235">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="0897c-236">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="0897c-236">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="0897c-237">**Klíč**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="0897c-237">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="0897c-238">**Zadejte**: `int`</span><span class="sxs-lookup"><span data-stu-id="0897c-238">**Type**: `int`</span></span>  
<span data-ttu-id="0897c-239">**Výchozí hodnota**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="0897c-239">**Default**: 5 seconds</span></span>  
<span data-ttu-id="0897c-240">**Proměnná prostředí**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="0897c-240">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="0897c-241">K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="0897c-241">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="0897c-242">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="0897c-242">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="0897c-243">Zakázat opětovné načtení konfigurace aplikace při změně</span><span class="sxs-lookup"><span data-stu-id="0897c-243">Disable app configuration reload on change</span></span>

<span data-ttu-id="0897c-244">Ve [výchozím nastavení](xref:fundamentals/configuration/index#default) *appsettings.jsv* a *appSettings. { Prostředí}. JSON* se při změně souboru znovu načte.</span><span class="sxs-lookup"><span data-stu-id="0897c-244">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="0897c-245">Chcete-li zakázat toto opakované načítání v ASP.NET Core 5,0 nebo novějším, nastavte `hostBuilder:reloadConfigOnChange` klíč na `false` .</span><span class="sxs-lookup"><span data-stu-id="0897c-245">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="0897c-246">**Klíč**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0897c-246">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="0897c-247">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="0897c-247">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0897c-248">**Výchozí**: `true`</span><span class="sxs-lookup"><span data-stu-id="0897c-248">**Default**: `true`</span></span>  
<span data-ttu-id="0897c-249">**Argument příkazového řádku**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0897c-249">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="0897c-250">**Proměnná prostředí**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="0897c-250">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="0897c-251">Oddělovač dvojtečky ( `:` ) nefunguje s hierarchickými klíči proměnné prostředí na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="0897c-251">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="0897c-252">Další informace naleznete v tématu [proměnné prostředí](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="0897c-252">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="0897c-253">Nastavení pro Web Apps</span><span class="sxs-lookup"><span data-stu-id="0897c-253">Settings for web apps</span></span>

<span data-ttu-id="0897c-254">Některá nastavení hostitele se vztahují jenom na úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="0897c-254">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="0897c-255">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="0897c-255">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="0897c-256">`IWebHostBuilder`K dispozici jsou metody rozšíření pro tato nastavení.</span><span class="sxs-lookup"><span data-stu-id="0897c-256">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="0897c-257">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` , je instancí `IWebHostBuilder` , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="0897c-257">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="0897c-258">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="0897c-258">CaptureStartupErrors</span></span>

<span data-ttu-id="0897c-259">`false`V důsledku dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-259">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="0897c-260">Když `true` hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.</span><span class="sxs-lookup"><span data-stu-id="0897c-260">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="0897c-261">**Klíč**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="0897c-261">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="0897c-262">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="0897c-262">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0897c-263">**Výchozí**: výchozí nastavení `false` , pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true` .</span><span class="sxs-lookup"><span data-stu-id="0897c-263">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="0897c-264">**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="0897c-264">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="0897c-265">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="0897c-265">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="0897c-266">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="0897c-266">DetailedErrors</span></span>

<span data-ttu-id="0897c-267">Pokud je povoleno nebo když je prostředí `Development` , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="0897c-267">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="0897c-268">**Klíč**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="0897c-268">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="0897c-269">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="0897c-269">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0897c-270">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="0897c-270">**Default**: `false`</span></span>  
<span data-ttu-id="0897c-271">**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="0897c-271">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="0897c-272">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0897c-272">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="0897c-273">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="0897c-273">HostingStartupAssemblies</span></span>

<span data-ttu-id="0897c-274">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0897c-274">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="0897c-275">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-275">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="0897c-276">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="0897c-276">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="0897c-277">**Klíč**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0897c-277">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="0897c-278">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-278">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-279">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="0897c-279">**Default**: Empty string</span></span>  
<span data-ttu-id="0897c-280">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0897c-280">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="0897c-281">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0897c-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="0897c-282">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="0897c-282">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="0897c-283">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0897c-283">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="0897c-284">**Klíč**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0897c-284">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="0897c-285">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-285">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-286">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="0897c-286">**Default**: Empty string</span></span>  
<span data-ttu-id="0897c-287">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0897c-287">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="0897c-288">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0897c-288">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="0897c-289">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="0897c-289">HTTPS_Port</span></span>

<span data-ttu-id="0897c-290">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0897c-290">The HTTPS redirect port.</span></span> <span data-ttu-id="0897c-291">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="0897c-291">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="0897c-292">**Klíč**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="0897c-292">**Key**: `https_port`</span></span>  
<span data-ttu-id="0897c-293">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-293">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-294">**Výchozí**hodnota: výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="0897c-294">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="0897c-295">**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="0897c-295">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="0897c-296">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0897c-296">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="0897c-297">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="0897c-297">PreferHostingUrls</span></span>

<span data-ttu-id="0897c-298">Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným pomocí a `IWebHostBuilder` místo adres URL nakonfigurovaných `IServer` implementací.</span><span class="sxs-lookup"><span data-stu-id="0897c-298">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="0897c-299">**Klíč**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="0897c-299">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="0897c-300">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="0897c-300">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0897c-301">**Výchozí**: `true`</span><span class="sxs-lookup"><span data-stu-id="0897c-301">**Default**: `true`</span></span>  
<span data-ttu-id="0897c-302">**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="0897c-302">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="0897c-303">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="0897c-303">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="0897c-304">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="0897c-304">PreventHostingStartup</span></span>

<span data-ttu-id="0897c-305">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-305">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="0897c-306">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0897c-306">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="0897c-307">**Klíč**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="0897c-307">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="0897c-308">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="0897c-308">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0897c-309">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="0897c-309">**Default**: `false`</span></span>  
<span data-ttu-id="0897c-310">**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="0897c-310">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="0897c-311">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0897c-311">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="0897c-312">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="0897c-312">StartupAssembly</span></span>

<span data-ttu-id="0897c-313">Sestavení, ve kterém se má hledat `Startup` Třída</span><span class="sxs-lookup"><span data-stu-id="0897c-313">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="0897c-314">**Klíč**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="0897c-314">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="0897c-315">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-315">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-316">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="0897c-316">**Default**: The app's assembly</span></span>  
<span data-ttu-id="0897c-317">**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="0897c-317">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="0897c-318">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="0897c-318">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="0897c-319">`UseStartup` může převzít název sestavení ( `string` ) nebo typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="0897c-319">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="0897c-320">Pokud `UseStartup` je voláno více metod, má poslední z nich přednost.</span><span class="sxs-lookup"><span data-stu-id="0897c-320">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="0897c-321">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="0897c-321">URLs</span></span>

<span data-ttu-id="0897c-322">Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="0897c-322">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="0897c-323">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="0897c-323">For example, `http://localhost:123`.</span></span> <span data-ttu-id="0897c-324">Pomocí příkazu " \* " určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="0897c-324">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="0897c-325">Protokol ( `http://` nebo `https://` ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="0897c-325">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="0897c-326">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="0897c-326">Supported formats vary among servers.</span></span>

<span data-ttu-id="0897c-327">**Klíč**: `urls`</span><span class="sxs-lookup"><span data-stu-id="0897c-327">**Key**: `urls`</span></span>  
<span data-ttu-id="0897c-328">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-328">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-329">**Výchozí**: `http://localhost:5000` a `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="0897c-329">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="0897c-330">**Proměnná prostředí**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="0897c-330">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="0897c-331">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="0897c-331">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="0897c-332">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="0897c-332">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="0897c-333">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0897c-333">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="0897c-334">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0897c-334">WebRoot</span></span>

<span data-ttu-id="0897c-335">Vlastnost [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) Určuje relativní cestu ke statickým assetům aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-335">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="0897c-336">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="0897c-336">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="0897c-337">**Klíč**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="0897c-337">**Key**: `webroot`</span></span>  
<span data-ttu-id="0897c-338">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-338">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-339">**Výchozí**: výchozí hodnota je `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="0897c-339">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="0897c-340">Cesta k *obsahu {root}/wwwroot* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="0897c-340">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="0897c-341">**Proměnná prostředí**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="0897c-341">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="0897c-342">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseWebRoot` na `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0897c-342">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="0897c-343">Další informace naleznete v tématech:</span><span class="sxs-lookup"><span data-stu-id="0897c-343">For more information, see:</span></span>

* [<span data-ttu-id="0897c-344">Základy: web root</span><span class="sxs-lookup"><span data-stu-id="0897c-344">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="0897c-345">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0897c-345">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="0897c-346">Správa životnosti hostitele</span><span class="sxs-lookup"><span data-stu-id="0897c-346">Manage the host lifetime</span></span>

<span data-ttu-id="0897c-347">Voláním metod v sestavené <xref:Microsoft.Extensions.Hosting.IHost> implementaci spustíte a zastavíte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0897c-347">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="0897c-348">Tyto metody ovlivňují všechny  <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0897c-348">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0897c-349">Spustit</span><span class="sxs-lookup"><span data-stu-id="0897c-349">Run</span></span>

<span data-ttu-id="0897c-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.</span><span class="sxs-lookup"><span data-stu-id="0897c-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="0897c-351">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-351">RunAsync</span></span>

<span data-ttu-id="0897c-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="0897c-353">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-353">RunConsoleAsync</span></span>

<span data-ttu-id="0897c-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="0897c-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="0897c-355">Spustit</span><span class="sxs-lookup"><span data-stu-id="0897c-355">Start</span></span>

<span data-ttu-id="0897c-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="0897c-357">StartAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-357">StartAsync</span></span>

<span data-ttu-id="0897c-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí hostitele a vrátí <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="0897c-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> je volána na začátku `StartAsync` , což čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="0897c-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0897c-360">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="0897c-360">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="0897c-361">StopAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-361">StopAsync</span></span>

<span data-ttu-id="0897c-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusy o zastavení hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="0897c-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="0897c-363">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="0897c-363">WaitForShutdown</span></span>

<span data-ttu-id="0897c-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například pomocí <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="0897c-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="0897c-365">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-365">WaitForShutdownAsync</span></span>

<span data-ttu-id="0897c-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="0897c-367">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="0897c-367">External control</span></span>

<span data-ttu-id="0897c-368">Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="0897c-368">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="0897c-369">Šablony ASP.NET Core vytvoří obecného hostitele .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="0897c-369">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="0897c-370">Toto téma poskytuje informace o použití obecného hostitele .NET v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0897c-370">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="0897c-371">Informace o použití obecného hostitele .NET v konzolových aplikacích najdete v tématu [obecný hostitel .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="0897c-371">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="0897c-372">Definice hostitele</span><span class="sxs-lookup"><span data-stu-id="0897c-372">Host definition</span></span>

<span data-ttu-id="0897c-373">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="0897c-373">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="0897c-374">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="0897c-374">Dependency injection (DI)</span></span>
* <span data-ttu-id="0897c-375">Protokolování</span><span class="sxs-lookup"><span data-stu-id="0897c-375">Logging</span></span>
* <span data-ttu-id="0897c-376">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="0897c-376">Configuration</span></span>
* <span data-ttu-id="0897c-377">`IHostedService` implementaci</span><span class="sxs-lookup"><span data-stu-id="0897c-377">`IHostedService` implementations</span></span>

<span data-ttu-id="0897c-378">Při spuštění hostitele zavolá <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> každou implementaci <xref:Microsoft.Extensions.Hosting.IHostedService> zaregistrovanou v kolekci hostovaných služeb kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0897c-378">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="0897c-379">V rámci webové aplikace je jednou z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="0897c-379">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="0897c-380">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-380">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0897c-381">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="0897c-381">Set up a host</span></span>

<span data-ttu-id="0897c-382">Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve `Program` třídě.</span><span class="sxs-lookup"><span data-stu-id="0897c-382">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="0897c-383">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="0897c-383">The `Main` method:</span></span>

* <span data-ttu-id="0897c-384">Volá `CreateHostBuilder` metodu pro vytvoření a konfiguraci objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="0897c-384">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="0897c-385">Volání `Build` a `Run` metody objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="0897c-385">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="0897c-386">Webové šablony ASP.NET Core generují následující kód pro vytvoření obecného hostitele:</span><span class="sxs-lookup"><span data-stu-id="0897c-386">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="0897c-387">Následující kód vytvoří obecného hostitele pomocí úlohy jiného typu než HTTP.</span><span class="sxs-lookup"><span data-stu-id="0897c-387">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="0897c-388">`IHostedService`Implementace je přidána do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="0897c-388">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="0897c-389">Pro úlohy HTTP `Main` je metoda stejná, ale `CreateHostBuilder` volání `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="0897c-389">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="0897c-390">Předchozí kód je vygenerován šablonami ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0897c-390">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="0897c-391">Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="0897c-391">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="0897c-392">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že budou nalezeny `CreateHostBuilder` metody, které nakonfigurují hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-392">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="0897c-393">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="0897c-393">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="0897c-394">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="0897c-394">Default builder settings</span></span>

<span data-ttu-id="0897c-395"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>Metoda:</span><span class="sxs-lookup"><span data-stu-id="0897c-395">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="0897c-396">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="0897c-396">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="0897c-397">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="0897c-397">Loads host configuration from:</span></span>
  * <span data-ttu-id="0897c-398">Proměnné prostředí s předponou `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="0897c-398">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="0897c-399">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="0897c-399">Command-line arguments.</span></span>
* <span data-ttu-id="0897c-400">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="0897c-400">Loads app configuration from:</span></span>
  * <span data-ttu-id="0897c-401">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="0897c-401">*appsettings.json*.</span></span>
  * <span data-ttu-id="0897c-402">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0897c-402">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="0897c-403">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="0897c-403">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="0897c-404">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0897c-404">Environment variables.</span></span>
  * <span data-ttu-id="0897c-405">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="0897c-405">Command-line arguments.</span></span>
* <span data-ttu-id="0897c-406">Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="0897c-406">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="0897c-407">Konzola</span><span class="sxs-lookup"><span data-stu-id="0897c-407">Console</span></span>
  * <span data-ttu-id="0897c-408">Ladit</span><span class="sxs-lookup"><span data-stu-id="0897c-408">Debug</span></span>
  * <span data-ttu-id="0897c-409">EventSource</span><span class="sxs-lookup"><span data-stu-id="0897c-409">EventSource</span></span>
  * <span data-ttu-id="0897c-410">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="0897c-410">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="0897c-411">Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.</span><span class="sxs-lookup"><span data-stu-id="0897c-411">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="0897c-412">`ConfigureWebHostDefaults`Metoda:</span><span class="sxs-lookup"><span data-stu-id="0897c-412">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="0897c-413">Načte konfiguraci hostitele z proměnných prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0897c-413">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="0897c-414">Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-414">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="0897c-415">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="0897c-415">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="0897c-416">Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="0897c-416">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="0897c-417">Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , pokud je rovno `true` .</span><span class="sxs-lookup"><span data-stu-id="0897c-417">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="0897c-418">Povolí integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0897c-418">Enables IIS integration.</span></span> <span data-ttu-id="0897c-419">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="0897c-419">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="0897c-420">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="0897c-420">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0897c-421">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="0897c-421">Framework-provided services</span></span>

<span data-ttu-id="0897c-422">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="0897c-422">The following services are registered automatically:</span></span>

* [<span data-ttu-id="0897c-423">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="0897c-423">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="0897c-424">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="0897c-424">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="0897c-425">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="0897c-425">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="0897c-426">Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="0897c-426">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="0897c-427">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="0897c-427">IHostApplicationLifetime</span></span>

<span data-ttu-id="0897c-428">Vložení <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> služby (dříve `IApplicationLifetime` ) do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-428">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="0897c-429">Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-429">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="0897c-430">Rozhraní obsahuje také `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="0897c-430">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="0897c-431">Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:</span><span class="sxs-lookup"><span data-stu-id="0897c-431">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="0897c-432">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="0897c-432">IHostLifetime</span></span>

<span data-ttu-id="0897c-433"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementace určuje, kdy se hostitel spustí a kdy se zastaví.</span><span class="sxs-lookup"><span data-stu-id="0897c-433">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="0897c-434">Použije se Poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="0897c-434">The last implementation registered is used.</span></span>

<span data-ttu-id="0897c-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je výchozí `IHostLifetime` implementací.</span><span class="sxs-lookup"><span data-stu-id="0897c-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="0897c-436">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="0897c-436">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="0897c-437">Naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-437">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="0897c-438">Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="0897c-438">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="0897c-439">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="0897c-439">IHostEnvironment</span></span>

<span data-ttu-id="0897c-440">Vložením <xref:Microsoft.Extensions.Hosting.IHostEnvironment> služby do třídy získáte informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="0897c-440">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="0897c-441">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0897c-441">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="0897c-442">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0897c-442">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="0897c-443">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="0897c-443">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="0897c-444">Webové aplikace implementují `IWebHostEnvironment` rozhraní, které dědí `IHostEnvironment` a přidává rozhraní [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="0897c-444">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0897c-445">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="0897c-445">Host configuration</span></span>

<span data-ttu-id="0897c-446">Konfigurace hostitele se používá pro vlastnosti <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementace.</span><span class="sxs-lookup"><span data-stu-id="0897c-446">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="0897c-447">Konfigurace hostitele je k dispozici z [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="0897c-447">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="0897c-448">Po `ConfigureAppConfiguration` nahrazení se v `HostBuilderContext.Configuration` konfiguraci aplikace nahradí.</span><span class="sxs-lookup"><span data-stu-id="0897c-448">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="0897c-449">Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> na `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0897c-449">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="0897c-450">`ConfigureHostConfiguration` dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="0897c-450">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0897c-451">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="0897c-451">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0897c-452">Zprostředkovatel proměnné prostředí s argumenty předpony `DOTNET_` a příkazového řádku jsou obsaženy v `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0897c-452">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0897c-453">U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="0897c-453">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="0897c-454">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="0897c-454">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0897c-455">Například hodnota proměnné prostředí pro se nastaví jako `ASPNETCORE_ENVIRONMENT` hodnota konfigurace hostitele pro `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="0897c-455">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0897c-456">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="0897c-456">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="0897c-457">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="0897c-457">App configuration</span></span>

<span data-ttu-id="0897c-458">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> metody `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0897c-458">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="0897c-459">`ConfigureAppConfiguration` dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="0897c-459">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="0897c-460">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="0897c-460">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="0897c-461">Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di.</span><span class="sxs-lookup"><span data-stu-id="0897c-461">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="0897c-462">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-462">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="0897c-463">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="0897c-463">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="0897c-464">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="0897c-464">Settings for all app types</span></span>

<span data-ttu-id="0897c-465">V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP.</span><span class="sxs-lookup"><span data-stu-id="0897c-465">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="0897c-466">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="0897c-466">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="0897c-467">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="0897c-467">ApplicationName</span></span>

<span data-ttu-id="0897c-468">Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-468">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="0897c-469">**Klíč**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="0897c-469">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0897c-470">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-470">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-471">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-471">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="0897c-472">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="0897c-472">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="0897c-473">K nastavení této hodnoty použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="0897c-473">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="0897c-474">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0897c-474">ContentRoot</span></span>

<span data-ttu-id="0897c-475">Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="0897c-475">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="0897c-476">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="0897c-476">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="0897c-477">**Klíč**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0897c-477">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0897c-478">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-478">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-479">**Výchozí**: složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-479">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="0897c-480">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="0897c-480">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="0897c-481">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseContentRoot` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0897c-481">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="0897c-482">Další informace naleznete v tématech:</span><span class="sxs-lookup"><span data-stu-id="0897c-482">For more information, see:</span></span>

* [<span data-ttu-id="0897c-483">Základy: kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="0897c-483">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="0897c-484">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0897c-484">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="0897c-485">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="0897c-485">EnvironmentName</span></span>

<span data-ttu-id="0897c-486">Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="0897c-486">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="0897c-487">Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` .</span><span class="sxs-lookup"><span data-stu-id="0897c-487">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0897c-488">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="0897c-488">Values aren't case-sensitive.</span></span>

<span data-ttu-id="0897c-489">**Klíč**: `environment`</span><span class="sxs-lookup"><span data-stu-id="0897c-489">**Key**: `environment`</span></span>  
<span data-ttu-id="0897c-490">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-490">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-491">**Výchozí**: `Production`</span><span class="sxs-lookup"><span data-stu-id="0897c-491">**Default**: `Production`</span></span>  
<span data-ttu-id="0897c-492">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="0897c-492">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="0897c-493">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseEnvironment` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0897c-493">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="0897c-494">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="0897c-494">ShutdownTimeout</span></span>

<span data-ttu-id="0897c-495">[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0897c-496">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="0897c-496">The default value is five seconds.</span></span>  <span data-ttu-id="0897c-497">Po uplynutí časového limitu hostitel:</span><span class="sxs-lookup"><span data-stu-id="0897c-497">During the timeout period, the host:</span></span>

* <span data-ttu-id="0897c-498">Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="0897c-498">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="0897c-499">Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="0897c-499">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="0897c-500">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="0897c-500">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="0897c-501">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="0897c-501">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="0897c-502">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="0897c-502">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="0897c-503">**Klíč**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="0897c-503">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="0897c-504">**Zadejte**: `int`</span><span class="sxs-lookup"><span data-stu-id="0897c-504">**Type**: `int`</span></span>  
<span data-ttu-id="0897c-505">**Výchozí hodnota**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="0897c-505">**Default**: 5 seconds</span></span>  
<span data-ttu-id="0897c-506">**Proměnná prostředí**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="0897c-506">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="0897c-507">K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="0897c-507">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="0897c-508">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="0897c-508">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="0897c-509">Nastavení pro Web Apps</span><span class="sxs-lookup"><span data-stu-id="0897c-509">Settings for web apps</span></span>

<span data-ttu-id="0897c-510">Některá nastavení hostitele se vztahují jenom na úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="0897c-510">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="0897c-511">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="0897c-511">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="0897c-512">`IWebHostBuilder`K dispozici jsou metody rozšíření pro tato nastavení.</span><span class="sxs-lookup"><span data-stu-id="0897c-512">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="0897c-513">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` , je instancí `IWebHostBuilder` , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="0897c-513">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="0897c-514">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="0897c-514">CaptureStartupErrors</span></span>

<span data-ttu-id="0897c-515">`false`V důsledku dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-515">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="0897c-516">Když `true` hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.</span><span class="sxs-lookup"><span data-stu-id="0897c-516">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="0897c-517">**Klíč**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="0897c-517">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="0897c-518">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="0897c-518">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0897c-519">**Výchozí**: výchozí nastavení `false` , pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true` .</span><span class="sxs-lookup"><span data-stu-id="0897c-519">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="0897c-520">**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="0897c-520">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="0897c-521">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="0897c-521">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="0897c-522">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="0897c-522">DetailedErrors</span></span>

<span data-ttu-id="0897c-523">Pokud je povoleno nebo když je prostředí `Development` , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="0897c-523">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="0897c-524">**Klíč**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="0897c-524">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="0897c-525">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="0897c-525">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0897c-526">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="0897c-526">**Default**: `false`</span></span>  
<span data-ttu-id="0897c-527">**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="0897c-527">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="0897c-528">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0897c-528">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="0897c-529">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="0897c-529">HostingStartupAssemblies</span></span>

<span data-ttu-id="0897c-530">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0897c-530">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="0897c-531">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-531">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="0897c-532">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="0897c-532">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="0897c-533">**Klíč**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0897c-533">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="0897c-534">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-534">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-535">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="0897c-535">**Default**: Empty string</span></span>  
<span data-ttu-id="0897c-536">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0897c-536">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="0897c-537">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0897c-537">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="0897c-538">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="0897c-538">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="0897c-539">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0897c-539">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="0897c-540">**Klíč**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="0897c-540">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="0897c-541">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-541">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-542">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="0897c-542">**Default**: Empty string</span></span>  
<span data-ttu-id="0897c-543">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="0897c-543">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="0897c-544">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0897c-544">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="0897c-545">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="0897c-545">HTTPS_Port</span></span>

<span data-ttu-id="0897c-546">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0897c-546">The HTTPS redirect port.</span></span> <span data-ttu-id="0897c-547">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="0897c-547">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="0897c-548">**Klíč**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="0897c-548">**Key**: `https_port`</span></span>  
<span data-ttu-id="0897c-549">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-549">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-550">**Výchozí**hodnota: výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="0897c-550">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="0897c-551">**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="0897c-551">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="0897c-552">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0897c-552">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="0897c-553">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="0897c-553">PreferHostingUrls</span></span>

<span data-ttu-id="0897c-554">Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným pomocí a `IWebHostBuilder` místo adres URL nakonfigurovaných `IServer` implementací.</span><span class="sxs-lookup"><span data-stu-id="0897c-554">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="0897c-555">**Klíč**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="0897c-555">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="0897c-556">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="0897c-556">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0897c-557">**Výchozí**: `true`</span><span class="sxs-lookup"><span data-stu-id="0897c-557">**Default**: `true`</span></span>  
<span data-ttu-id="0897c-558">**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="0897c-558">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="0897c-559">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="0897c-559">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="0897c-560">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="0897c-560">PreventHostingStartup</span></span>

<span data-ttu-id="0897c-561">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-561">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="0897c-562">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0897c-562">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="0897c-563">**Klíč**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="0897c-563">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="0897c-564">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="0897c-564">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="0897c-565">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="0897c-565">**Default**: `false`</span></span>  
<span data-ttu-id="0897c-566">**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="0897c-566">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="0897c-567">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="0897c-567">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="0897c-568">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="0897c-568">StartupAssembly</span></span>

<span data-ttu-id="0897c-569">Sestavení, ve kterém se má hledat `Startup` Třída</span><span class="sxs-lookup"><span data-stu-id="0897c-569">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="0897c-570">**Klíč**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="0897c-570">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="0897c-571">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-571">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-572">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="0897c-572">**Default**: The app's assembly</span></span>  
<span data-ttu-id="0897c-573">**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="0897c-573">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="0897c-574">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="0897c-574">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="0897c-575">`UseStartup` může převzít název sestavení ( `string` ) nebo typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="0897c-575">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="0897c-576">Pokud `UseStartup` je voláno více metod, má poslední z nich přednost.</span><span class="sxs-lookup"><span data-stu-id="0897c-576">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="0897c-577">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="0897c-577">URLs</span></span>

<span data-ttu-id="0897c-578">Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="0897c-578">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="0897c-579">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="0897c-579">For example, `http://localhost:123`.</span></span> <span data-ttu-id="0897c-580">Pomocí příkazu " \* " určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="0897c-580">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="0897c-581">Protokol ( `http://` nebo `https://` ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="0897c-581">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="0897c-582">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="0897c-582">Supported formats vary among servers.</span></span>

<span data-ttu-id="0897c-583">**Klíč**: `urls`</span><span class="sxs-lookup"><span data-stu-id="0897c-583">**Key**: `urls`</span></span>  
<span data-ttu-id="0897c-584">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-584">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-585">**Výchozí**: `http://localhost:5000` a `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="0897c-585">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="0897c-586">**Proměnná prostředí**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="0897c-586">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="0897c-587">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="0897c-587">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="0897c-588">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="0897c-588">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="0897c-589">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0897c-589">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="0897c-590">WebRoot</span><span class="sxs-lookup"><span data-stu-id="0897c-590">WebRoot</span></span>

<span data-ttu-id="0897c-591">Vlastnost [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) Určuje relativní cestu ke statickým assetům aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-591">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="0897c-592">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="0897c-592">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="0897c-593">**Klíč**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="0897c-593">**Key**: `webroot`</span></span>  
<span data-ttu-id="0897c-594">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-594">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-595">**Výchozí**: výchozí hodnota je `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="0897c-595">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="0897c-596">Cesta k *obsahu {root}/wwwroot* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="0897c-596">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="0897c-597">**Proměnná prostředí**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="0897c-597">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="0897c-598">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseWebRoot` na `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0897c-598">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="0897c-599">Další informace naleznete v tématech:</span><span class="sxs-lookup"><span data-stu-id="0897c-599">For more information, see:</span></span>

* [<span data-ttu-id="0897c-600">Základy: web root</span><span class="sxs-lookup"><span data-stu-id="0897c-600">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="0897c-601">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="0897c-601">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="0897c-602">Správa životnosti hostitele</span><span class="sxs-lookup"><span data-stu-id="0897c-602">Manage the host lifetime</span></span>

<span data-ttu-id="0897c-603">Voláním metod v sestavené <xref:Microsoft.Extensions.Hosting.IHost> implementaci spustíte a zastavíte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0897c-603">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="0897c-604">Tyto metody ovlivňují všechny  <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0897c-604">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0897c-605">Spustit</span><span class="sxs-lookup"><span data-stu-id="0897c-605">Run</span></span>

<span data-ttu-id="0897c-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.</span><span class="sxs-lookup"><span data-stu-id="0897c-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="0897c-607">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-607">RunAsync</span></span>

<span data-ttu-id="0897c-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="0897c-609">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-609">RunConsoleAsync</span></span>

<span data-ttu-id="0897c-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="0897c-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="0897c-611">Spustit</span><span class="sxs-lookup"><span data-stu-id="0897c-611">Start</span></span>

<span data-ttu-id="0897c-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="0897c-613">StartAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-613">StartAsync</span></span>

<span data-ttu-id="0897c-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí hostitele a vrátí <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="0897c-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> je volána na začátku `StartAsync` , což čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="0897c-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0897c-616">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="0897c-616">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="0897c-617">StopAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-617">StopAsync</span></span>

<span data-ttu-id="0897c-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusy o zastavení hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="0897c-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="0897c-619">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="0897c-619">WaitForShutdown</span></span>

<span data-ttu-id="0897c-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například pomocí <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="0897c-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="0897c-621">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-621">WaitForShutdownAsync</span></span>

<span data-ttu-id="0897c-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="0897c-623">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="0897c-623">External control</span></span>

<span data-ttu-id="0897c-624">Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="0897c-624">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="0897c-625">ASP.NET Core aplikace nakonfigurují a spouštějí hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-625">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="0897c-626">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="0897c-626">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="0897c-627">Tento článek se zabývá ASP.NET Core obecný hostitel ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ), který se používá pro aplikace, které nezpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="0897c-627">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="0897c-628">Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele, aby bylo možné rozšířit pole hostitelských scénářů.</span><span class="sxs-lookup"><span data-stu-id="0897c-628">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="0897c-629">Zasílání zpráv, úloh na pozadí a dalších úloh nevyužívajících HTTP na základě výhody obecného hostitele z různých možností, jako je konfigurace, vkládání závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="0897c-629">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="0897c-630">Obecný hostitel je v ASP.NET Core 2,1 novinkou a není vhodný pro scénáře hostování webů.</span><span class="sxs-lookup"><span data-stu-id="0897c-630">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="0897c-631">U scénářů pro hostování webů použijte [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="0897c-631">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="0897c-632">Obecný hostitel nahradí webového hostitele v budoucí verzi a bude fungovat jako primární rozhraní API v rámci scénářů HTTP i non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="0897c-632">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="0897c-633">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0897c-633">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0897c-634">Při spuštění ukázkové aplikace v [Visual Studio Code](https://code.visualstudio.com/)použijte *externí nebo integrovaný terminál*.</span><span class="sxs-lookup"><span data-stu-id="0897c-634">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="0897c-635">Nespouštějte ukázku v `internalConsole` .</span><span class="sxs-lookup"><span data-stu-id="0897c-635">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="0897c-636">Nastavení konzoly v Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="0897c-636">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="0897c-637">Otevřete soubor *. VSCode/launch.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="0897c-637">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="0897c-638">V konfiguraci **spuštění .NET Core (konzola)** vyhledejte položku **Konzola** .</span><span class="sxs-lookup"><span data-stu-id="0897c-638">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="0897c-639">Nastavte hodnotu buď `externalTerminal` nebo `integratedTerminal` .</span><span class="sxs-lookup"><span data-stu-id="0897c-639">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="0897c-640">Úvod</span><span class="sxs-lookup"><span data-stu-id="0897c-640">Introduction</span></span>

<span data-ttu-id="0897c-641">Knihovna obecných hostitelů je k dispozici v <xref:Microsoft.Extensions.Hosting> oboru názvů a poskytuje balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) .</span><span class="sxs-lookup"><span data-stu-id="0897c-641">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="0897c-642">`Microsoft.Extensions.Hosting`Balíček je součástí [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="0897c-642">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="0897c-643"><xref:Microsoft.Extensions.Hosting.IHostedService> je vstupním bodem pro provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="0897c-643"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="0897c-644">Každá <xref:Microsoft.Extensions.Hosting.IHostedService> implementace se spustí v pořadí [Registrace služby v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="0897c-644">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="0897c-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> je volána při <xref:Microsoft.Extensions.Hosting.IHostedService> spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> je volána v pořadí zpětné registrace při bezproblémovém ukončení hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="0897c-646">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="0897c-646">Set up a host</span></span>

<span data-ttu-id="0897c-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> je hlavní součástí, kterou knihovny a aplikace používají k inicializaci, sestavování a spouštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="0897c-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="0897c-648">Možnosti</span><span class="sxs-lookup"><span data-stu-id="0897c-648">Options</span></span>

<span data-ttu-id="0897c-649"><xref:Microsoft.Extensions.Hosting.HostOptions> nakonfigurujte možnosti pro <xref:Microsoft.Extensions.Hosting.IHost> .</span><span class="sxs-lookup"><span data-stu-id="0897c-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="0897c-650">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="0897c-650">Shutdown timeout</span></span>

<span data-ttu-id="0897c-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> Nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="0897c-652">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="0897c-652">The default value is five seconds.</span></span>

<span data-ttu-id="0897c-653">Následující konfigurace možnosti v nástroji `Program.Main` zvyšuje výchozí časový limit pro vypnutí v pěti sekundách na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="0897c-653">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="0897c-654">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="0897c-654">Default services</span></span>

<span data-ttu-id="0897c-655">Během inicializace hostitele jsou registrovány následující služby:</span><span class="sxs-lookup"><span data-stu-id="0897c-655">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="0897c-656">[Prostředí](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="0897c-656">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="0897c-657">[Konfigurace](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="0897c-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="0897c-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="0897c-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="0897c-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="0897c-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="0897c-660">[Možnosti](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="0897c-660">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="0897c-661">[Protokolování](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="0897c-661">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="0897c-662">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="0897c-662">Host configuration</span></span>

<span data-ttu-id="0897c-663">Konfiguraci hostitele vytvořil:</span><span class="sxs-lookup"><span data-stu-id="0897c-663">Host configuration is created by:</span></span>

* <span data-ttu-id="0897c-664">Voláním metod rozšíření pro <xref:Microsoft.Extensions.Hosting.IHostBuilder> nastavte [kořen obsahu](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="0897c-664">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="0897c-665">Čtení konfigurace od zprostředkovatelů konfigurace v nástroji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-665">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="0897c-666">Metody rozšíření</span><span class="sxs-lookup"><span data-stu-id="0897c-666">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="0897c-667">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="0897c-667">Application key (name)</span></span>

<span data-ttu-id="0897c-668">Vlastnost [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-668">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="0897c-669">Chcete-li nastavit hodnotu explicitně, použijte [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="0897c-669">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="0897c-670">**Klíč**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="0897c-670">**Key**: `applicationName`</span></span>  
<span data-ttu-id="0897c-671">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-671">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-672">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-672">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="0897c-673">**Nastavit pomocí**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="0897c-673">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="0897c-674">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0897c-674">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="0897c-675">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="0897c-675">Content root</span></span>

<span data-ttu-id="0897c-676">Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="0897c-676">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="0897c-677">**Klíč**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="0897c-677">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="0897c-678">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-678">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-679">**Výchozí**: výchozí nastavení složky, kde se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-679">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="0897c-680">**Nastavit pomocí**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="0897c-680">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="0897c-681">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0897c-681">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="0897c-682">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="0897c-682">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="0897c-683">Další informace najdete v tématu [základy: kořen obsahu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="0897c-683">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="0897c-684">Prostředí</span><span class="sxs-lookup"><span data-stu-id="0897c-684">Environment</span></span>

<span data-ttu-id="0897c-685">Nastaví [prostředí](xref:fundamentals/environments)aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-685">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0897c-686">**Klíč**: `environment`</span><span class="sxs-lookup"><span data-stu-id="0897c-686">**Key**: `environment`</span></span>  
<span data-ttu-id="0897c-687">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="0897c-687">**Type**: `string`</span></span>  
<span data-ttu-id="0897c-688">**Výchozí**: `Production`</span><span class="sxs-lookup"><span data-stu-id="0897c-688">**Default**: `Production`</span></span>  
<span data-ttu-id="0897c-689">**Nastavit pomocí**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="0897c-689">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="0897c-690">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="0897c-690">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="0897c-691">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="0897c-691">The environment can be set to any value.</span></span> <span data-ttu-id="0897c-692">Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` .</span><span class="sxs-lookup"><span data-stu-id="0897c-692">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="0897c-693">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="0897c-693">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="0897c-694">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="0897c-694">ConfigureHostConfiguration</span></span>

<span data-ttu-id="0897c-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="0897c-696">Konfigurace hostitele slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-696">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="0897c-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="0897c-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="0897c-698">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="0897c-698">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="0897c-699">Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé.</span><span class="sxs-lookup"><span data-stu-id="0897c-699">No providers are included by default.</span></span> <span data-ttu-id="0897c-700">Musíte explicitně určit, v jakém poskytovateli konfigurace aplikace vyžaduje <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , včetně:</span><span class="sxs-lookup"><span data-stu-id="0897c-700">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="0897c-701">Konfigurace souboru (například z *hostsettings.js* souboru).</span><span class="sxs-lookup"><span data-stu-id="0897c-701">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="0897c-702">Konfigurace proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0897c-702">Environment variable configuration.</span></span>
* <span data-ttu-id="0897c-703">Konfigurace argumentů příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="0897c-703">Command-line argument configuration.</span></span>
* <span data-ttu-id="0897c-704">Všichni další povinní poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0897c-704">Any other required configuration providers.</span></span>

<span data-ttu-id="0897c-705">Konfigurace souborů hostitele je povolená zadáním základní cesty aplikace `SetBasePath` a následným voláním jednoho z [poskytovatelů konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0897c-705">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="0897c-706">Ukázková aplikace používá soubor JSON, *hostsettings.jszapnutý*, a volá <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> k využívání nastavení konfigurace hostitele souboru.</span><span class="sxs-lookup"><span data-stu-id="0897c-706">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="0897c-707">Chcete-li přidat [konfiguraci proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-707">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="0897c-708">`AddEnvironmentVariables` přijme volitelnou uživatelem definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="0897c-708">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="0897c-709">Ukázková aplikace používá předponu `PREFIX_` .</span><span class="sxs-lookup"><span data-stu-id="0897c-709">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="0897c-710">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="0897c-710">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="0897c-711">Když je hostitel ukázkové aplikace nakonfigurovaný, hodnota proměnné prostředí `PREFIX_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro tento `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="0897c-711">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="0897c-712">Během vývoje při používání sady [Visual Studio](https://visualstudio.microsoft.com) nebo při spuštění aplikace s se `dotnet run` mohou proměnné prostředí nastavit v souboru *Properties/launchSettings.js* .</span><span class="sxs-lookup"><span data-stu-id="0897c-712">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="0897c-713">V [Visual Studio Code](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *. VSCode/launch.js* při vývoji.</span><span class="sxs-lookup"><span data-stu-id="0897c-713">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="0897c-714">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0897c-714">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="0897c-715">[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-715">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="0897c-716">K povolení argumentů příkazového řádku pro přepsání konfigurace poskytované předchozími poskytovateli konfigurace se přidá poslední konfigurace příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="0897c-716">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="0897c-717">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="0897c-717">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="0897c-718">Další konfiguraci lze poskytnout pomocí klíčů [ApplicationName](#application-key-name) a [contentRoot](#content-root) .</span><span class="sxs-lookup"><span data-stu-id="0897c-718">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="0897c-719">Příklad `HostBuilder` Konfigurace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="0897c-719">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="0897c-720">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="0897c-720">ConfigureAppConfiguration</span></span>

<span data-ttu-id="0897c-721">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementace.</span><span class="sxs-lookup"><span data-stu-id="0897c-721">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="0897c-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0897c-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="0897c-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="0897c-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="0897c-724">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="0897c-724">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="0897c-725">Konfigurace vytvořená nástrojem <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-725">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="0897c-726">Konfigurace aplikace automaticky přijímá konfiguraci hostitele, kterou poskytuje [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="0897c-726">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="0897c-727">Příklad konfigurace aplikace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="0897c-727">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="0897c-728">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="0897c-728">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="0897c-729">*appsettings.Development.js*:</span><span class="sxs-lookup"><span data-stu-id="0897c-729">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="0897c-730">*appsettings.Production.js*:</span><span class="sxs-lookup"><span data-stu-id="0897c-730">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="0897c-731">Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="0897c-731">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="0897c-732">Ukázková aplikace přesune své soubory nastavení aplikace JSON a *hostsettings.js* s touto `<Content>` položkou:</span><span class="sxs-lookup"><span data-stu-id="0897c-732">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="0897c-733">Metody rozšíření konfigurace, například <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, například [Microsoft.Extensions.Configuration.Jsv](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft.Extensions.Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="0897c-733">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="0897c-734">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), tyto balíčky musí být kromě balíčku Core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) přidány do projektu.</span><span class="sxs-lookup"><span data-stu-id="0897c-734">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="0897c-735">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0897c-735">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="0897c-736">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="0897c-736">ConfigureServices</span></span>

<span data-ttu-id="0897c-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> Přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0897c-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="0897c-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="0897c-739">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0897c-739">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="0897c-740">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="0897c-740">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="0897c-741">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá `AddHostedService` metodu rozšíření k přidání služby pro události života, `LifetimeEventsHostedService` a časově omezená úloha na pozadí, `TimedHostedService` do aplikace:</span><span class="sxs-lookup"><span data-stu-id="0897c-741">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="0897c-742">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="0897c-742">ConfigureLogging</span></span>

<span data-ttu-id="0897c-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> Přidá delegáta pro konfiguraci poskytnutého <xref:Microsoft.Extensions.Logging.ILoggingBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0897c-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="0897c-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> může být voláno vícekrát s přísadou výsledků.</span><span class="sxs-lookup"><span data-stu-id="0897c-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="0897c-745">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="0897c-745">UseConsoleLifetime</span></span>

<span data-ttu-id="0897c-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="0897c-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="0897c-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="0897c-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je předem registrován jako výchozí implementace životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="0897c-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="0897c-749">Je použita poslední registrovaná doba života.</span><span class="sxs-lookup"><span data-stu-id="0897c-749">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="0897c-750">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="0897c-750">Container configuration</span></span>

<span data-ttu-id="0897c-751">Pro podporu zapojení do jiných kontejnerů může hostitel přijmout <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> .</span><span class="sxs-lookup"><span data-stu-id="0897c-751">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="0897c-752">Poskytnutí továrny není součástí registrace DI Container, ale je místo toho hostitel, který se používá k vytvoření konkrétního kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="0897c-752">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="0897c-753">[UseServiceProviderFactory (IServiceProviderFactory &lt; TContainerBuilder &gt; )](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí továrnu, která byla použita k vytvoření poskytovatele služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="0897c-754">Vlastní konfigurace kontejneru je spravovaná <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metodou.</span><span class="sxs-lookup"><span data-stu-id="0897c-754">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="0897c-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> poskytuje silné typové prostředí pro konfiguraci kontejneru nad podkladovým rozhraním API hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="0897c-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="0897c-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="0897c-757">Vytvoření kontejneru služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0897c-757">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="0897c-758">Poskytněte objekt pro vytváření kontejnerů služby:</span><span class="sxs-lookup"><span data-stu-id="0897c-758">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="0897c-759">Použijte objekt pro vytváření a nakonfigurujte vlastní kontejner služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0897c-759">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="0897c-760">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="0897c-760">Extensibility</span></span>

<span data-ttu-id="0897c-761">Rozšiřitelnost hostitelů se provádí s metodami rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0897c-761">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="0897c-762">Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementaci s příkladem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) znázorněným v <xref:fundamentals/host/hosted-services> .</span><span class="sxs-lookup"><span data-stu-id="0897c-762">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="0897c-763">Aplikace vytváří `UseHostedService` metodu rozšíření pro registraci hostované služby, která byla předána `T` :</span><span class="sxs-lookup"><span data-stu-id="0897c-763">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="0897c-764">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="0897c-764">Manage the host</span></span>

<span data-ttu-id="0897c-765"><xref:Microsoft.Extensions.Hosting.IHost>Implementace zodpovídá za spouštění a zastavování <xref:Microsoft.Extensions.Hosting.IHostedService> implementací, které jsou zaregistrované v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0897c-765">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="0897c-766">Spustit</span><span class="sxs-lookup"><span data-stu-id="0897c-766">Run</span></span>

<span data-ttu-id="0897c-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud není hostitel vypnutý:</span><span class="sxs-lookup"><span data-stu-id="0897c-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="0897c-768">RunAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-768">RunAsync</span></span>

<span data-ttu-id="0897c-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí:</span><span class="sxs-lookup"><span data-stu-id="0897c-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="0897c-770">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-770">RunConsoleAsync</span></span>

<span data-ttu-id="0897c-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="0897c-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="0897c-772">Spustit a StopAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-772">Start and StopAsync</span></span>

<span data-ttu-id="0897c-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="0897c-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="0897c-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusy o zastavení hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="0897c-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="0897c-775">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-775">StartAsync and StopAsync</span></span>

<span data-ttu-id="0897c-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0897c-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="0897c-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> ukončí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0897c-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="0897c-778">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="0897c-778">WaitForShutdown</span></span>

<span data-ttu-id="0897c-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>je aktivována prostřednictvím <xref:Microsoft.Extensions.Hosting.IHostLifetime> , například `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (naslouchá pro <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="0897c-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="0897c-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="0897c-781">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="0897c-781">WaitForShutdownAsync</span></span>

<span data-ttu-id="0897c-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="0897c-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="0897c-783">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="0897c-783">External control</span></span>

<span data-ttu-id="0897c-784">Externí kontrolu hostitele lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="0897c-784">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="0897c-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> je volána na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> , což čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="0897c-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="0897c-786">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="0897c-786">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="0897c-787">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="0897c-787">IHostingEnvironment interface</span></span>

<span data-ttu-id="0897c-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> poskytuje informace o hostitelském prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="0897c-789">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání, aby <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="0897c-789">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="0897c-790">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0897c-790">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="0897c-791">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="0897c-791">IApplicationLifetime interface</span></span>

<span data-ttu-id="0897c-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> umožňuje aktivity po spuštění a vypnutí, včetně řádných požadavků na vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="0897c-793">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-793">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="0897c-794">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="0897c-794">Cancellation Token</span></span> | <span data-ttu-id="0897c-795">Aktivované při&#8230;</span><span class="sxs-lookup"><span data-stu-id="0897c-795">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="0897c-796">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="0897c-796">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="0897c-797">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-797">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="0897c-798">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="0897c-798">All requests should be processed.</span></span> <span data-ttu-id="0897c-799">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="0897c-799">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="0897c-800">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0897c-800">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="0897c-801">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="0897c-801">Requests may still be processing.</span></span> <span data-ttu-id="0897c-802">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="0897c-802">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="0897c-803">Konstruktor – vloží <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> službu do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="0897c-803">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="0897c-804">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání konstruktoru do `LifetimeEventsHostedService` třídy ( <xref:Microsoft.Extensions.Hosting.IHostedService> implementace) pro registraci událostí.</span><span class="sxs-lookup"><span data-stu-id="0897c-804">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="0897c-805">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="0897c-805">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="0897c-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> požaduje ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0897c-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="0897c-807">Následující třída používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> k bezproblémovému vypnutí aplikace při `Shutdown` volání metody třídy:</span><span class="sxs-lookup"><span data-stu-id="0897c-807">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="0897c-808">Další materiály</span><span class="sxs-lookup"><span data-stu-id="0897c-808">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
