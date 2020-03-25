---
title: Obecný hostitel .NET
author: rick-anderson
description: Seznamte se s obecným hostitelem .NET Core, který je zodpovědný za spouštění a správu životního cyklu aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 0f8f03dabf65f2cbfe4c41d36b02a25d7902cefb
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219217"
---
# <a name="net-generic-host"></a><span data-ttu-id="72e2b-103">Obecný hostitel .NET</span><span class="sxs-lookup"><span data-stu-id="72e2b-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="72e2b-104">Tento článek představuje obecného hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny k jeho použití.</span><span class="sxs-lookup"><span data-stu-id="72e2b-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="72e2b-105">Co je hostitel?</span><span class="sxs-lookup"><span data-stu-id="72e2b-105">What's a host?</span></span>

<span data-ttu-id="72e2b-106">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="72e2b-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="72e2b-107">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="72e2b-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="72e2b-108">Protokolování</span><span class="sxs-lookup"><span data-stu-id="72e2b-108">Logging</span></span>
* <span data-ttu-id="72e2b-109">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="72e2b-109">Configuration</span></span>
* <span data-ttu-id="72e2b-110">`IHostedService` implementace</span><span class="sxs-lookup"><span data-stu-id="72e2b-110">`IHostedService` implementations</span></span>

<span data-ttu-id="72e2b-111">Při spuštění hostitele volá `IHostedService.StartAsync` na každé implementaci <xref:Microsoft.Extensions.Hosting.IHostedService>, kterou nalezne v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="72e2b-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="72e2b-112">V rámci webové aplikace je jedním z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="72e2b-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="72e2b-113">Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="72e2b-114">Ve verzích ASP.NET Core starších než 3,0 se [webový hostitel](xref:fundamentals/host/web-host) používá pro úlohy http.</span><span class="sxs-lookup"><span data-stu-id="72e2b-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="72e2b-115">Webový hostitel již není doporučen pro webové aplikace a zůstává dostupný pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="72e2b-116">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="72e2b-116">Set up a host</span></span>

<span data-ttu-id="72e2b-117">Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve třídě `Program`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="72e2b-118">Metoda `Main`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-118">The `Main` method:</span></span>

* <span data-ttu-id="72e2b-119">Volá metodu `CreateHostBuilder` pro vytvoření a konfiguraci objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="72e2b-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="72e2b-120">Volá `Build` a `Run` metody objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="72e2b-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="72e2b-121">Zde je *program.cs* kód pro úlohu jiného typu než HTTP a jedna implementace `IHostedService` přidána do kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="72e2b-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="72e2b-122">Pro úlohy HTTP je metoda `Main` stejná, ale `CreateHostBuilder` volá `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="72e2b-123">Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="72e2b-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="72e2b-124">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že se najde `CreateHostBuilder` metoda, která nakonfiguruje hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="72e2b-125">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="72e2b-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="72e2b-126">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="72e2b-126">Default builder settings</span></span>

<span data-ttu-id="72e2b-127">Metoda <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="72e2b-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="72e2b-128">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="72e2b-129">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="72e2b-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="72e2b-130">Proměnné prostředí s předponou `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-130">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="72e2b-131">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="72e2b-131">Command-line arguments.</span></span>
* <span data-ttu-id="72e2b-132">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="72e2b-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="72e2b-133">*appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72e2b-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="72e2b-134">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72e2b-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="72e2b-135">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="72e2b-136">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-136">Environment variables.</span></span>
  * <span data-ttu-id="72e2b-137">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="72e2b-137">Command-line arguments.</span></span>
* <span data-ttu-id="72e2b-138">Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="72e2b-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="72e2b-139">Konzola</span><span class="sxs-lookup"><span data-stu-id="72e2b-139">Console</span></span>
  * <span data-ttu-id="72e2b-140">Ladění</span><span class="sxs-lookup"><span data-stu-id="72e2b-140">Debug</span></span>
  * <span data-ttu-id="72e2b-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="72e2b-141">EventSource</span></span>
  * <span data-ttu-id="72e2b-142">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="72e2b-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="72e2b-143">Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="72e2b-144">Metoda `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="72e2b-145">Načte konfiguraci hostitele z proměnných prostředí s předponou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-145">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="72e2b-146">Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="72e2b-147">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="72e2b-148">Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="72e2b-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="72e2b-149">Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` rovná `true`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="72e2b-150">Povolí integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="72e2b-150">Enables IIS integration.</span></span> <span data-ttu-id="72e2b-151">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="72e2b-152">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="72e2b-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="72e2b-153">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="72e2b-153">Framework-provided services</span></span>

<span data-ttu-id="72e2b-154">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="72e2b-154">The following services are registered automatically:</span></span>

* [<span data-ttu-id="72e2b-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="72e2b-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="72e2b-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="72e2b-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="72e2b-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="72e2b-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="72e2b-158">Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="72e2b-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="72e2b-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="72e2b-160">Vložení služby <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (dřív `IApplicationLifetime`) do libovolné třídy pro zpracování úloh po spuštění a řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="72e2b-161">Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="72e2b-162">Rozhraní obsahuje také metodu `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="72e2b-163">Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:</span><span class="sxs-lookup"><span data-stu-id="72e2b-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="72e2b-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="72e2b-164">IHostLifetime</span></span>

<span data-ttu-id="72e2b-165">Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> určuje, kdy se hostitel spustí a kdy se zastaví.</span><span class="sxs-lookup"><span data-stu-id="72e2b-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="72e2b-166">Použije se Poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-166">The last implementation registered is used.</span></span>

<span data-ttu-id="72e2b-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je výchozí `IHostLifetime` implementace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="72e2b-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="72e2b-169">Naslouchá pro <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM a volá <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-169">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="72e2b-170">Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="72e2b-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="72e2b-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="72e2b-171">IHostEnvironment</span></span>

<span data-ttu-id="72e2b-172">Vložením služby <xref:Microsoft.Extensions.Hosting.IHostEnvironment> do třídy získáte informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="72e2b-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="72e2b-173">NázevAplikace</span><span class="sxs-lookup"><span data-stu-id="72e2b-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="72e2b-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="72e2b-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="72e2b-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="72e2b-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="72e2b-176">Webové aplikace implementují rozhraní `IWebHostEnvironment`, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="72e2b-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="72e2b-177">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="72e2b-177">Host configuration</span></span>

<span data-ttu-id="72e2b-178">Konfigurace hostitele se používá pro vlastnosti implementace <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="72e2b-179">Konfigurace hostitele je k dispozici z [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) v rámci <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="72e2b-180">Po `ConfigureAppConfiguration`se `HostBuilderContext.Configuration` nahradí konfigurací aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="72e2b-181">Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> v `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="72e2b-182">`ConfigureHostConfiguration` lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="72e2b-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="72e2b-183">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="72e2b-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="72e2b-184">`CreateDefaultBuilder`obsahuje poskytovatele proměnné prostředí s předponou `DOTNET_` a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="72e2b-184">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72e2b-185">U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="72e2b-186">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="72e2b-187">Například hodnota proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="72e2b-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="72e2b-188">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="72e2b-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="72e2b-189">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="72e2b-189">App configuration</span></span>

<span data-ttu-id="72e2b-190">Konfigurace aplikace se vytvoří voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="72e2b-191">`ConfigureAppConfiguration` lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="72e2b-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="72e2b-192">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="72e2b-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="72e2b-193">Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di.</span><span class="sxs-lookup"><span data-stu-id="72e2b-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="72e2b-194">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="72e2b-195">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="72e2b-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="72e2b-196">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="72e2b-196">Settings for all app types</span></span>

<span data-ttu-id="72e2b-197">V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP.</span><span class="sxs-lookup"><span data-stu-id="72e2b-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="72e2b-198">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="72e2b-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="72e2b-199">ApplicationName</span></span>

<span data-ttu-id="72e2b-200">Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="72e2b-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="72e2b-201">**Klíč**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="72e2b-201">**Key**: `applicationName`</span></span>  
<span data-ttu-id="72e2b-202">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-202">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-203">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="72e2b-204">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="72e2b-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="72e2b-205">K nastavení této hodnoty použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="72e2b-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="72e2b-206">ContentRootPath</span></span>

<span data-ttu-id="72e2b-207">Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="72e2b-208">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="72e2b-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="72e2b-209">**Klíč**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="72e2b-209">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="72e2b-210">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-210">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-211">**Výchozí**: složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="72e2b-212">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="72e2b-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="72e2b-213">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseContentRoot` volání v `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="72e2b-214">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="72e2b-214">For more information, see:</span></span>

* [<span data-ttu-id="72e2b-215">Základy: kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="72e2b-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="72e2b-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="72e2b-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="72e2b-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="72e2b-217">EnvironmentName</span></span>

<span data-ttu-id="72e2b-218">Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="72e2b-219">Mezi hodnoty definované rozhraní patří `Development`, `Staging`a `Production`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="72e2b-220">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="72e2b-220">Values aren't case-sensitive.</span></span>

<span data-ttu-id="72e2b-221">**Klíč**: `environment`</span><span class="sxs-lookup"><span data-stu-id="72e2b-221">**Key**: `environment`</span></span>  
<span data-ttu-id="72e2b-222">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-222">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-223">**Výchozí**: `Production`</span><span class="sxs-lookup"><span data-stu-id="72e2b-223">**Default**: `Production`</span></span>  
<span data-ttu-id="72e2b-224">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="72e2b-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="72e2b-225">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseEnvironment` volání v `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="72e2b-226">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="72e2b-226">ShutdownTimeout</span></span>

<span data-ttu-id="72e2b-227">[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="72e2b-228">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="72e2b-228">The default value is five seconds.</span></span>  <span data-ttu-id="72e2b-229">Po uplynutí časového limitu hostitel:</span><span class="sxs-lookup"><span data-stu-id="72e2b-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="72e2b-230">Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="72e2b-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="72e2b-231">Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="72e2b-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="72e2b-232">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="72e2b-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="72e2b-233">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="72e2b-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="72e2b-234">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="72e2b-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="72e2b-235">**Klíč**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="72e2b-235">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="72e2b-236">**Zadejte**: `int`</span><span class="sxs-lookup"><span data-stu-id="72e2b-236">**Type**: `int`</span></span>  
<span data-ttu-id="72e2b-237">**Výchozí hodnota**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="72e2b-237">**Default**: 5 seconds</span></span>  
<span data-ttu-id="72e2b-238">**Proměnná prostředí**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="72e2b-238">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="72e2b-239">K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="72e2b-240">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="72e2b-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="72e2b-241">Zakázat opětovné načtení konfigurace aplikace při změně</span><span class="sxs-lookup"><span data-stu-id="72e2b-241">Disable app configuration reload on change</span></span>

<span data-ttu-id="72e2b-242">Ve [výchozím nastavení](xref:fundamentals/configuration/index#default), *appSettings. JSON* a *appSettings. { Prostředí}. JSON* se při změně souboru znovu načte.</span><span class="sxs-lookup"><span data-stu-id="72e2b-242">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="72e2b-243">Pokud chcete toto chování při opakovaném nastavování zakázat v ASP.NET Core 5,0 Preview 3 nebo novějším, nastavte klíč `hostBuilder:reloadConfigOnChange` na `false`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-243">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="72e2b-244">**Klíč**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="72e2b-244">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="72e2b-245">**Typ**: `bool` (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="72e2b-245">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="72e2b-246">**Výchozí**: `true`</span><span class="sxs-lookup"><span data-stu-id="72e2b-246">**Default**: `true`</span></span>  
<span data-ttu-id="72e2b-247">**Argument příkazového řádku**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="72e2b-247">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="72e2b-248">**Proměnná prostředí**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="72e2b-248">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="72e2b-249">Oddělovač dvojtečky (`:`) nefunguje s hierarchickými klíči proměnné prostředí na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="72e2b-249">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="72e2b-250">Další informace naleznete v tématu [proměnné prostředí](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="72e2b-250">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="72e2b-251">Nastavení pro Web Apps</span><span class="sxs-lookup"><span data-stu-id="72e2b-251">Settings for web apps</span></span>

<span data-ttu-id="72e2b-252">Některá nastavení hostitele se vztahují jenom na úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="72e2b-252">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="72e2b-253">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-253">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="72e2b-254">Pro tato nastavení jsou k dispozici metody rozšíření na `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-254">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="72e2b-255">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` je instance `IWebHostBuilder`, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="72e2b-255">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="72e2b-256">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="72e2b-256">CaptureStartupErrors</span></span>

<span data-ttu-id="72e2b-257">Při `false`dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="72e2b-257">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="72e2b-258">Při `true`zachytí hostitel při spuštění výjimky a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="72e2b-258">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="72e2b-259">**Klíč**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="72e2b-259">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="72e2b-260">**Typ**: `bool` (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="72e2b-260">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="72e2b-261">**Výchozí**: výchozí nastavení `false`, pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-261">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="72e2b-262">**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="72e2b-262">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="72e2b-263">Chcete-li nastavit tuto hodnotu, použijte `CaptureStartupErrors`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-263">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="72e2b-264">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="72e2b-264">DetailedErrors</span></span>

<span data-ttu-id="72e2b-265">Pokud je povoleno nebo když je prostředí `Development`, aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="72e2b-265">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="72e2b-266">**Klíč**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="72e2b-266">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="72e2b-267">**Typ**: `bool` (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="72e2b-267">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="72e2b-268">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="72e2b-268">**Default**: `false`</span></span>  
<span data-ttu-id="72e2b-269">**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="72e2b-269">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="72e2b-270">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="72e2b-271">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="72e2b-271">HostingStartupAssemblies</span></span>

<span data-ttu-id="72e2b-272">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="72e2b-272">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="72e2b-273">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-273">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="72e2b-274">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="72e2b-274">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="72e2b-275">**Klíč**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="72e2b-275">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="72e2b-276">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-276">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-277">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="72e2b-277">**Default**: Empty string</span></span>  
<span data-ttu-id="72e2b-278">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="72e2b-278">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="72e2b-279">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="72e2b-280">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="72e2b-280">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="72e2b-281">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="72e2b-281">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="72e2b-282">**Klíč**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="72e2b-282">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="72e2b-283">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-283">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-284">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="72e2b-284">**Default**: Empty string</span></span>  
<span data-ttu-id="72e2b-285">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="72e2b-285">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="72e2b-286">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-286">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="72e2b-287">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="72e2b-287">HTTPS_Port</span></span>

<span data-ttu-id="72e2b-288">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="72e2b-288">The HTTPS redirect port.</span></span> <span data-ttu-id="72e2b-289">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="72e2b-289">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="72e2b-290">**Klíč**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="72e2b-290">**Key**: `https_port`</span></span>  
<span data-ttu-id="72e2b-291">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-291">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-292">**Výchozí**hodnota: výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="72e2b-292">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="72e2b-293">**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="72e2b-293">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="72e2b-294">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-294">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="72e2b-295">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="72e2b-295">PreferHostingUrls</span></span>

<span data-ttu-id="72e2b-296">Určuje, jestli by měl hostitel naslouchat adresám URL nakonfigurovaným pomocí `IWebHostBuilder` místo adres URL nakonfigurovaných pomocí implementace `IServer`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-296">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="72e2b-297">**Klíč**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="72e2b-297">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="72e2b-298">**Typ**: `bool` (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="72e2b-298">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="72e2b-299">**Výchozí**: `true`</span><span class="sxs-lookup"><span data-stu-id="72e2b-299">**Default**: `true`</span></span>  
<span data-ttu-id="72e2b-300">**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="72e2b-300">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="72e2b-301">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `PreferHostingUrls`volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-301">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="72e2b-302">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="72e2b-302">PreventHostingStartup</span></span>

<span data-ttu-id="72e2b-303">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-303">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="72e2b-304">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-304">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="72e2b-305">**Klíč**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="72e2b-305">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="72e2b-306">**Typ**: `bool` (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="72e2b-306">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="72e2b-307">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="72e2b-307">**Default**: `false`</span></span>  
<span data-ttu-id="72e2b-308">**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="72e2b-308">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="72e2b-309">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseSetting` volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-309">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="72e2b-310">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="72e2b-310">StartupAssembly</span></span>

<span data-ttu-id="72e2b-311">Sestavení, ve kterém se má hledat `Startup` třídy</span><span class="sxs-lookup"><span data-stu-id="72e2b-311">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="72e2b-312">**Klíč**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="72e2b-312">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="72e2b-313">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-313">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-314">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="72e2b-314">**Default**: The app's assembly</span></span>  
<span data-ttu-id="72e2b-315">**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="72e2b-315">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="72e2b-316">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-316">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="72e2b-317">`UseStartup` může převzít název sestavení (`string`) nebo typ (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="72e2b-317">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="72e2b-318">Je-li voláno více metod `UseStartup`, má poslední název přednost.</span><span class="sxs-lookup"><span data-stu-id="72e2b-318">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="72e2b-319">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="72e2b-319">URLs</span></span>

<span data-ttu-id="72e2b-320">Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="72e2b-320">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="72e2b-321">například `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-321">For example, `http://localhost:123`.</span></span> <span data-ttu-id="72e2b-322">Pomocí příkazu "\*" označíte, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="72e2b-322">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="72e2b-323">Protokol (`http://` nebo `https://`) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="72e2b-323">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="72e2b-324">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="72e2b-324">Supported formats vary among servers.</span></span>

<span data-ttu-id="72e2b-325">**Klíč**: `urls`</span><span class="sxs-lookup"><span data-stu-id="72e2b-325">**Key**: `urls`</span></span>  
<span data-ttu-id="72e2b-326">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-326">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-327">**Výchozí**: `http://localhost:5000` a `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="72e2b-327">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="72e2b-328">**Proměnná prostředí**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="72e2b-328">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="72e2b-329">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseUrls`volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-329">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="72e2b-330">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-330">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="72e2b-331">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-331">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="72e2b-332">WebRoot</span><span class="sxs-lookup"><span data-stu-id="72e2b-332">WebRoot</span></span>

<span data-ttu-id="72e2b-333">Relativní cesta ke statickým prostředkům aplikace</span><span class="sxs-lookup"><span data-stu-id="72e2b-333">The relative path to the app's static assets.</span></span>

<span data-ttu-id="72e2b-334">**Klíč**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="72e2b-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="72e2b-335">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-335">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-336">**Výchozí**: výchozí hodnota je `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="72e2b-337">Cesta k *obsahu {root}/wwwroot* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="72e2b-337">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="72e2b-338">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="72e2b-338">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="72e2b-339">**Proměnná prostředí**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="72e2b-339">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="72e2b-340">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseWebRoot`volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-340">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="72e2b-341">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="72e2b-341">For more information, see:</span></span>

* [<span data-ttu-id="72e2b-342">Základy: web root</span><span class="sxs-lookup"><span data-stu-id="72e2b-342">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="72e2b-343">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="72e2b-343">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="72e2b-344">Správa životnosti hostitele</span><span class="sxs-lookup"><span data-stu-id="72e2b-344">Manage the host lifetime</span></span>

<span data-ttu-id="72e2b-345">Volejte metody pro sestavenou <xref:Microsoft.Extensions.Hosting.IHost> implementaci pro spuštění a zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-345">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="72e2b-346">Tyto metody ovlivňují všechny <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="72e2b-346">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="72e2b-347">Spusťte</span><span class="sxs-lookup"><span data-stu-id="72e2b-347">Run</span></span>

<span data-ttu-id="72e2b-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.</span><span class="sxs-lookup"><span data-stu-id="72e2b-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="72e2b-349">RunAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-349">RunAsync</span></span>

<span data-ttu-id="72e2b-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task>, která se dokončí při aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="72e2b-351">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-351">RunConsoleAsync</span></span>

<span data-ttu-id="72e2b-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestavování a spouštění hostitele a čeká na vypnutí <kbd>klávesové zkratky Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="72e2b-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="72e2b-353">Start</span><span class="sxs-lookup"><span data-stu-id="72e2b-353">Start</span></span>

<span data-ttu-id="72e2b-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spouští hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="72e2b-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="72e2b-355">StartAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-355">StartAsync</span></span>

<span data-ttu-id="72e2b-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí hostitele a vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="72e2b-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se volá na začátku `StartAsync`, která čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="72e2b-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="72e2b-358">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-358">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="72e2b-359">StopAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-359">StopAsync</span></span>

<span data-ttu-id="72e2b-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> se pokusí zastavit hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="72e2b-361">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="72e2b-361">WaitForShutdown</span></span>

<span data-ttu-id="72e2b-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například pomocí <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="72e2b-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="72e2b-363">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-363">WaitForShutdownAsync</span></span>

<span data-ttu-id="72e2b-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje prostřednictvím daného tokenu a volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="72e2b-365">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="72e2b-365">External control</span></span>

<span data-ttu-id="72e2b-366">Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="72e2b-366">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="72e2b-367">Tento článek představuje obecného hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny k jeho použití.</span><span class="sxs-lookup"><span data-stu-id="72e2b-367">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="72e2b-368">Co je hostitel?</span><span class="sxs-lookup"><span data-stu-id="72e2b-368">What's a host?</span></span>

<span data-ttu-id="72e2b-369">*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="72e2b-369">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="72e2b-370">Vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="72e2b-370">Dependency injection (DI)</span></span>
* <span data-ttu-id="72e2b-371">Protokolování</span><span class="sxs-lookup"><span data-stu-id="72e2b-371">Logging</span></span>
* <span data-ttu-id="72e2b-372">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="72e2b-372">Configuration</span></span>
* <span data-ttu-id="72e2b-373">`IHostedService` implementace</span><span class="sxs-lookup"><span data-stu-id="72e2b-373">`IHostedService` implementations</span></span>

<span data-ttu-id="72e2b-374">Při spuštění hostitele volá `IHostedService.StartAsync` na každé implementaci <xref:Microsoft.Extensions.Hosting.IHostedService>, kterou nalezne v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="72e2b-374">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="72e2b-375">V rámci webové aplikace je jedním z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="72e2b-375">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="72e2b-376">Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-376">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="72e2b-377">Ve verzích ASP.NET Core starších než 3,0 se [webový hostitel](xref:fundamentals/host/web-host) používá pro úlohy http.</span><span class="sxs-lookup"><span data-stu-id="72e2b-377">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="72e2b-378">Webový hostitel již není doporučen pro webové aplikace a zůstává dostupný pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-378">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="72e2b-379">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="72e2b-379">Set up a host</span></span>

<span data-ttu-id="72e2b-380">Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve třídě `Program`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-380">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="72e2b-381">Metoda `Main`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-381">The `Main` method:</span></span>

* <span data-ttu-id="72e2b-382">Volá metodu `CreateHostBuilder` pro vytvoření a konfiguraci objektu Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="72e2b-382">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="72e2b-383">Volá `Build` a `Run` metody objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="72e2b-383">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="72e2b-384">Zde je *program.cs* kód pro úlohu jiného typu než HTTP a jedna implementace `IHostedService` přidána do kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="72e2b-384">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="72e2b-385">Pro úlohy HTTP je metoda `Main` stejná, ale `CreateHostBuilder` volá `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-385">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="72e2b-386">Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="72e2b-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="72e2b-387">[Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že se najde `CreateHostBuilder` metoda, která nakonfiguruje hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="72e2b-388">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="72e2b-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="72e2b-389">Výchozí nastavení tvůrce</span><span class="sxs-lookup"><span data-stu-id="72e2b-389">Default builder settings</span></span>

<span data-ttu-id="72e2b-390">Metoda <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="72e2b-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="72e2b-391">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="72e2b-392">Načte konfiguraci hostitele z:</span><span class="sxs-lookup"><span data-stu-id="72e2b-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="72e2b-393">Proměnné prostředí s předponou `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="72e2b-394">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="72e2b-394">Command-line arguments.</span></span>
* <span data-ttu-id="72e2b-395">Načte konfiguraci aplikace z:</span><span class="sxs-lookup"><span data-stu-id="72e2b-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="72e2b-396">*appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72e2b-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="72e2b-397">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72e2b-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="72e2b-398">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="72e2b-399">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-399">Environment variables.</span></span>
  * <span data-ttu-id="72e2b-400">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="72e2b-400">Command-line arguments.</span></span>
* <span data-ttu-id="72e2b-401">Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="72e2b-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="72e2b-402">Konzola</span><span class="sxs-lookup"><span data-stu-id="72e2b-402">Console</span></span>
  * <span data-ttu-id="72e2b-403">Ladění</span><span class="sxs-lookup"><span data-stu-id="72e2b-403">Debug</span></span>
  * <span data-ttu-id="72e2b-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="72e2b-404">EventSource</span></span>
  * <span data-ttu-id="72e2b-405">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="72e2b-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="72e2b-406">Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="72e2b-407">Metoda `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="72e2b-408">Načte konfiguraci hostitele z proměnných prostředí s předponou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="72e2b-409">Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="72e2b-410">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="72e2b-411">Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="72e2b-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="72e2b-412">Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` rovná `true`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="72e2b-413">Povolí integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="72e2b-413">Enables IIS integration.</span></span> <span data-ttu-id="72e2b-414">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="72e2b-415">[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.</span><span class="sxs-lookup"><span data-stu-id="72e2b-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="72e2b-416">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="72e2b-416">Framework-provided services</span></span>

<span data-ttu-id="72e2b-417">Následující služby jsou registrovány automaticky:</span><span class="sxs-lookup"><span data-stu-id="72e2b-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="72e2b-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="72e2b-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="72e2b-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="72e2b-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="72e2b-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="72e2b-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="72e2b-421">Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="72e2b-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="72e2b-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="72e2b-423">Vložení služby <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (dřív `IApplicationLifetime`) do libovolné třídy pro zpracování úloh po spuštění a řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="72e2b-424">Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="72e2b-425">Rozhraní obsahuje také metodu `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="72e2b-426">Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:</span><span class="sxs-lookup"><span data-stu-id="72e2b-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="72e2b-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="72e2b-427">IHostLifetime</span></span>

<span data-ttu-id="72e2b-428">Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> určuje, kdy se hostitel spustí a kdy se zastaví.</span><span class="sxs-lookup"><span data-stu-id="72e2b-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="72e2b-429">Použije se Poslední registrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-429">The last implementation registered is used.</span></span>

<span data-ttu-id="72e2b-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je výchozí `IHostLifetime` implementace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="72e2b-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="72e2b-432">Naslouchá pro <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM a volá <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="72e2b-433">Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="72e2b-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="72e2b-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="72e2b-434">IHostEnvironment</span></span>

<span data-ttu-id="72e2b-435">Vložením služby <xref:Microsoft.Extensions.Hosting.IHostEnvironment> do třídy získáte informace o následujících nastaveních:</span><span class="sxs-lookup"><span data-stu-id="72e2b-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="72e2b-436">NázevAplikace</span><span class="sxs-lookup"><span data-stu-id="72e2b-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="72e2b-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="72e2b-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="72e2b-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="72e2b-438">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="72e2b-439">Webové aplikace implementují rozhraní `IWebHostEnvironment`, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="72e2b-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="72e2b-440">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="72e2b-440">Host configuration</span></span>

<span data-ttu-id="72e2b-441">Konfigurace hostitele se používá pro vlastnosti implementace <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="72e2b-442">Konfigurace hostitele je k dispozici z [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) v rámci <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="72e2b-443">Po `ConfigureAppConfiguration`se `HostBuilderContext.Configuration` nahradí konfigurací aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="72e2b-444">Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> v `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="72e2b-445">`ConfigureHostConfiguration` lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="72e2b-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="72e2b-446">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="72e2b-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="72e2b-447">`CreateDefaultBuilder`obsahuje poskytovatele proměnné prostředí s předponou `DOTNET_` a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="72e2b-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72e2b-448">U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="72e2b-449">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="72e2b-450">Například hodnota proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="72e2b-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="72e2b-451">Následující příklad vytvoří konfiguraci hostitele:</span><span class="sxs-lookup"><span data-stu-id="72e2b-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="72e2b-452">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="72e2b-452">App configuration</span></span>

<span data-ttu-id="72e2b-453">Konfigurace aplikace se vytvoří voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="72e2b-454">`ConfigureAppConfiguration` lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="72e2b-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="72e2b-455">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="72e2b-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="72e2b-456">Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di.</span><span class="sxs-lookup"><span data-stu-id="72e2b-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="72e2b-457">Konfigurace hostitele je také přidána do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="72e2b-458">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="72e2b-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="72e2b-459">Nastavení pro všechny typy aplikací</span><span class="sxs-lookup"><span data-stu-id="72e2b-459">Settings for all app types</span></span>

<span data-ttu-id="72e2b-460">V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP.</span><span class="sxs-lookup"><span data-stu-id="72e2b-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="72e2b-461">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="72e2b-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="72e2b-462">ApplicationName</span></span>

<span data-ttu-id="72e2b-463">Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="72e2b-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="72e2b-464">**Klíč**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="72e2b-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="72e2b-465">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-465">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-466">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="72e2b-467">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="72e2b-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="72e2b-468">K nastavení této hodnoty použijte proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-468">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="72e2b-469">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="72e2b-469">ContentRootPath</span></span>

<span data-ttu-id="72e2b-470">Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="72e2b-471">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="72e2b-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="72e2b-472">**Klíč**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="72e2b-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="72e2b-473">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-473">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-474">**Výchozí**: složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="72e2b-475">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="72e2b-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="72e2b-476">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseContentRoot` volání v `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="72e2b-477">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="72e2b-477">For more information, see:</span></span>

* [<span data-ttu-id="72e2b-478">Základy: kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="72e2b-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="72e2b-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="72e2b-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="72e2b-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="72e2b-480">EnvironmentName</span></span>

<span data-ttu-id="72e2b-481">Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="72e2b-482">Mezi hodnoty definované rozhraní patří `Development`, `Staging`a `Production`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="72e2b-483">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="72e2b-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="72e2b-484">**Klíč**: `environment`</span><span class="sxs-lookup"><span data-stu-id="72e2b-484">**Key**: `environment`</span></span>  
<span data-ttu-id="72e2b-485">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-485">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-486">**Výchozí**: `Production`</span><span class="sxs-lookup"><span data-stu-id="72e2b-486">**Default**: `Production`</span></span>  
<span data-ttu-id="72e2b-487">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="72e2b-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="72e2b-488">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseEnvironment` volání v `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="72e2b-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="72e2b-489">ShutdownTimeout</span></span>

<span data-ttu-id="72e2b-490">[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="72e2b-491">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="72e2b-491">The default value is five seconds.</span></span>  <span data-ttu-id="72e2b-492">Po uplynutí časového limitu hostitel:</span><span class="sxs-lookup"><span data-stu-id="72e2b-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="72e2b-493">Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="72e2b-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="72e2b-494">Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="72e2b-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="72e2b-495">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="72e2b-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="72e2b-496">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="72e2b-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="72e2b-497">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="72e2b-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="72e2b-498">**Klíč**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="72e2b-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="72e2b-499">**Zadejte**: `int`</span><span class="sxs-lookup"><span data-stu-id="72e2b-499">**Type**: `int`</span></span>  
<span data-ttu-id="72e2b-500">**Výchozí hodnota**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="72e2b-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="72e2b-501">**Proměnná prostředí**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="72e2b-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="72e2b-502">K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="72e2b-503">Následující příklad nastaví časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="72e2b-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="72e2b-504">Nastavení pro Web Apps</span><span class="sxs-lookup"><span data-stu-id="72e2b-504">Settings for web apps</span></span>

<span data-ttu-id="72e2b-505">Některá nastavení hostitele se vztahují jenom na úlohy HTTP.</span><span class="sxs-lookup"><span data-stu-id="72e2b-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="72e2b-506">Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="72e2b-507">Pro tato nastavení jsou k dispozici metody rozšíření na `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="72e2b-508">Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` je instance `IWebHostBuilder`, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="72e2b-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="72e2b-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="72e2b-509">CaptureStartupErrors</span></span>

<span data-ttu-id="72e2b-510">Při `false`dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="72e2b-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="72e2b-511">Při `true`zachytí hostitel při spuštění výjimky a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="72e2b-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="72e2b-512">**Klíč**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="72e2b-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="72e2b-513">**Typ**: `bool` (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="72e2b-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="72e2b-514">**Výchozí**: výchozí nastavení `false`, pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="72e2b-515">**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="72e2b-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="72e2b-516">Chcete-li nastavit tuto hodnotu, použijte `CaptureStartupErrors`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="72e2b-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="72e2b-517">DetailedErrors</span></span>

<span data-ttu-id="72e2b-518">Pokud je povoleno nebo když je prostředí `Development`, aplikace zachycuje podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="72e2b-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="72e2b-519">**Klíč**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="72e2b-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="72e2b-520">**Typ**: `bool` (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="72e2b-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="72e2b-521">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="72e2b-521">**Default**: `false`</span></span>  
<span data-ttu-id="72e2b-522">**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="72e2b-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="72e2b-523">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="72e2b-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="72e2b-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="72e2b-525">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="72e2b-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="72e2b-526">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="72e2b-527">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="72e2b-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="72e2b-528">**Klíč**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="72e2b-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="72e2b-529">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-529">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-530">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="72e2b-530">**Default**: Empty string</span></span>  
<span data-ttu-id="72e2b-531">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="72e2b-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="72e2b-532">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="72e2b-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="72e2b-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="72e2b-534">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="72e2b-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="72e2b-535">**Klíč**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="72e2b-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="72e2b-536">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-536">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-537">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="72e2b-537">**Default**: Empty string</span></span>  
<span data-ttu-id="72e2b-538">**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="72e2b-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="72e2b-539">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="72e2b-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="72e2b-540">HTTPS_Port</span></span>

<span data-ttu-id="72e2b-541">Port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="72e2b-541">The HTTPS redirect port.</span></span> <span data-ttu-id="72e2b-542">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="72e2b-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="72e2b-543">**Klíč**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="72e2b-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="72e2b-544">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-544">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-545">**Výchozí**hodnota: výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="72e2b-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="72e2b-546">**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="72e2b-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="72e2b-547">Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="72e2b-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="72e2b-548">PreferHostingUrls</span></span>

<span data-ttu-id="72e2b-549">Určuje, jestli by měl hostitel naslouchat adresám URL nakonfigurovaným pomocí `IWebHostBuilder` místo adres URL nakonfigurovaných pomocí implementace `IServer`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="72e2b-550">**Klíč**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="72e2b-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="72e2b-551">**Typ**: `bool` (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="72e2b-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="72e2b-552">**Výchozí**: `true`</span><span class="sxs-lookup"><span data-stu-id="72e2b-552">**Default**: `true`</span></span>  
<span data-ttu-id="72e2b-553">**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="72e2b-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="72e2b-554">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `PreferHostingUrls`volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="72e2b-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="72e2b-555">PreventHostingStartup</span></span>

<span data-ttu-id="72e2b-556">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="72e2b-557">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="72e2b-558">**Klíč**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="72e2b-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="72e2b-559">**Typ**: `bool` (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="72e2b-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="72e2b-560">**Výchozí**: `false`</span><span class="sxs-lookup"><span data-stu-id="72e2b-560">**Default**: `false`</span></span>  
<span data-ttu-id="72e2b-561">**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="72e2b-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="72e2b-562">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseSetting` volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="72e2b-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="72e2b-563">StartupAssembly</span></span>

<span data-ttu-id="72e2b-564">Sestavení, ve kterém se má hledat `Startup` třídy</span><span class="sxs-lookup"><span data-stu-id="72e2b-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="72e2b-565">**Klíč**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="72e2b-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="72e2b-566">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-566">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-567">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="72e2b-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="72e2b-568">**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="72e2b-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="72e2b-569">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="72e2b-570">`UseStartup` může převzít název sestavení (`string`) nebo typ (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="72e2b-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="72e2b-571">Je-li voláno více metod `UseStartup`, má poslední název přednost.</span><span class="sxs-lookup"><span data-stu-id="72e2b-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="72e2b-572">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="72e2b-572">URLs</span></span>

<span data-ttu-id="72e2b-573">Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="72e2b-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="72e2b-574">například `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="72e2b-575">Pomocí příkazu "\*" označíte, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="72e2b-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="72e2b-576">Protokol (`http://` nebo `https://`) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="72e2b-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="72e2b-577">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="72e2b-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="72e2b-578">**Klíč**: `urls`</span><span class="sxs-lookup"><span data-stu-id="72e2b-578">**Key**: `urls`</span></span>  
<span data-ttu-id="72e2b-579">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-579">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-580">**Výchozí**: `http://localhost:5000` a `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="72e2b-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="72e2b-581">**Proměnná prostředí**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="72e2b-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="72e2b-582">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseUrls`volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="72e2b-583">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="72e2b-584">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="72e2b-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="72e2b-585">WebRoot</span></span>

<span data-ttu-id="72e2b-586">Relativní cesta ke statickým prostředkům aplikace</span><span class="sxs-lookup"><span data-stu-id="72e2b-586">The relative path to the app's static assets.</span></span>

<span data-ttu-id="72e2b-587">**Klíč**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="72e2b-587">**Key**: `webroot`</span></span>  
<span data-ttu-id="72e2b-588">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-588">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-589">**Výchozí**: výchozí hodnota je `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-589">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="72e2b-590">Cesta k *obsahu {root}/wwwroot* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="72e2b-590">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="72e2b-591">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="72e2b-591">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="72e2b-592">**Proměnná prostředí**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="72e2b-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="72e2b-593">Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseWebRoot`volání:</span><span class="sxs-lookup"><span data-stu-id="72e2b-593">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="72e2b-594">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="72e2b-594">For more information, see:</span></span>

* [<span data-ttu-id="72e2b-595">Základy: web root</span><span class="sxs-lookup"><span data-stu-id="72e2b-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="72e2b-596">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="72e2b-596">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="72e2b-597">Správa životnosti hostitele</span><span class="sxs-lookup"><span data-stu-id="72e2b-597">Manage the host lifetime</span></span>

<span data-ttu-id="72e2b-598">Volejte metody pro sestavenou <xref:Microsoft.Extensions.Hosting.IHost> implementaci pro spuštění a zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="72e2b-599">Tyto metody ovlivňují všechny <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="72e2b-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="72e2b-600">Spusťte</span><span class="sxs-lookup"><span data-stu-id="72e2b-600">Run</span></span>

<span data-ttu-id="72e2b-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.</span><span class="sxs-lookup"><span data-stu-id="72e2b-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="72e2b-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-602">RunAsync</span></span>

<span data-ttu-id="72e2b-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task>, která se dokončí při aktivaci tokenu zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="72e2b-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-604">RunConsoleAsync</span></span>

<span data-ttu-id="72e2b-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestavování a spouštění hostitele a čeká na vypnutí <kbd>klávesové zkratky Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="72e2b-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="72e2b-606">Start</span><span class="sxs-lookup"><span data-stu-id="72e2b-606">Start</span></span>

<span data-ttu-id="72e2b-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spouští hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="72e2b-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="72e2b-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-608">StartAsync</span></span>

<span data-ttu-id="72e2b-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí hostitele a vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="72e2b-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se volá na začátku `StartAsync`, která čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="72e2b-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="72e2b-611">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="72e2b-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-612">StopAsync</span></span>

<span data-ttu-id="72e2b-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> se pokusí zastavit hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="72e2b-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="72e2b-614">WaitForShutdown</span></span>

<span data-ttu-id="72e2b-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například pomocí <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="72e2b-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="72e2b-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="72e2b-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje prostřednictvím daného tokenu a volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="72e2b-618">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="72e2b-618">External control</span></span>

<span data-ttu-id="72e2b-619">Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="72e2b-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="72e2b-620">ASP.NET Core aplikace nakonfigurují a spouštějí hostitele.</span><span class="sxs-lookup"><span data-stu-id="72e2b-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="72e2b-621">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="72e2b-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="72e2b-622">Tento článek se zabývá ASP.NET Core obecný hostitel (<xref:Microsoft.Extensions.Hosting.HostBuilder>), který se používá pro aplikace, které nezpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="72e2b-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="72e2b-623">Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele, aby bylo možné rozšířit pole hostitelských scénářů.</span><span class="sxs-lookup"><span data-stu-id="72e2b-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="72e2b-624">Zasílání zpráv, úloh na pozadí a dalších úloh nevyužívajících HTTP na základě výhody obecného hostitele z různých možností, jako je konfigurace, vkládání závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="72e2b-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="72e2b-625">Obecný hostitel je v ASP.NET Core 2,1 novinkou a není vhodný pro scénáře hostování webů.</span><span class="sxs-lookup"><span data-stu-id="72e2b-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="72e2b-626">U scénářů pro hostování webů použijte [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="72e2b-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="72e2b-627">Obecný hostitel nahradí webového hostitele v budoucí verzi a bude fungovat jako primární rozhraní API v rámci scénářů HTTP i non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="72e2b-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="72e2b-628">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="72e2b-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="72e2b-629">Při spuštění ukázkové aplikace v [Visual Studio Code](https://code.visualstudio.com/)použijte *externí nebo integrovaný terminál*.</span><span class="sxs-lookup"><span data-stu-id="72e2b-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="72e2b-630">Nespouštějte ukázku ve `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="72e2b-631">Nastavení konzoly v Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="72e2b-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="72e2b-632">Otevřete soubor *. VSCode/Launch. JSON* .</span><span class="sxs-lookup"><span data-stu-id="72e2b-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="72e2b-633">V konfiguraci **spuštění .NET Core (konzola)** vyhledejte položku **Konzola** .</span><span class="sxs-lookup"><span data-stu-id="72e2b-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="72e2b-634">Nastavte hodnotu buď `externalTerminal`, nebo `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="72e2b-635">Úvod</span><span class="sxs-lookup"><span data-stu-id="72e2b-635">Introduction</span></span>

<span data-ttu-id="72e2b-636">Knihovna obecných hostitelů je k dispozici v oboru názvů <xref:Microsoft.Extensions.Hosting> a poskytuje balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) .</span><span class="sxs-lookup"><span data-stu-id="72e2b-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="72e2b-637">Balíček `Microsoft.Extensions.Hosting` obsahuje [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="72e2b-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="72e2b-638"><xref:Microsoft.Extensions.Hosting.IHostedService> je vstupním bodem pro provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="72e2b-639">Jednotlivé implementace <xref:Microsoft.Extensions.Hosting.IHostedService> jsou spouštěny v pořadí [Registrace služby v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="72e2b-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="72e2b-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> se volá na každém <xref:Microsoft.Extensions.Hosting.IHostedService> při spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> se volá v pořadí zpětné registrace při bezproblémovém vypnutí hostitele.</span><span class="sxs-lookup"><span data-stu-id="72e2b-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="72e2b-641">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="72e2b-641">Set up a host</span></span>

<span data-ttu-id="72e2b-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> je hlavní součást, kterou knihovny a aplikace používají k inicializaci, sestavování a spouštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="72e2b-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="72e2b-643">Možnosti</span><span class="sxs-lookup"><span data-stu-id="72e2b-643">Options</span></span>

<span data-ttu-id="72e2b-644"><xref:Microsoft.Extensions.Hosting.HostOptions> nakonfigurovat možnosti pro <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="72e2b-645">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="72e2b-645">Shutdown timeout</span></span>

<span data-ttu-id="72e2b-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="72e2b-647">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="72e2b-647">The default value is five seconds.</span></span>

<span data-ttu-id="72e2b-648">Následující konfigurace možnosti v `Program.Main` zvyšuje výchozí časový limit pro vypnutí v pěti sekundách na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="72e2b-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="72e2b-649">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="72e2b-649">Default services</span></span>

<span data-ttu-id="72e2b-650">Během inicializace hostitele jsou registrovány následující služby:</span><span class="sxs-lookup"><span data-stu-id="72e2b-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="72e2b-651">[Prostředí](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="72e2b-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="72e2b-652">[Konfigurace](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="72e2b-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="72e2b-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="72e2b-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="72e2b-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="72e2b-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="72e2b-655">[Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="72e2b-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="72e2b-656">[Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="72e2b-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="72e2b-657">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="72e2b-657">Host configuration</span></span>

<span data-ttu-id="72e2b-658">Konfiguraci hostitele vytvořil:</span><span class="sxs-lookup"><span data-stu-id="72e2b-658">Host configuration is created by:</span></span>

* <span data-ttu-id="72e2b-659">Volání rozšiřujících metod v <xref:Microsoft.Extensions.Hosting.IHostBuilder> pro nastavení [kořenového adresáře obsahu](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="72e2b-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="72e2b-660">Čtení konfigurace od zprostředkovatelů konfigurace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="72e2b-661">Metody rozšíření</span><span class="sxs-lookup"><span data-stu-id="72e2b-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="72e2b-662">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="72e2b-662">Application key (name)</span></span>

<span data-ttu-id="72e2b-663">Vlastnost [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="72e2b-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="72e2b-664">Chcete-li nastavit hodnotu explicitně, použijte [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="72e2b-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="72e2b-665">**Klíč**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="72e2b-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="72e2b-666">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-666">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-667">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="72e2b-668">**Nastavit pomocí**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="72e2b-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="72e2b-669">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="72e2b-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="72e2b-670">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="72e2b-670">Content root</span></span>

<span data-ttu-id="72e2b-671">Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="72e2b-672">**Klíč**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="72e2b-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="72e2b-673">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-673">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-674">**Výchozí**: výchozí nastavení složky, kde se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="72e2b-675">**Nastavit pomocí**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="72e2b-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="72e2b-676">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="72e2b-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="72e2b-677">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="72e2b-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="72e2b-678">Další informace najdete v tématu [základy: kořen obsahu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="72e2b-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="72e2b-679">Prostředí</span><span class="sxs-lookup"><span data-stu-id="72e2b-679">Environment</span></span>

<span data-ttu-id="72e2b-680">Nastaví [prostředí](xref:fundamentals/environments)aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="72e2b-681">**Klíč**: `environment`</span><span class="sxs-lookup"><span data-stu-id="72e2b-681">**Key**: `environment`</span></span>  
<span data-ttu-id="72e2b-682">**Zadejte**: `string`</span><span class="sxs-lookup"><span data-stu-id="72e2b-682">**Type**: `string`</span></span>  
<span data-ttu-id="72e2b-683">**Výchozí**: `Production`</span><span class="sxs-lookup"><span data-stu-id="72e2b-683">**Default**: `Production`</span></span>  
<span data-ttu-id="72e2b-684">**Nastavit pomocí**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="72e2b-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="72e2b-685">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="72e2b-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="72e2b-686">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-686">The environment can be set to any value.</span></span> <span data-ttu-id="72e2b-687">Mezi hodnoty definované rozhraní patří `Development`, `Staging`a `Production`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="72e2b-688">V hodnotách se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="72e2b-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="72e2b-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="72e2b-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="72e2b-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele.</span><span class="sxs-lookup"><span data-stu-id="72e2b-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="72e2b-691">Konfigurace hostitele se používá k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="72e2b-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="72e2b-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="72e2b-693">Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.</span><span class="sxs-lookup"><span data-stu-id="72e2b-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="72e2b-694">Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé.</span><span class="sxs-lookup"><span data-stu-id="72e2b-694">No providers are included by default.</span></span> <span data-ttu-id="72e2b-695">Musíte explicitně zadat, které poskytovatelé konfigurace aplikace vyžaduje v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, včetně:</span><span class="sxs-lookup"><span data-stu-id="72e2b-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="72e2b-696">Konfigurace souboru (například ze souboru *HostSettings. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="72e2b-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="72e2b-697">Konfigurace proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-697">Environment variable configuration.</span></span>
* <span data-ttu-id="72e2b-698">Konfigurace argumentů příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="72e2b-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="72e2b-699">Všichni další povinní poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-699">Any other required configuration providers.</span></span>

<span data-ttu-id="72e2b-700">Konfigurace souborů hostitele je povolená zadáním základní cesty aplikace s `SetBasePath` následovaným voláním jednoho z [poskytovatelů konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72e2b-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="72e2b-701">Ukázková aplikace používá soubor JSON *HostSettings. JSON*a volá <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> ke využívání nastavení konfigurace hostitele souboru.</span><span class="sxs-lookup"><span data-stu-id="72e2b-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="72e2b-702">Chcete-li přidat [konfiguraci proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v Tvůrci hostitele.</span><span class="sxs-lookup"><span data-stu-id="72e2b-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="72e2b-703">`AddEnvironmentVariables` akceptuje volitelnou uživatelem definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="72e2b-704">Ukázková aplikace používá předponu `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="72e2b-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="72e2b-705">Předpona je odebrána při čtení proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="72e2b-706">Když je nakonfigurován hostitel ukázkové aplikace, hodnota proměnné prostředí pro `PREFIX_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="72e2b-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="72e2b-707">Během vývoje při používání sady [Visual Studio](https://visualstudio.microsoft.com) nebo při spuštění aplikace s `dotnet run`lze proměnné prostředí nastavit v souboru *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="72e2b-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="72e2b-708">V [Visual Studio Code](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *. VSCode/Launch. JSON* během vývoje.</span><span class="sxs-lookup"><span data-stu-id="72e2b-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="72e2b-709">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="72e2b-710">[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="72e2b-711">K povolení argumentů příkazového řádku pro přepsání konfigurace poskytované předchozími poskytovateli konfigurace se přidá poslední konfigurace příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="72e2b-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="72e2b-712">*HostSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="72e2b-712">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="72e2b-713">Další konfiguraci lze poskytnout pomocí klíčů [ApplicationName](#application-key-name) a [contentRoot](#content-root) .</span><span class="sxs-lookup"><span data-stu-id="72e2b-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="72e2b-714">Příklad `HostBuilder` konfigurace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="72e2b-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="72e2b-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="72e2b-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="72e2b-716">Konfigurace aplikace se vytvoří voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> v implementaci <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="72e2b-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="72e2b-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="72e2b-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="72e2b-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="72e2b-719">Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last.</span><span class="sxs-lookup"><span data-stu-id="72e2b-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="72e2b-720">Konfigurace vytvořená nástrojem <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="72e2b-721">Konfigurace aplikace automaticky přijímá konfiguraci hostitele, kterou poskytuje [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="72e2b-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="72e2b-722">Příklad konfigurace aplikace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="72e2b-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="72e2b-723">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="72e2b-723">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="72e2b-724">*appSettings. Vývoj. JSON*:</span><span class="sxs-lookup"><span data-stu-id="72e2b-724">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="72e2b-725">*appSettings. Produkční. JSON*:</span><span class="sxs-lookup"><span data-stu-id="72e2b-725">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="72e2b-726">Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="72e2b-727">Ukázková aplikace přesune své soubory nastavení aplikace JSON a *HostSettings. JSON* s následující položkou `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="72e2b-728">Metody rozšíření konfigurace, například <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, například [Microsoft. Extensions. Configuration. JSON](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft. Extensions. Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="72e2b-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="72e2b-729">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), tyto balíčky musí být kromě základního balíčku [Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) přidány do projektu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="72e2b-730">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="72e2b-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="72e2b-731">ConfigureServices</span></span>

<span data-ttu-id="72e2b-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="72e2b-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="72e2b-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="72e2b-734">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="72e2b-735">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="72e2b-736">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá metodu rozšíření `AddHostedService` k přidání služby pro události života, `LifetimeEventsHostedService`a časově omezená úloha na pozadí, `TimedHostedService`do aplikace:</span><span class="sxs-lookup"><span data-stu-id="72e2b-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="72e2b-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="72e2b-737">ConfigureLogging</span></span>

<span data-ttu-id="72e2b-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> přidá delegáta pro konfiguraci poskytnuté <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="72e2b-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> může být voláno vícekrát s doplňkovou výsledků.</span><span class="sxs-lookup"><span data-stu-id="72e2b-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="72e2b-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="72e2b-740">UseConsoleLifetime</span></span>

<span data-ttu-id="72e2b-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> naslouchá <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM a volá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pro spuštění procesu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="72e2b-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="72e2b-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="72e2b-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je předem registrován jako výchozí implementace životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="72e2b-744">Je použita poslední registrovaná doba života.</span><span class="sxs-lookup"><span data-stu-id="72e2b-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="72e2b-745">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="72e2b-745">Container configuration</span></span>

<span data-ttu-id="72e2b-746">Pro podporu zapojení do jiných kontejnerů může hostitel přijmout <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="72e2b-747">Poskytnutí továrny není součástí registrace DI Container, ale je místo toho hostitel, který se používá k vytvoření konkrétního kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="72e2b-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="72e2b-748">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí továrnu, která byla použita k vytvoření poskytovatele služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="72e2b-749">Vlastní konfigurace kontejneru je spravovaná metodou <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="72e2b-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> poskytuje silné typové prostředí pro konfiguraci kontejneru nad podkladovým rozhraním API hostitele.</span><span class="sxs-lookup"><span data-stu-id="72e2b-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="72e2b-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="72e2b-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="72e2b-752">Vytvoření kontejneru služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="72e2b-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="72e2b-753">Poskytněte objekt pro vytváření kontejnerů služby:</span><span class="sxs-lookup"><span data-stu-id="72e2b-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="72e2b-754">Použijte objekt pro vytváření a nakonfigurujte vlastní kontejner služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="72e2b-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="72e2b-755">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="72e2b-755">Extensibility</span></span>

<span data-ttu-id="72e2b-756">Rozšiřitelnost hostitelů se provádí s metodami rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="72e2b-757">Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementaci s příkladem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) znázorněným v <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="72e2b-758">Aplikace vytvoří metodu rozšíření `UseHostedService` k registraci hostované služby předané v `T`:</span><span class="sxs-lookup"><span data-stu-id="72e2b-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="72e2b-759">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="72e2b-759">Manage the host</span></span>

<span data-ttu-id="72e2b-760">Implementace <xref:Microsoft.Extensions.Hosting.IHost> zodpovídá za spuštění a zastavení <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrované v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="72e2b-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="72e2b-761">Spusťte</span><span class="sxs-lookup"><span data-stu-id="72e2b-761">Run</span></span>

<span data-ttu-id="72e2b-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud není hostitel vypnutý:</span><span class="sxs-lookup"><span data-stu-id="72e2b-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="72e2b-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-763">RunAsync</span></span>

<span data-ttu-id="72e2b-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task>, která se dokončí, když se aktivuje token zrušení nebo vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="72e2b-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-765">RunConsoleAsync</span></span>

<span data-ttu-id="72e2b-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestavování a spouštění hostitele a čeká na vypnutí <kbd>klávesové zkratky Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="72e2b-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="72e2b-767">Spustit a StopAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-767">Start and StopAsync</span></span>

<span data-ttu-id="72e2b-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spouští hostitele synchronně.</span><span class="sxs-lookup"><span data-stu-id="72e2b-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="72e2b-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> se pokusí zastavit hostitele v zadaném časovém limitu.</span><span class="sxs-lookup"><span data-stu-id="72e2b-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="72e2b-770">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="72e2b-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="72e2b-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="72e2b-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> zastaví aplikaci.</span><span class="sxs-lookup"><span data-stu-id="72e2b-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="72e2b-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="72e2b-773">WaitForShutdown</span></span>

<span data-ttu-id="72e2b-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se spustí prostřednictvím <xref:Microsoft.Extensions.Hosting.IHostLifetime>, jako je například `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (naslouchat <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="72e2b-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="72e2b-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="72e2b-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="72e2b-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="72e2b-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje prostřednictvím daného tokenu a volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="72e2b-778">Externí ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="72e2b-778">External control</span></span>

<span data-ttu-id="72e2b-779">Externí kontrolu hostitele lze dosáhnout pomocí metod, které mohou být volány externě:</span><span class="sxs-lookup"><span data-stu-id="72e2b-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="72e2b-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se volá na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, která čeká na dokončení před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="72e2b-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="72e2b-781">Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="72e2b-782">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="72e2b-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="72e2b-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> poskytuje informace o hostitelském prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="72e2b-784">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) k získání <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>, aby bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="72e2b-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="72e2b-785">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="72e2b-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="72e2b-786">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="72e2b-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="72e2b-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> umožňuje aktivity po spuštění a vypnutí, včetně řádných požadavků na vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="72e2b-788">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="72e2b-789">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="72e2b-789">Cancellation Token</span></span> | <span data-ttu-id="72e2b-790">Aktivováno, když&#8230;</span><span class="sxs-lookup"><span data-stu-id="72e2b-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="72e2b-791">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="72e2b-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="72e2b-792">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="72e2b-793">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="72e2b-793">All requests should be processed.</span></span> <span data-ttu-id="72e2b-794">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="72e2b-795">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="72e2b-796">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="72e2b-796">Requests may still be processing.</span></span> <span data-ttu-id="72e2b-797">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="72e2b-798">Konstruktor – vloží službu <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="72e2b-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="72e2b-799">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání konstruktoru do `LifetimeEventsHostedService` třídy (implementace <xref:Microsoft.Extensions.Hosting.IHostedService>) k registraci událostí.</span><span class="sxs-lookup"><span data-stu-id="72e2b-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="72e2b-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="72e2b-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="72e2b-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> žádosti o ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="72e2b-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="72e2b-802">Následující třída používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> k bezproblémovému vypnutí aplikace, když je volána metoda `Shutdown` třídy:</span><span class="sxs-lookup"><span data-stu-id="72e2b-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="72e2b-803">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="72e2b-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
