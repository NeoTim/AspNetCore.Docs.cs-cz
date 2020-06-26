---
title: Obecný hostitel .NET
author: rick-anderson
description: Seznamte se s obecným hostitelem .NET Core, který je zodpovědný za spouštění a správu životního cyklu aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: eb0797a26393ce425d07ea9a97109b7fe8983f91
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407873"
---
# <a name="net-generic-host"></a><span data-ttu-id="80db5-103">Obecný hostitel .NET</span><span class="sxs-lookup"><span data-stu-id="80db5-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="80db5-104">Šablony ASP.NET Core vytvoří obecného hostitele .NET Core, <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="80db5-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="80db5-105">Definice hostitele</span><span class="sxs-lookup"><span data-stu-id="80db5-105">Host definition</span></span>

<span data-ttu-id="80db5-106">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="80db5-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="80db5-107">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="80db5-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="80db5-108">protokolování</span><span class="sxs-lookup"><span data-stu-id="80db5-108">Logging</span></span>
* <span data-ttu-id="80db5-109">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="80db5-109">Configuration</span></span>
* <span data-ttu-id="80db5-110">`IHostedService`implementaci</span><span class="sxs-lookup"><span data-stu-id="80db5-110">`IHostedService` implementations</span></span>

<span data-ttu-id="80db5-111">Při spuštění hostitele volá `IHostedService.StartAsync` každou implementaci <xref:Microsoft.Extensions.Hosting.IHostedService> , kterou najde v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="80db5-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="80db5-112">V rámci webové aplikace je jednou z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="80db5-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="80db5-113">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="80db5-114">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="80db5-114">Set up a host</span></span>

<span data-ttu-id="80db5-115">Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve `Program` třídě.</span><span class="sxs-lookup"><span data-stu-id="80db5-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="80db5-116">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="80db5-116">The `Main` method:</span></span>

* <span data-ttu-id="80db5-117">Volá `CreateHostBuilder` metodu pro vytvoření a konfiguraci objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="80db5-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="80db5-118">Volání `Build` a `Run` metody objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="80db5-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="80db5-119">Webové šablony ASP.NET Core generují následující kód pro vytvoření obecného hostitele:</span><span class="sxs-lookup"><span data-stu-id="80db5-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="80db5-120">Následující kód vytvoří obecného hostitele pomocí úlohy jiného typu než HTTP.</span><span class="sxs-lookup"><span data-stu-id="80db5-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="80db5-121">`IHostedService`Implementace je přidána do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="80db5-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="80db5-122">Pro úlohy HTTP `Main` je metoda stejná, ale `CreateHostBuilder` volání `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="80db5-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="80db5-123">Předchozí kód je vygenerován šablonami ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="80db5-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="80db5-124">Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="80db5-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="80db5-125">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že budou nalezeny `CreateHostBuilder` metody, které nakonfigurují hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="80db5-126">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="80db5-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="80db5-127">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="80db5-127">Default builder settings</span></span>

<span data-ttu-id="80db5-128"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="80db5-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="80db5-129">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="80db5-130">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="80db5-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="80db5-131">Proměnné prostředí s předponou `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="80db5-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="80db5-132">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="80db5-132">Command-line arguments.</span></span>
* <span data-ttu-id="80db5-133">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="80db5-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="80db5-134">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="80db5-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="80db5-135">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="80db5-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="80db5-136">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="80db5-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="80db5-137">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="80db5-137">Environment variables.</span></span>
  * <span data-ttu-id="80db5-138">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="80db5-138">Command-line arguments.</span></span>
* <span data-ttu-id="80db5-139">Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="80db5-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="80db5-140">Konzola</span><span class="sxs-lookup"><span data-stu-id="80db5-140">Console</span></span>
  * <span data-ttu-id="80db5-141">Ladění</span><span class="sxs-lookup"><span data-stu-id="80db5-141">Debug</span></span>
  * <span data-ttu-id="80db5-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="80db5-142">EventSource</span></span>
  * <span data-ttu-id="80db5-143">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="80db5-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="80db5-144">Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.</span><span class="sxs-lookup"><span data-stu-id="80db5-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="80db5-145">`ConfigureWebHostDefaults`Metoda:</span><span class="sxs-lookup"><span data-stu-id="80db5-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="80db5-146">Načte konfiguraci hostitele z proměnných prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="80db5-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="80db5-147">Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="80db5-148">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="80db5-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="80db5-149">Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="80db5-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="80db5-150">Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , pokud je rovno `true` .</span><span class="sxs-lookup"><span data-stu-id="80db5-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="80db5-151">Povolí integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="80db5-151">Enables IIS integration.</span></span> <span data-ttu-id="80db5-152">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="80db5-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="80db5-153">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="80db5-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="80db5-154">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="80db5-154">Framework-provided services</span></span>

<span data-ttu-id="80db5-155">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="80db5-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="80db5-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="80db5-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="80db5-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="80db5-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="80db5-158">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="80db5-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="80db5-159">Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="80db5-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="80db5-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="80db5-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="80db5-161">Vložení <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> služby (dříve `IApplicationLifetime` ) do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="80db5-162">Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="80db5-163">Rozhraní obsahuje také `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="80db5-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="80db5-164">Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:</span><span class="sxs-lookup"><span data-stu-id="80db5-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="80db5-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="80db5-165">IHostLifetime</span></span>

<span data-ttu-id="80db5-166"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementace určuje, kdy se hostitel spustí a kdy se zastaví.</span><span class="sxs-lookup"><span data-stu-id="80db5-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="80db5-167">Použije se Poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="80db5-167">The last implementation registered is used.</span></span>

<span data-ttu-id="80db5-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementací.</span><span class="sxs-lookup"><span data-stu-id="80db5-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="80db5-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="80db5-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="80db5-170">Naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="80db5-171">Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="80db5-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="80db5-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="80db5-172">IHostEnvironment</span></span>

<span data-ttu-id="80db5-173">Vložením <xref:Microsoft.Extensions.Hosting.IHostEnvironment> služby do třídy získáte informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="80db5-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="80db5-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="80db5-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="80db5-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="80db5-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="80db5-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="80db5-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="80db5-177">Webové aplikace implementují `IWebHostEnvironment` rozhraní, které dědí `IHostEnvironment` a přidává rozhraní [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="80db5-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="80db5-178">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="80db5-178">Host configuration</span></span>

<span data-ttu-id="80db5-179">Konfigurace hostitele se používá pro vlastnosti <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementace.</span><span class="sxs-lookup"><span data-stu-id="80db5-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="80db5-180">Konfigurace hostitele je k dispozici z [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="80db5-181">Po `ConfigureAppConfiguration` nahrazení se v `HostBuilderContext.Configuration` konfiguraci aplikace nahradí.</span><span class="sxs-lookup"><span data-stu-id="80db5-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="80db5-182">Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> na `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="80db5-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="80db5-183">`ConfigureHostConfiguration`dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="80db5-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="80db5-184">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="80db5-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="80db5-185">Zprostředkovatel proměnné prostředí s argumenty předpony `DOTNET_` a příkazového řádku jsou obsaženy v `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="80db5-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="80db5-186">U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="80db5-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="80db5-187">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="80db5-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="80db5-188">Například hodnota proměnné prostředí pro se nastaví jako `ASPNETCORE_ENVIRONMENT` hodnota konfigurace hostitele pro `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="80db5-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="80db5-189">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="80db5-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="80db5-190">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="80db5-190">App configuration</span></span>

<span data-ttu-id="80db5-191">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> metody `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="80db5-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="80db5-192">`ConfigureAppConfiguration`dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="80db5-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="80db5-193">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="80db5-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="80db5-194">Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di.</span><span class="sxs-lookup"><span data-stu-id="80db5-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="80db5-195">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="80db5-196">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="80db5-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="80db5-197">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="80db5-197">Settings for all app types</span></span>

<span data-ttu-id="80db5-198">V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP.</span><span class="sxs-lookup"><span data-stu-id="80db5-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="80db5-199">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="80db5-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="80db5-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="80db5-200">ApplicationName</span></span>

<span data-ttu-id="80db5-201">Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="80db5-202">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="80db5-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="80db5-203">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-203">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-204">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="80db5-205">**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="80db5-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="80db5-206">K nastavení této hodnoty použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="80db5-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="80db5-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="80db5-207">ContentRoot</span></span>

<span data-ttu-id="80db5-208">Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="80db5-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="80db5-209">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="80db5-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="80db5-210">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="80db5-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="80db5-211">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-211">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-212">**Výchozí**: složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="80db5-213">**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="80db5-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="80db5-214">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseContentRoot` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="80db5-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="80db5-215">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="80db5-215">For more information, see:</span></span>

* [<span data-ttu-id="80db5-216">Základy: kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="80db5-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="80db5-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="80db5-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="80db5-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="80db5-218">EnvironmentName</span></span>

<span data-ttu-id="80db5-219">Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="80db5-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="80db5-220">Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` .</span><span class="sxs-lookup"><span data-stu-id="80db5-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="80db5-221">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="80db5-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="80db5-222">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="80db5-222">**Key**: `environment`</span></span>  
<span data-ttu-id="80db5-223">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-223">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-224">**Výchozí**:`Production`</span><span class="sxs-lookup"><span data-stu-id="80db5-224">**Default**: `Production`</span></span>  
<span data-ttu-id="80db5-225">**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="80db5-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="80db5-226">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseEnvironment` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="80db5-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="80db5-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="80db5-227">ShutdownTimeout</span></span>

<span data-ttu-id="80db5-228">[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="80db5-229">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="80db5-229">The default value is five seconds.</span></span>  <span data-ttu-id="80db5-230">Po uplynutí časového limitu hostitel:</span><span class="sxs-lookup"><span data-stu-id="80db5-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="80db5-231">Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="80db5-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="80db5-232">Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="80db5-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="80db5-233">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="80db5-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="80db5-234">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="80db5-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="80db5-235">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="80db5-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="80db5-236">**Klíč**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="80db5-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="80db5-237">**Zadejte**:`int`</span><span class="sxs-lookup"><span data-stu-id="80db5-237">**Type**: `int`</span></span>  
<span data-ttu-id="80db5-238">**Výchozí hodnota**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="80db5-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="80db5-239">**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="80db5-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="80db5-240">K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="80db5-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="80db5-241">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="80db5-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="80db5-242">Nastavení pro Web Apps</span><span class="sxs-lookup"><span data-stu-id="80db5-242">Settings for web apps</span></span>

<span data-ttu-id="80db5-243">Některá nastavení hostitele se vztahují jenom na úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="80db5-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="80db5-244">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="80db5-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="80db5-245">`IWebHostBuilder`K dispozici jsou metody rozšíření pro tato nastavení.</span><span class="sxs-lookup"><span data-stu-id="80db5-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="80db5-246">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` , je instancí `IWebHostBuilder` , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="80db5-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="80db5-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="80db5-247">CaptureStartupErrors</span></span>

<span data-ttu-id="80db5-248">`false`V důsledku dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="80db5-249">Když `true` hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.</span><span class="sxs-lookup"><span data-stu-id="80db5-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="80db5-250">**Klíč**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="80db5-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="80db5-251">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="80db5-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="80db5-252">**Výchozí**: výchozí nastavení `false` , pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true` .</span><span class="sxs-lookup"><span data-stu-id="80db5-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="80db5-253">**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="80db5-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="80db5-254">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="80db5-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="80db5-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="80db5-255">DetailedErrors</span></span>

<span data-ttu-id="80db5-256">Pokud je povoleno nebo když je prostředí `Development` , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="80db5-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="80db5-257">**Klíč**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="80db5-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="80db5-258">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="80db5-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="80db5-259">**Výchozí**:`false`</span><span class="sxs-lookup"><span data-stu-id="80db5-259">**Default**: `false`</span></span>  
<span data-ttu-id="80db5-260">**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="80db5-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="80db5-261">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="80db5-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="80db5-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="80db5-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="80db5-263">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="80db5-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="80db5-264">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="80db5-265">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="80db5-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="80db5-266">**Klíč**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="80db5-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="80db5-267">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-267">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-268">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="80db5-268">**Default**: Empty string</span></span>  
<span data-ttu-id="80db5-269">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="80db5-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="80db5-270">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="80db5-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="80db5-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="80db5-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="80db5-272">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="80db5-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="80db5-273">**Klíč**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="80db5-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="80db5-274">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-274">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-275">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="80db5-275">**Default**: Empty string</span></span>  
<span data-ttu-id="80db5-276">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="80db5-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="80db5-277">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="80db5-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="80db5-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="80db5-278">HTTPS_Port</span></span>

<span data-ttu-id="80db5-279">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="80db5-279">The HTTPS redirect port.</span></span> <span data-ttu-id="80db5-280">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="80db5-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="80db5-281">**Klíč**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="80db5-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="80db5-282">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-282">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-283">**Výchozí**hodnota: výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="80db5-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="80db5-284">**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="80db5-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="80db5-285">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="80db5-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="80db5-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="80db5-286">PreferHostingUrls</span></span>

<span data-ttu-id="80db5-287">Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným pomocí a `IWebHostBuilder` místo adres URL nakonfigurovaných `IServer` implementací.</span><span class="sxs-lookup"><span data-stu-id="80db5-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="80db5-288">**Klíč**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="80db5-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="80db5-289">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="80db5-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="80db5-290">**Výchozí**:`true`</span><span class="sxs-lookup"><span data-stu-id="80db5-290">**Default**: `true`</span></span>  
<span data-ttu-id="80db5-291">**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="80db5-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="80db5-292">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="80db5-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="80db5-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="80db5-293">PreventHostingStartup</span></span>

<span data-ttu-id="80db5-294">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="80db5-295">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="80db5-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="80db5-296">**Klíč**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="80db5-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="80db5-297">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="80db5-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="80db5-298">**Výchozí**:`false`</span><span class="sxs-lookup"><span data-stu-id="80db5-298">**Default**: `false`</span></span>  
<span data-ttu-id="80db5-299">**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="80db5-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="80db5-300">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="80db5-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="80db5-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="80db5-301">StartupAssembly</span></span>

<span data-ttu-id="80db5-302">Sestavení, ve kterém se má hledat `Startup` Třída</span><span class="sxs-lookup"><span data-stu-id="80db5-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="80db5-303">**Klíč**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="80db5-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="80db5-304">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-304">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-305">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="80db5-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="80db5-306">**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="80db5-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="80db5-307">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="80db5-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="80db5-308">`UseStartup`může převzít název sestavení ( `string` ) nebo typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="80db5-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="80db5-309">Pokud `UseStartup` je voláno více metod, má poslední z nich přednost.</span><span class="sxs-lookup"><span data-stu-id="80db5-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="80db5-310">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="80db5-310">URLs</span></span>

<span data-ttu-id="80db5-311">Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="80db5-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="80db5-312">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="80db5-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="80db5-313">Pomocí příkazu " \* " určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="80db5-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="80db5-314">Protokol ( `http://` nebo `https://` ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="80db5-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="80db5-315">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="80db5-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="80db5-316">**Klíč**:`urls`</span><span class="sxs-lookup"><span data-stu-id="80db5-316">**Key**: `urls`</span></span>  
<span data-ttu-id="80db5-317">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-317">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-318">**Výchozí**: `http://localhost:5000` a`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="80db5-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="80db5-319">**Proměnná prostředí**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="80db5-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="80db5-320">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="80db5-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="80db5-321">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="80db5-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="80db5-322">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="80db5-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="80db5-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="80db5-323">WebRoot</span></span>

<span data-ttu-id="80db5-324">Vlastnost [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) Určuje relativní cestu ke statickým assetům aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="80db5-325">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="80db5-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="80db5-326">**Klíč**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="80db5-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="80db5-327">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-327">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-328">**Výchozí**: výchozí hodnota je `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="80db5-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="80db5-329">Cesta k *obsahu {root}/wwwroot* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="80db5-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="80db5-330">**Proměnná prostředí**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="80db5-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="80db5-331">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseWebRoot` na `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="80db5-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="80db5-332">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="80db5-332">For more information, see:</span></span>

* [<span data-ttu-id="80db5-333">Základy: web root</span><span class="sxs-lookup"><span data-stu-id="80db5-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="80db5-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="80db5-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="80db5-335">Správa životnosti hostitele</span><span class="sxs-lookup"><span data-stu-id="80db5-335">Manage the host lifetime</span></span>

<span data-ttu-id="80db5-336">Voláním metod v sestavené <xref:Microsoft.Extensions.Hosting.IHost> implementaci spustíte a zastavíte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="80db5-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="80db5-337">Tyto metody ovlivňují všechny <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="80db5-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="80db5-338">Spustit</span><span class="sxs-lookup"><span data-stu-id="80db5-338">Run</span></span>

<span data-ttu-id="80db5-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.</span><span class="sxs-lookup"><span data-stu-id="80db5-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="80db5-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-340">RunAsync</span></span>

<span data-ttu-id="80db5-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="80db5-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-342">RunConsoleAsync</span></span>

<span data-ttu-id="80db5-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="80db5-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="80db5-344">Spustit</span><span class="sxs-lookup"><span data-stu-id="80db5-344">Start</span></span>

<span data-ttu-id="80db5-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="80db5-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-346">StartAsync</span></span>

<span data-ttu-id="80db5-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a vrátí <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="80db5-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na začátku `StartAsync` , což čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="80db5-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="80db5-349">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="80db5-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="80db5-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-350">StopAsync</span></span>

<span data-ttu-id="80db5-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusy o zastavení hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="80db5-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="80db5-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="80db5-352">WaitForShutdown</span></span>

<span data-ttu-id="80db5-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například pomocí <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="80db5-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="80db5-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="80db5-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="80db5-356">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="80db5-356">External control</span></span>

<span data-ttu-id="80db5-357">Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="80db5-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="80db5-358">ASP.NET Core aplikace nakonfigurují a spouštějí hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="80db5-359">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="80db5-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="80db5-360">Tento článek se zabývá ASP.NET Core obecný hostitel ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ), který se používá pro aplikace, které nezpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="80db5-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="80db5-361">Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele, aby bylo možné rozšířit pole hostitelských scénářů.</span><span class="sxs-lookup"><span data-stu-id="80db5-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="80db5-362">Zasílání zpráv, úloh na pozadí a dalších úloh nevyužívajících HTTP na základě výhody obecného hostitele z různých možností, jako je konfigurace, vkládání závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="80db5-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="80db5-363">Obecný hostitel je v ASP.NET Core 2,1 novinkou a není vhodný pro scénáře hostování webů.</span><span class="sxs-lookup"><span data-stu-id="80db5-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="80db5-364">U scénářů pro hostování webů použijte [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="80db5-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="80db5-365">Obecný hostitel nahradí webového hostitele v budoucí verzi a bude fungovat jako primární rozhraní API v rámci scénářů HTTP i non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="80db5-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="80db5-366">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="80db5-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="80db5-367">Při spuštění ukázkové aplikace v [Visual Studio Code](https://code.visualstudio.com/)použijte *externí nebo integrovaný terminál*.</span><span class="sxs-lookup"><span data-stu-id="80db5-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="80db5-368">Nespouštějte ukázku v `internalConsole` .</span><span class="sxs-lookup"><span data-stu-id="80db5-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="80db5-369">Nastavení konzoly v Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="80db5-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="80db5-370">Otevřete soubor *. VSCode/launch.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="80db5-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="80db5-371">V konfiguraci **spuštění .NET Core (konzola)** vyhledejte položku **Konzola** .</span><span class="sxs-lookup"><span data-stu-id="80db5-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="80db5-372">Nastavte hodnotu buď `externalTerminal` nebo `integratedTerminal` .</span><span class="sxs-lookup"><span data-stu-id="80db5-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="80db5-373">Úvod</span><span class="sxs-lookup"><span data-stu-id="80db5-373">Introduction</span></span>

<span data-ttu-id="80db5-374">Knihovna obecných hostitelů je k dispozici v <xref:Microsoft.Extensions.Hosting> oboru názvů a poskytuje balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) .</span><span class="sxs-lookup"><span data-stu-id="80db5-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="80db5-375">`Microsoft.Extensions.Hosting`Balíček je součástí [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="80db5-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="80db5-376"><xref:Microsoft.Extensions.Hosting.IHostedService>je vstupním bodem pro provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="80db5-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="80db5-377">Každá <xref:Microsoft.Extensions.Hosting.IHostedService> implementace se spustí v pořadí [Registrace služby v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="80db5-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="80db5-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>je volána při <xref:Microsoft.Extensions.Hosting.IHostedService> spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> je volána v pořadí zpětné registrace při bezproblémovém ukončení hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="80db5-379">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="80db5-379">Set up a host</span></span>

<span data-ttu-id="80db5-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder>je hlavní součástí, kterou knihovny a aplikace používají k inicializaci, sestavování a spouštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="80db5-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="80db5-381">Možnosti</span><span class="sxs-lookup"><span data-stu-id="80db5-381">Options</span></span>

<span data-ttu-id="80db5-382"><xref:Microsoft.Extensions.Hosting.HostOptions>nakonfigurujte možnosti pro <xref:Microsoft.Extensions.Hosting.IHost> .</span><span class="sxs-lookup"><span data-stu-id="80db5-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="80db5-383">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="80db5-383">Shutdown timeout</span></span>

<span data-ttu-id="80db5-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="80db5-385">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="80db5-385">The default value is five seconds.</span></span>

<span data-ttu-id="80db5-386">Následující konfigurace možnosti v nástroji `Program.Main` zvyšuje výchozí časový limit pro vypnutí v pěti sekundách na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="80db5-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="80db5-387">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="80db5-387">Default services</span></span>

<span data-ttu-id="80db5-388">Během inicializace hostitele jsou registrovány následující služby:</span><span class="sxs-lookup"><span data-stu-id="80db5-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="80db5-389">[Prostředí](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="80db5-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="80db5-390">[Konfigurace](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="80db5-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="80db5-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="80db5-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="80db5-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="80db5-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="80db5-393">[Možnosti](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="80db5-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="80db5-394">[Protokolování](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="80db5-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="80db5-395">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="80db5-395">Host configuration</span></span>

<span data-ttu-id="80db5-396">Konfiguraci hostitele vytvořil:</span><span class="sxs-lookup"><span data-stu-id="80db5-396">Host configuration is created by:</span></span>

* <span data-ttu-id="80db5-397">Voláním metod rozšíření pro <xref:Microsoft.Extensions.Hosting.IHostBuilder> nastavte [kořen obsahu](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="80db5-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="80db5-398">Čtení konfigurace od zprostředkovatelů konfigurace v nástroji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="80db5-399">Metody rozšíření</span><span class="sxs-lookup"><span data-stu-id="80db5-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="80db5-400">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="80db5-400">Application key (name)</span></span>

<span data-ttu-id="80db5-401">Vlastnost [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="80db5-402">Chcete-li nastavit hodnotu explicitně, použijte [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="80db5-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="80db5-403">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="80db5-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="80db5-404">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-404">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-405">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="80db5-406">**Nastavit pomocí**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="80db5-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="80db5-407">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="80db5-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="80db5-408">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="80db5-408">Content root</span></span>

<span data-ttu-id="80db5-409">Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="80db5-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="80db5-410">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="80db5-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="80db5-411">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-411">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-412">**Výchozí**: výchozí nastavení složky, kde se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="80db5-413">**Nastavit pomocí**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="80db5-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="80db5-414">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="80db5-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="80db5-415">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="80db5-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="80db5-416">Další informace najdete v tématu [základy: kořen obsahu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="80db5-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="80db5-417">Prostředí</span><span class="sxs-lookup"><span data-stu-id="80db5-417">Environment</span></span>

<span data-ttu-id="80db5-418">Nastaví [prostředí](xref:fundamentals/environments)aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="80db5-419">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="80db5-419">**Key**: `environment`</span></span>  
<span data-ttu-id="80db5-420">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-420">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-421">**Výchozí**:`Production`</span><span class="sxs-lookup"><span data-stu-id="80db5-421">**Default**: `Production`</span></span>  
<span data-ttu-id="80db5-422">**Nastavit pomocí**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="80db5-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="80db5-423">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="80db5-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="80db5-424">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="80db5-424">The environment can be set to any value.</span></span> <span data-ttu-id="80db5-425">Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` .</span><span class="sxs-lookup"><span data-stu-id="80db5-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="80db5-426">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="80db5-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="80db5-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="80db5-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="80db5-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="80db5-429">Konfigurace hostitele slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="80db5-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="80db5-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="80db5-431">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="80db5-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="80db5-432">Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé.</span><span class="sxs-lookup"><span data-stu-id="80db5-432">No providers are included by default.</span></span> <span data-ttu-id="80db5-433">Musíte explicitně určit, v jakém poskytovateli konfigurace aplikace vyžaduje <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , včetně:</span><span class="sxs-lookup"><span data-stu-id="80db5-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="80db5-434">Konfigurace souboru (například z *hostsettings.js* souboru).</span><span class="sxs-lookup"><span data-stu-id="80db5-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="80db5-435">Konfigurace proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="80db5-435">Environment variable configuration.</span></span>
* <span data-ttu-id="80db5-436">Konfigurace argumentů příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="80db5-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="80db5-437">Všichni další povinní poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="80db5-437">Any other required configuration providers.</span></span>

<span data-ttu-id="80db5-438">Konfigurace souborů hostitele je povolená zadáním základní cesty aplikace `SetBasePath` a následným voláním jednoho z [poskytovatelů konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="80db5-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="80db5-439">Ukázková aplikace používá soubor JSON, *hostsettings.jszapnutý*, a volá <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> k využívání nastavení konfigurace hostitele souboru.</span><span class="sxs-lookup"><span data-stu-id="80db5-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="80db5-440">Chcete-li přidat [konfiguraci proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="80db5-441">`AddEnvironmentVariables`přijme volitelnou uživatelem definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="80db5-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="80db5-442">Ukázková aplikace používá předponu `PREFIX_` .</span><span class="sxs-lookup"><span data-stu-id="80db5-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="80db5-443">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="80db5-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="80db5-444">Když je hostitel ukázkové aplikace nakonfigurovaný, hodnota proměnné prostředí `PREFIX_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro tento `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="80db5-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="80db5-445">Během vývoje při používání sady [Visual Studio](https://visualstudio.microsoft.com) nebo při spuštění aplikace s se `dotnet run` mohou proměnné prostředí nastavit v souboru *Properties/launchSettings.js* .</span><span class="sxs-lookup"><span data-stu-id="80db5-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="80db5-446">V [Visual Studio Code](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *. VSCode/launch.js* při vývoji.</span><span class="sxs-lookup"><span data-stu-id="80db5-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="80db5-447">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="80db5-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="80db5-448">[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="80db5-449">K povolení argumentů příkazového řádku pro přepsání konfigurace poskytované předchozími poskytovateli konfigurace se přidá poslední konfigurace příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="80db5-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="80db5-450">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="80db5-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="80db5-451">Další konfiguraci lze poskytnout pomocí klíčů [ApplicationName](#application-key-name) a [contentRoot](#content-root) .</span><span class="sxs-lookup"><span data-stu-id="80db5-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="80db5-452">Příklad `HostBuilder` Konfigurace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="80db5-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="80db5-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="80db5-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="80db5-454">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementace.</span><span class="sxs-lookup"><span data-stu-id="80db5-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="80db5-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="80db5-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="80db5-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="80db5-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="80db5-457">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="80db5-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="80db5-458">Konfigurace vytvořená nástrojem <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="80db5-459">Konfigurace aplikace automaticky přijímá konfiguraci hostitele, kterou poskytuje [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="80db5-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="80db5-460">Příklad konfigurace aplikace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="80db5-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="80db5-461">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="80db5-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="80db5-462">*appsettings.Development.js*:</span><span class="sxs-lookup"><span data-stu-id="80db5-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="80db5-463">*appsettings.Production.js*:</span><span class="sxs-lookup"><span data-stu-id="80db5-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="80db5-464">Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="80db5-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="80db5-465">Ukázková aplikace přesune své soubory nastavení aplikace JSON a *hostsettings.js* s touto `<Content>` položkou:</span><span class="sxs-lookup"><span data-stu-id="80db5-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="80db5-466">Metody rozšíření konfigurace, například <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, například [Microsoft.Extensions.Configuration.Jsv](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft.Extensions.Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="80db5-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="80db5-467">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), tyto balíčky musí být kromě balíčku Core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) přidány do projektu.</span><span class="sxs-lookup"><span data-stu-id="80db5-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="80db5-468">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="80db5-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="80db5-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="80db5-469">ConfigureServices</span></span>

<span data-ttu-id="80db5-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>Přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="80db5-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="80db5-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="80db5-472">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="80db5-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="80db5-473">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="80db5-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="80db5-474">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá `AddHostedService` metodu rozšíření k přidání služby pro události života, `LifetimeEventsHostedService` a časově omezená úloha na pozadí, `TimedHostedService` do aplikace:</span><span class="sxs-lookup"><span data-stu-id="80db5-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="80db5-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="80db5-475">ConfigureLogging</span></span>

<span data-ttu-id="80db5-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>Přidá delegáta pro konfiguraci poskytnutého <xref:Microsoft.Extensions.Logging.ILoggingBuilder> .</span><span class="sxs-lookup"><span data-stu-id="80db5-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="80db5-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>může být voláno vícekrát s přísadou výsledků.</span><span class="sxs-lookup"><span data-stu-id="80db5-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="80db5-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="80db5-478">UseConsoleLifetime</span></span>

<span data-ttu-id="80db5-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="80db5-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="80db5-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="80db5-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je předem registrován jako výchozí implementace životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="80db5-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="80db5-482">Je použita poslední registrovaná doba života.</span><span class="sxs-lookup"><span data-stu-id="80db5-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="80db5-483">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="80db5-483">Container configuration</span></span>

<span data-ttu-id="80db5-484">Pro podporu zapojení do jiných kontejnerů může hostitel přijmout <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> .</span><span class="sxs-lookup"><span data-stu-id="80db5-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="80db5-485">Poskytnutí továrny není součástí registrace DI Container, ale je místo toho hostitel, který se používá k vytvoření konkrétního kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="80db5-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="80db5-486">[UseServiceProviderFactory (IServiceProviderFactory &lt; TContainerBuilder &gt; )](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí továrnu, která byla použita k vytvoření poskytovatele služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="80db5-487">Vlastní konfigurace kontejneru je spravovaná <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metodou.</span><span class="sxs-lookup"><span data-stu-id="80db5-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="80db5-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>poskytuje silné typové prostředí pro konfiguraci kontejneru nad podkladovým rozhraním API hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="80db5-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="80db5-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="80db5-490">Vytvoření kontejneru služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="80db5-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="80db5-491">Poskytněte objekt pro vytváření kontejnerů služby:</span><span class="sxs-lookup"><span data-stu-id="80db5-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="80db5-492">Použijte objekt pro vytváření a nakonfigurujte vlastní kontejner služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="80db5-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="80db5-493">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="80db5-493">Extensibility</span></span>

<span data-ttu-id="80db5-494">Rozšiřitelnost hostitelů se provádí s metodami rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="80db5-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="80db5-495">Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementaci s příkladem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) znázorněným v <xref:fundamentals/host/hosted-services> .</span><span class="sxs-lookup"><span data-stu-id="80db5-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="80db5-496">Aplikace vytváří `UseHostedService` metodu rozšíření pro registraci hostované služby, která byla předána `T` :</span><span class="sxs-lookup"><span data-stu-id="80db5-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="80db5-497">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="80db5-497">Manage the host</span></span>

<span data-ttu-id="80db5-498"><xref:Microsoft.Extensions.Hosting.IHost>Implementace zodpovídá za spouštění a zastavování <xref:Microsoft.Extensions.Hosting.IHostedService> implementací, které jsou zaregistrované v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="80db5-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="80db5-499">Spustit</span><span class="sxs-lookup"><span data-stu-id="80db5-499">Run</span></span>

<span data-ttu-id="80db5-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud není hostitel vypnutý:</span><span class="sxs-lookup"><span data-stu-id="80db5-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="80db5-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-501">RunAsync</span></span>

<span data-ttu-id="80db5-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí:</span><span class="sxs-lookup"><span data-stu-id="80db5-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="80db5-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-503">RunConsoleAsync</span></span>

<span data-ttu-id="80db5-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="80db5-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="80db5-505">Spustit a StopAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-505">Start and StopAsync</span></span>

<span data-ttu-id="80db5-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="80db5-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusy o zastavení hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="80db5-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="80db5-508">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="80db5-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="80db5-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="80db5-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>ukončí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="80db5-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="80db5-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="80db5-511">WaitForShutdown</span></span>

<span data-ttu-id="80db5-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>je aktivována prostřednictvím <xref:Microsoft.Extensions.Hosting.IHostLifetime> , například `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (naslouchá pro <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="80db5-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="80db5-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="80db5-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="80db5-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="80db5-516">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="80db5-516">External control</span></span>

<span data-ttu-id="80db5-517">Externí kontrolu hostitele lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="80db5-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="80db5-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> , což čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="80db5-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="80db5-519">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="80db5-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="80db5-520">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="80db5-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="80db5-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>poskytuje informace o hostitelském prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="80db5-522">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání, aby <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="80db5-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="80db5-523">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="80db5-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="80db5-524">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="80db5-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="80db5-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>umožňuje aktivity po spuštění a vypnutí, včetně řádných požadavků na vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="80db5-526">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="80db5-527">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="80db5-527">Cancellation Token</span></span> | <span data-ttu-id="80db5-528">Aktivované při&#8230;</span><span class="sxs-lookup"><span data-stu-id="80db5-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="80db5-529">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="80db5-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="80db5-530">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="80db5-531">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="80db5-531">All requests should be processed.</span></span> <span data-ttu-id="80db5-532">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="80db5-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="80db5-533">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="80db5-534">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="80db5-534">Requests may still be processing.</span></span> <span data-ttu-id="80db5-535">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="80db5-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="80db5-536">Konstruktor – vloží <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> službu do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="80db5-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="80db5-537">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání konstruktoru do `LifetimeEventsHostedService` třídy ( <xref:Microsoft.Extensions.Hosting.IHostedService> implementace) pro registraci událostí.</span><span class="sxs-lookup"><span data-stu-id="80db5-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="80db5-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="80db5-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="80db5-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>požaduje ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="80db5-540">Následující třída používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> k bezproblémovému vypnutí aplikace při `Shutdown` volání metody třídy:</span><span class="sxs-lookup"><span data-stu-id="80db5-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="80db5-541">Šablony ASP.NET Core vytvoří obecného hostitele .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="80db5-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="80db5-542">Definice hostitele</span><span class="sxs-lookup"><span data-stu-id="80db5-542">Host definition</span></span>

<span data-ttu-id="80db5-543">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="80db5-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="80db5-544">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="80db5-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="80db5-545">protokolování</span><span class="sxs-lookup"><span data-stu-id="80db5-545">Logging</span></span>
* <span data-ttu-id="80db5-546">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="80db5-546">Configuration</span></span>
* <span data-ttu-id="80db5-547">`IHostedService`implementaci</span><span class="sxs-lookup"><span data-stu-id="80db5-547">`IHostedService` implementations</span></span>

<span data-ttu-id="80db5-548">Při spuštění hostitele volá `IHostedService.StartAsync` každou implementaci <xref:Microsoft.Extensions.Hosting.IHostedService> , kterou najde v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="80db5-548">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="80db5-549">V rámci webové aplikace je jednou z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="80db5-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="80db5-550">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="80db5-551">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="80db5-551">Set up a host</span></span>

<span data-ttu-id="80db5-552">Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve `Program` třídě.</span><span class="sxs-lookup"><span data-stu-id="80db5-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="80db5-553">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="80db5-553">The `Main` method:</span></span>

* <span data-ttu-id="80db5-554">Volá `CreateHostBuilder` metodu pro vytvoření a konfiguraci objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="80db5-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="80db5-555">Volání `Build` a `Run` metody objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="80db5-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="80db5-556">Webové šablony ASP.NET Core generují následující kód pro vytvoření hostitele:</span><span class="sxs-lookup"><span data-stu-id="80db5-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="80db5-557">Následující kód vytvoří úlohu jiného typu než HTTP s `IHostedService` implementací přidanou do kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="80db5-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="80db5-558">Pro úlohy HTTP `Main` je metoda stejná, ale `CreateHostBuilder` volání `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="80db5-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="80db5-559">Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="80db5-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="80db5-560">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že budou nalezeny `CreateHostBuilder` metody, které nakonfigurují hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="80db5-561">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="80db5-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="80db5-562">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="80db5-562">Default builder settings</span></span>

<span data-ttu-id="80db5-563"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="80db5-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="80db5-564">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="80db5-565">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="80db5-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="80db5-566">Proměnné prostředí s předponou `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="80db5-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="80db5-567">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="80db5-567">Command-line arguments.</span></span>
* <span data-ttu-id="80db5-568">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="80db5-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="80db5-569">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="80db5-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="80db5-570">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="80db5-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="80db5-571">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="80db5-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="80db5-572">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="80db5-572">Environment variables.</span></span>
  * <span data-ttu-id="80db5-573">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="80db5-573">Command-line arguments.</span></span>
* <span data-ttu-id="80db5-574">Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="80db5-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="80db5-575">Konzola</span><span class="sxs-lookup"><span data-stu-id="80db5-575">Console</span></span>
  * <span data-ttu-id="80db5-576">Ladění</span><span class="sxs-lookup"><span data-stu-id="80db5-576">Debug</span></span>
  * <span data-ttu-id="80db5-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="80db5-577">EventSource</span></span>
  * <span data-ttu-id="80db5-578">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="80db5-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="80db5-579">Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.</span><span class="sxs-lookup"><span data-stu-id="80db5-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="80db5-580">`ConfigureWebHostDefaults`Metoda:</span><span class="sxs-lookup"><span data-stu-id="80db5-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="80db5-581">Načte konfiguraci hostitele z proměnných prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="80db5-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="80db5-582">Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="80db5-583">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="80db5-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="80db5-584">Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="80db5-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="80db5-585">Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , pokud je rovno `true` .</span><span class="sxs-lookup"><span data-stu-id="80db5-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="80db5-586">Povolí integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="80db5-586">Enables IIS integration.</span></span> <span data-ttu-id="80db5-587">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="80db5-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="80db5-588">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="80db5-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="80db5-589">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="80db5-589">Framework-provided services</span></span>

<span data-ttu-id="80db5-590">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="80db5-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="80db5-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="80db5-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="80db5-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="80db5-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="80db5-593">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="80db5-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="80db5-594">Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="80db5-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="80db5-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="80db5-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="80db5-596">Vložení <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> služby (dříve `IApplicationLifetime` ) do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="80db5-597">Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="80db5-598">Rozhraní obsahuje také `StopApplication` metodu.</span><span class="sxs-lookup"><span data-stu-id="80db5-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="80db5-599">Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:</span><span class="sxs-lookup"><span data-stu-id="80db5-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="80db5-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="80db5-600">IHostLifetime</span></span>

<span data-ttu-id="80db5-601"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementace určuje, kdy se hostitel spustí a kdy se zastaví.</span><span class="sxs-lookup"><span data-stu-id="80db5-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="80db5-602">Použije se Poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="80db5-602">The last implementation registered is used.</span></span>

<span data-ttu-id="80db5-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementací.</span><span class="sxs-lookup"><span data-stu-id="80db5-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="80db5-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="80db5-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="80db5-605">Naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="80db5-606">Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="80db5-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="80db5-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="80db5-607">IHostEnvironment</span></span>

<span data-ttu-id="80db5-608">Vložením <xref:Microsoft.Extensions.Hosting.IHostEnvironment> služby do třídy získáte informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="80db5-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="80db5-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="80db5-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="80db5-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="80db5-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="80db5-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="80db5-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="80db5-612">Webové aplikace implementují `IWebHostEnvironment` rozhraní, které dědí `IHostEnvironment` a přidává rozhraní [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="80db5-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="80db5-613">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="80db5-613">Host configuration</span></span>

<span data-ttu-id="80db5-614">Konfigurace hostitele se používá pro vlastnosti <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementace.</span><span class="sxs-lookup"><span data-stu-id="80db5-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="80db5-615">Konfigurace hostitele je k dispozici z [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="80db5-616">Po `ConfigureAppConfiguration` nahrazení se v `HostBuilderContext.Configuration` konfiguraci aplikace nahradí.</span><span class="sxs-lookup"><span data-stu-id="80db5-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="80db5-617">Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> na `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="80db5-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="80db5-618">`ConfigureHostConfiguration`dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="80db5-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="80db5-619">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="80db5-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="80db5-620">Zprostředkovatel proměnné prostředí s argumenty předpony `DOTNET_` a příkazového řádku jsou obsaženy v `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="80db5-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="80db5-621">U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="80db5-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="80db5-622">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="80db5-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="80db5-623">Například hodnota proměnné prostředí pro se nastaví jako `ASPNETCORE_ENVIRONMENT` hodnota konfigurace hostitele pro `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="80db5-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="80db5-624">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="80db5-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="80db5-625">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="80db5-625">App configuration</span></span>

<span data-ttu-id="80db5-626">Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> metody `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="80db5-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="80db5-627">`ConfigureAppConfiguration`dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="80db5-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="80db5-628">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="80db5-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="80db5-629">Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di.</span><span class="sxs-lookup"><span data-stu-id="80db5-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="80db5-630">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="80db5-631">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="80db5-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="80db5-632">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="80db5-632">Settings for all app types</span></span>

<span data-ttu-id="80db5-633">V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP.</span><span class="sxs-lookup"><span data-stu-id="80db5-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="80db5-634">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="80db5-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="80db5-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="80db5-635">ApplicationName</span></span>

<span data-ttu-id="80db5-636">Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="80db5-637">**Klíč**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="80db5-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="80db5-638">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-638">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-639">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="80db5-640">**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="80db5-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="80db5-641">K nastavení této hodnoty použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="80db5-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="80db5-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="80db5-642">ContentRoot</span></span>

<span data-ttu-id="80db5-643">Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="80db5-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="80db5-644">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="80db5-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="80db5-645">**Klíč**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="80db5-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="80db5-646">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-646">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-647">**Výchozí**: složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="80db5-648">**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="80db5-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="80db5-649">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseContentRoot` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="80db5-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="80db5-650">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="80db5-650">For more information, see:</span></span>

* [<span data-ttu-id="80db5-651">Základy: kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="80db5-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="80db5-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="80db5-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="80db5-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="80db5-653">EnvironmentName</span></span>

<span data-ttu-id="80db5-654">Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="80db5-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="80db5-655">Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` .</span><span class="sxs-lookup"><span data-stu-id="80db5-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="80db5-656">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="80db5-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="80db5-657">**Klíč**:`environment`</span><span class="sxs-lookup"><span data-stu-id="80db5-657">**Key**: `environment`</span></span>  
<span data-ttu-id="80db5-658">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-658">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-659">**Výchozí**:`Production`</span><span class="sxs-lookup"><span data-stu-id="80db5-659">**Default**: `Production`</span></span>  
<span data-ttu-id="80db5-660">**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="80db5-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="80db5-661">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseEnvironment` na `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="80db5-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="80db5-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="80db5-662">ShutdownTimeout</span></span>

<span data-ttu-id="80db5-663">[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="80db5-664">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="80db5-664">The default value is five seconds.</span></span>  <span data-ttu-id="80db5-665">Po uplynutí časového limitu hostitel:</span><span class="sxs-lookup"><span data-stu-id="80db5-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="80db5-666">Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="80db5-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="80db5-667">Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="80db5-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="80db5-668">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="80db5-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="80db5-669">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="80db5-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="80db5-670">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="80db5-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="80db5-671">**Klíč**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="80db5-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="80db5-672">**Zadejte**:`int`</span><span class="sxs-lookup"><span data-stu-id="80db5-672">**Type**: `int`</span></span>  
<span data-ttu-id="80db5-673">**Výchozí hodnota**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="80db5-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="80db5-674">**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="80db5-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="80db5-675">K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="80db5-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="80db5-676">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="80db5-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="80db5-677">Zakázat opětovné načtení konfigurace aplikace při změně</span><span class="sxs-lookup"><span data-stu-id="80db5-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="80db5-678">Ve [výchozím nastavení](xref:fundamentals/configuration/index#default) *appsettings.jsv* a *appSettings. { Prostředí}. JSON* se při změně souboru znovu načte.</span><span class="sxs-lookup"><span data-stu-id="80db5-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="80db5-679">Pokud chcete toto chování při opětovném načítání zakázat v ASP.NET Core 5,0 Preview 3 nebo novějším, nastavte `hostBuilder:reloadConfigOnChange` klíč na `false` .</span><span class="sxs-lookup"><span data-stu-id="80db5-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="80db5-680">**Klíč**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="80db5-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="80db5-681">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="80db5-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="80db5-682">**Výchozí**:`true`</span><span class="sxs-lookup"><span data-stu-id="80db5-682">**Default**: `true`</span></span>  
<span data-ttu-id="80db5-683">**Argument příkazového řádku**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="80db5-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="80db5-684">**Proměnná prostředí**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="80db5-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="80db5-685">Oddělovač dvojtečky ( `:` ) nefunguje s hierarchickými klíči proměnné prostředí na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="80db5-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="80db5-686">Další informace naleznete v tématu [proměnné prostředí](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="80db5-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="80db5-687">Nastavení pro Web Apps</span><span class="sxs-lookup"><span data-stu-id="80db5-687">Settings for web apps</span></span>

<span data-ttu-id="80db5-688">Některá nastavení hostitele se vztahují jenom na úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="80db5-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="80db5-689">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.</span><span class="sxs-lookup"><span data-stu-id="80db5-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="80db5-690">`IWebHostBuilder`K dispozici jsou metody rozšíření pro tato nastavení.</span><span class="sxs-lookup"><span data-stu-id="80db5-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="80db5-691">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` , je instancí `IWebHostBuilder` , jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="80db5-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="80db5-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="80db5-692">CaptureStartupErrors</span></span>

<span data-ttu-id="80db5-693">`false`V důsledku dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="80db5-694">Když `true` hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.</span><span class="sxs-lookup"><span data-stu-id="80db5-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="80db5-695">**Klíč**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="80db5-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="80db5-696">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="80db5-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="80db5-697">**Výchozí**: výchozí nastavení `false` , pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true` .</span><span class="sxs-lookup"><span data-stu-id="80db5-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="80db5-698">**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="80db5-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="80db5-699">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="80db5-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="80db5-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="80db5-700">DetailedErrors</span></span>

<span data-ttu-id="80db5-701">Pokud je povoleno nebo když je prostředí `Development` , aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="80db5-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="80db5-702">**Klíč**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="80db5-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="80db5-703">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="80db5-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="80db5-704">**Výchozí**:`false`</span><span class="sxs-lookup"><span data-stu-id="80db5-704">**Default**: `false`</span></span>  
<span data-ttu-id="80db5-705">**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="80db5-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="80db5-706">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="80db5-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="80db5-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="80db5-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="80db5-708">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="80db5-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="80db5-709">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="80db5-710">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="80db5-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="80db5-711">**Klíč**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="80db5-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="80db5-712">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-712">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-713">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="80db5-713">**Default**: Empty string</span></span>  
<span data-ttu-id="80db5-714">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="80db5-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="80db5-715">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="80db5-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="80db5-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="80db5-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="80db5-717">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="80db5-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="80db5-718">**Klíč**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="80db5-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="80db5-719">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-719">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-720">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="80db5-720">**Default**: Empty string</span></span>  
<span data-ttu-id="80db5-721">**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="80db5-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="80db5-722">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="80db5-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="80db5-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="80db5-723">HTTPS_Port</span></span>

<span data-ttu-id="80db5-724">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="80db5-724">The HTTPS redirect port.</span></span> <span data-ttu-id="80db5-725">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="80db5-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="80db5-726">**Klíč**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="80db5-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="80db5-727">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-727">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-728">**Výchozí**hodnota: výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="80db5-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="80db5-729">**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="80db5-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="80db5-730">Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="80db5-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="80db5-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="80db5-731">PreferHostingUrls</span></span>

<span data-ttu-id="80db5-732">Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným pomocí a `IWebHostBuilder` místo adres URL nakonfigurovaných `IServer` implementací.</span><span class="sxs-lookup"><span data-stu-id="80db5-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="80db5-733">**Klíč**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="80db5-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="80db5-734">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="80db5-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="80db5-735">**Výchozí**:`true`</span><span class="sxs-lookup"><span data-stu-id="80db5-735">**Default**: `true`</span></span>  
<span data-ttu-id="80db5-736">**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="80db5-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="80db5-737">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="80db5-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="80db5-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="80db5-738">PreventHostingStartup</span></span>

<span data-ttu-id="80db5-739">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="80db5-740">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="80db5-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="80db5-741">**Klíč**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="80db5-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="80db5-742">**Typ**: `bool` ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="80db5-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="80db5-743">**Výchozí**:`false`</span><span class="sxs-lookup"><span data-stu-id="80db5-743">**Default**: `false`</span></span>  
<span data-ttu-id="80db5-744">**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="80db5-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="80db5-745">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="80db5-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="80db5-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="80db5-746">StartupAssembly</span></span>

<span data-ttu-id="80db5-747">Sestavení, ve kterém se má hledat `Startup` Třída</span><span class="sxs-lookup"><span data-stu-id="80db5-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="80db5-748">**Klíč**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="80db5-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="80db5-749">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-749">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-750">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="80db5-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="80db5-751">**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="80db5-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="80db5-752">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="80db5-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="80db5-753">`UseStartup`může převzít název sestavení ( `string` ) nebo typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="80db5-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="80db5-754">Pokud `UseStartup` je voláno více metod, má poslední z nich přednost.</span><span class="sxs-lookup"><span data-stu-id="80db5-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="80db5-755">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="80db5-755">URLs</span></span>

<span data-ttu-id="80db5-756">Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="80db5-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="80db5-757">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="80db5-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="80db5-758">Pomocí příkazu " \* " určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="80db5-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="80db5-759">Protokol ( `http://` nebo `https://` ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="80db5-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="80db5-760">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="80db5-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="80db5-761">**Klíč**:`urls`</span><span class="sxs-lookup"><span data-stu-id="80db5-761">**Key**: `urls`</span></span>  
<span data-ttu-id="80db5-762">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-762">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-763">**Výchozí**: `http://localhost:5000` a`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="80db5-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="80db5-764">**Proměnná prostředí**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="80db5-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="80db5-765">K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="80db5-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="80db5-766">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="80db5-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="80db5-767">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="80db5-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="80db5-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="80db5-768">WebRoot</span></span>

<span data-ttu-id="80db5-769">Vlastnost [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) Určuje relativní cestu ke statickým assetům aplikace.</span><span class="sxs-lookup"><span data-stu-id="80db5-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="80db5-770">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="80db5-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="80db5-771">**Klíč**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="80db5-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="80db5-772">**Zadejte**:`string`</span><span class="sxs-lookup"><span data-stu-id="80db5-772">**Type**: `string`</span></span>  
<span data-ttu-id="80db5-773">**Výchozí**: výchozí hodnota je `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="80db5-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="80db5-774">Cesta k *obsahu {root}/wwwroot* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="80db5-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="80db5-775">**Proměnná prostředí**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="80db5-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="80db5-776">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseWebRoot` na `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="80db5-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="80db5-777">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="80db5-777">For more information, see:</span></span>

* [<span data-ttu-id="80db5-778">Základy: web root</span><span class="sxs-lookup"><span data-stu-id="80db5-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="80db5-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="80db5-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="80db5-780">Správa životnosti hostitele</span><span class="sxs-lookup"><span data-stu-id="80db5-780">Manage the host lifetime</span></span>

<span data-ttu-id="80db5-781">Voláním metod v sestavené <xref:Microsoft.Extensions.Hosting.IHost> implementaci spustíte a zastavíte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="80db5-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="80db5-782">Tyto metody ovlivňují všechny <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="80db5-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="80db5-783">Spustit</span><span class="sxs-lookup"><span data-stu-id="80db5-783">Run</span></span>

<span data-ttu-id="80db5-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.</span><span class="sxs-lookup"><span data-stu-id="80db5-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="80db5-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-785">RunAsync</span></span>

<span data-ttu-id="80db5-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="80db5-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-787">RunConsoleAsync</span></span>

<span data-ttu-id="80db5-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="80db5-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="80db5-789">Spustit</span><span class="sxs-lookup"><span data-stu-id="80db5-789">Start</span></span>

<span data-ttu-id="80db5-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí synchronního hostitele.</span><span class="sxs-lookup"><span data-stu-id="80db5-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="80db5-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-791">StartAsync</span></span>

<span data-ttu-id="80db5-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a vrátí <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="80db5-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="80db5-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na začátku `StartAsync` , což čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="80db5-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="80db5-794">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="80db5-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="80db5-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-795">StopAsync</span></span>

<span data-ttu-id="80db5-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusy o zastavení hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="80db5-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="80db5-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="80db5-797">WaitForShutdown</span></span>

<span data-ttu-id="80db5-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například pomocí <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="80db5-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="80db5-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="80db5-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="80db5-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="80db5-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="80db5-801">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="80db5-801">External control</span></span>

<span data-ttu-id="80db5-802">Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="80db5-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="80db5-803">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="80db5-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
