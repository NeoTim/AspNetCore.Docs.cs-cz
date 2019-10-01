---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 0eaa3a62370c1e413840bb65f597dc664adafc38
ms.sourcegitcommit: fe88748b762525cb490f7e39089a4760f6a73a24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71688102"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="6eefd-103">Úlohy na pozadí s hostovanými službami v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6eefd-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="6eefd-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6eefd-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6eefd-105">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="6eefd-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="6eefd-106">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="6eefd-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="6eefd-107">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="6eefd-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="6eefd-108">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="6eefd-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="6eefd-109">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="6eefd-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6eefd-110">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6eefd-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="6eefd-111">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="6eefd-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="6eefd-112">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6eefd-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6eefd-113">Ukázková aplikace je k dispozici ve dvou verzích:</span><span class="sxs-lookup"><span data-stu-id="6eefd-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="6eefd-114">Webový hostitel &ndash; webového hostitele je vhodný pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="6eefd-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="6eefd-115">Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="6eefd-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="6eefd-116">Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .</span><span class="sxs-lookup"><span data-stu-id="6eefd-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="6eefd-117">Obecný hostitel &ndash; obecného hostitele je v ASP.NET Core 2,1 nový.</span><span class="sxs-lookup"><span data-stu-id="6eefd-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="6eefd-118">Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="6eefd-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="6eefd-119">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="6eefd-119">Worker Service template</span></span>

<span data-ttu-id="6eefd-120">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="6eefd-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="6eefd-121">Použití šablony jako základu pro aplikaci hostované služby:</span><span class="sxs-lookup"><span data-stu-id="6eefd-121">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

## <a name="package"></a><span data-ttu-id="6eefd-122">Balíček</span><span class="sxs-lookup"><span data-stu-id="6eefd-122">Package</span></span>

<span data-ttu-id="6eefd-123">Odkaz na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) se přidá implicitně pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="6eefd-123">A package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is added implicitly for ASP.NET Core apps.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="6eefd-124">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="6eefd-124">IHostedService interface</span></span>

<span data-ttu-id="6eefd-125"><xref:Microsoft.Extensions.Hosting.IHostedService> Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="6eefd-125">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="6eefd-126">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; obsahujelogikupro`StartAsync` zahájení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="6eefd-127">`StartAsync`se volá *před*:</span><span class="sxs-lookup"><span data-stu-id="6eefd-127">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="6eefd-128">Je nakonfigurovaný kanál zpracování požadavků aplikace (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="6eefd-128">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="6eefd-129">Server se spustí a spustí se [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="6eefd-129">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="6eefd-130">Výchozí chování se dá změnit tak, aby se hostovaná služba `StartAsync` spouštěla po nakonfigurování kanálu aplikace a `ApplicationStarted` zavolala se.</span><span class="sxs-lookup"><span data-stu-id="6eefd-130">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="6eefd-131">Chcete-li změnit výchozí chování, přidejte hostovanou službu`VideosWatcher` (v následujícím příkladu) po volání `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="6eefd-131">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

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
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* <span data-ttu-id="6eefd-132">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Aktivováno, když hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-132">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="6eefd-133">`StopAsync`obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-133">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="6eefd-134">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="6eefd-134">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="6eefd-135">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="6eefd-135">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="6eefd-136">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="6eefd-136">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="6eefd-137">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="6eefd-137">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="6eefd-138">Všechny metody, které `StopAsync` jsou volány v nástroji, by měly vracet výzvu.</span><span class="sxs-lookup"><span data-stu-id="6eefd-138">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="6eefd-139">Úkoly se ale po zrušení žádosti&mdash;zruší, protože volající čeká na dokončení všech úkolů.</span><span class="sxs-lookup"><span data-stu-id="6eefd-139">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="6eefd-140">Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="6eefd-140">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="6eefd-141">Proto žádné metody, které jsou volány nebo `StopAsync` operací, které jsou prováděny v nástroji, nemusí nastat.</span><span class="sxs-lookup"><span data-stu-id="6eefd-141">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="6eefd-142">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="6eefd-142">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="6eefd-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Při použití obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="6eefd-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="6eefd-144">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="6eefd-144">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="6eefd-145">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="6eefd-145">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="6eefd-146">Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="6eefd-146">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="6eefd-147">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="6eefd-147">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="6eefd-148">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` měla by být volána i `StopAsync` v případě, že není volána.</span><span class="sxs-lookup"><span data-stu-id="6eefd-148">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="6eefd-149">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="6eefd-149">BackgroundService</span></span>

<span data-ttu-id="6eefd-150">`BackgroundService`je základní třídou pro implementaci dlouhého běhu <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="6eefd-150">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="6eefd-151">`BackgroundService``ExecuteAsync(CancellationToken stoppingToken)` poskytuje abstraktní metodu, která bude obsahovat logiku služby.</span><span class="sxs-lookup"><span data-stu-id="6eefd-151">`BackgroundService` provides the `ExecuteAsync(CancellationToken stoppingToken)` abstract method to contain the service's logic.</span></span> <span data-ttu-id="6eefd-152">Je `stoppingToken` aktivována, když je volána metoda [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) .</span><span class="sxs-lookup"><span data-stu-id="6eefd-152">The `stoppingToken` is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="6eefd-153">Implementace této metody vrátí `Task` , která představuje celou dobu života služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-153">The implementation of this method returns a `Task` that represents the entire lifetime of the background service.</span></span>

<span data-ttu-id="6eefd-154">*Volitelně můžete* také přepsat metody definované v `IHostedService` pro spuštění spouštěcího a ukončovacího kódu pro vaši službu:</span><span class="sxs-lookup"><span data-stu-id="6eefd-154">In addition, *optionally* override the methods defined on `IHostedService` to run startup and shutdown code for your service:</span></span>

* <span data-ttu-id="6eefd-155">`StopAsync(CancellationToken cancellationToken)`&ndash; sevolá,kdyžhostitel`StopAsync` aplikace provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-155">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` is called when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="6eefd-156">Je `cancellationToken` správcem signalizována, když se hostitel rozhodne vynutit ukončení služby.</span><span class="sxs-lookup"><span data-stu-id="6eefd-156">The `cancellationToken` is signalled when the host decides to forcibly terminate the service.</span></span> <span data-ttu-id="6eefd-157">Pokud je tato metoda přepsána, je **nutné** zavolat `await`(a) metodu základní třídy, aby se služba správně vypnula.</span><span class="sxs-lookup"><span data-stu-id="6eefd-157">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service shuts down properly.</span></span>
* <span data-ttu-id="6eefd-158">`StartAsync(CancellationToken cancellationToken)`&ndash; sevoláke`StartAsync` spuštění služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-158">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` is called to start the background service.</span></span> <span data-ttu-id="6eefd-159">`cancellationToken` Je správcem signalizována, pokud je proces spuštění přerušený.</span><span class="sxs-lookup"><span data-stu-id="6eefd-159">The `cancellationToken` is signalled if the startup process is interrupted.</span></span> <span data-ttu-id="6eefd-160">Implementace vrátí `Task` , který představuje proces spuštění služby.</span><span class="sxs-lookup"><span data-stu-id="6eefd-160">The implementation returns a `Task` that represents the startup process for the service.</span></span> <span data-ttu-id="6eefd-161">Žádné další služby nejsou spuštěny, `Task` dokud se nedokončí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-161">No further services are started until this `Task` completes.</span></span> <span data-ttu-id="6eefd-162">Pokud je tato metoda přepsána, je **nutné** zavolat `await`(a) metodu základní třídy, aby bylo zajištěno, že služba bude spuštěna správně.</span><span class="sxs-lookup"><span data-stu-id="6eefd-162">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service starts properly.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="6eefd-163">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="6eefd-163">Timed background tasks</span></span>

<span data-ttu-id="6eefd-164">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="6eefd-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="6eefd-165">Časovač aktivuje `DoWork` metodu úkolu.</span><span class="sxs-lookup"><span data-stu-id="6eefd-165">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="6eefd-166">Časovač je zakázán `StopAsync` a vyřazen při vyřazení `Dispose`kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="6eefd-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="6eefd-167">Služba je registrována v `IHostBuilder.ConfigureServices` (*program.cs*) s `AddHostedService` metodou rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6eefd-167">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="6eefd-168">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="6eefd-168">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="6eefd-169">Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `BackgroundService`, vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="6eefd-169">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="6eefd-170">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="6eefd-170">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="6eefd-171">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-171">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="6eefd-172">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="6eefd-172">In the following example:</span></span>

* <span data-ttu-id="6eefd-173">Služba je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="6eefd-173">The service is asynchronous.</span></span> <span data-ttu-id="6eefd-174">`DoWork` Metoda`Task`vrátí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-174">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="6eefd-175">Pro demonstrační účely je v `DoWork` metodě očekáváno zpoždění deseti sekund.</span><span class="sxs-lookup"><span data-stu-id="6eefd-175">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="6eefd-176">Do <xref:Microsoft.Extensions.Logging.ILogger> služby se vloží.</span><span class="sxs-lookup"><span data-stu-id="6eefd-176">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="6eefd-177">Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí pro volání `DoWork` metody.</span><span class="sxs-lookup"><span data-stu-id="6eefd-177">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="6eefd-178">`DoWork`Vrátí, který je očekáván v `ExecuteAsync`: `Task`</span><span class="sxs-lookup"><span data-stu-id="6eefd-178">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="6eefd-179">Služby jsou zaregistrované `IHostBuilder.ConfigureServices` v (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="6eefd-179">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="6eefd-180">Hostovaná služba je zaregistrovaná `AddHostedService` v rámci metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6eefd-180">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="6eefd-181">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="6eefd-181">Queued background tasks</span></span>

<span data-ttu-id="6eefd-182">Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako integrované pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="6eefd-182">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="6eefd-183">V následujícím `QueueHostedService` příkladu:</span><span class="sxs-lookup"><span data-stu-id="6eefd-183">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="6eefd-184">Metoda vrátí hodnotu ,která`ExecuteAsync`je očekávána v. `Task` `BackgroundProcessing`</span><span class="sxs-lookup"><span data-stu-id="6eefd-184">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="6eefd-185">Úlohy na pozadí ve frontě se odřadí a spustí v `BackgroundProcessing`.</span><span class="sxs-lookup"><span data-stu-id="6eefd-185">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28,39-40,44)]

<span data-ttu-id="6eefd-186">Služba zpracovává enqueuing úlohy pro hostovanou službu vždy, `w` když je vybraný klíč na vstupním zařízení: `MonitorLoop`</span><span class="sxs-lookup"><span data-stu-id="6eefd-186">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="6eefd-187">`IBackgroundTaskQueue` Je vložen`MonitorLoop` do služby.</span><span class="sxs-lookup"><span data-stu-id="6eefd-187">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="6eefd-188">`IBackgroundTaskQueue.QueueBackgroundWorkItem`je volána pro zařazení pracovní položky do fronty.</span><span class="sxs-lookup"><span data-stu-id="6eefd-188">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="6eefd-189">Služby jsou zaregistrované `IHostBuilder.ConfigureServices` v (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="6eefd-189">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="6eefd-190">Hostovaná služba je zaregistrovaná `AddHostedService` v rámci metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6eefd-190">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6eefd-191">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="6eefd-191">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="6eefd-192">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="6eefd-192">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="6eefd-193">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="6eefd-193">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="6eefd-194">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="6eefd-194">Background task that runs on a timer.</span></span>
* <span data-ttu-id="6eefd-195">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="6eefd-195">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6eefd-196">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="6eefd-196">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="6eefd-197">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="6eefd-197">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="6eefd-198">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6eefd-198">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6eefd-199">Ukázková aplikace je k dispozici ve dvou verzích:</span><span class="sxs-lookup"><span data-stu-id="6eefd-199">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="6eefd-200">Webový hostitel &ndash; webového hostitele je vhodný pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="6eefd-200">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="6eefd-201">Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="6eefd-201">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="6eefd-202">Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .</span><span class="sxs-lookup"><span data-stu-id="6eefd-202">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="6eefd-203">Obecný hostitel &ndash; obecného hostitele je v ASP.NET Core 2,1 nový.</span><span class="sxs-lookup"><span data-stu-id="6eefd-203">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="6eefd-204">Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="6eefd-204">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="6eefd-205">Balíček</span><span class="sxs-lookup"><span data-stu-id="6eefd-205">Package</span></span>

<span data-ttu-id="6eefd-206">Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="6eefd-206">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="6eefd-207">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="6eefd-207">IHostedService interface</span></span>

<span data-ttu-id="6eefd-208">Hostované služby implementují <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6eefd-208">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="6eefd-209">Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="6eefd-209">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="6eefd-210">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; obsahujelogikupro`StartAsync` zahájení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-210">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="6eefd-211">Při použití [webového hostitele](xref:fundamentals/host/web-host) `StartAsync` se volá po spuštění serveru a aktivaci [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="6eefd-211">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="6eefd-212">Při použití [obecného hostitele](xref:fundamentals/host/generic-host) `StartAsync` se před `ApplicationStarted` aktivací volá.</span><span class="sxs-lookup"><span data-stu-id="6eefd-212">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="6eefd-213">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Aktivováno, když hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-213">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="6eefd-214">`StopAsync`obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-214">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="6eefd-215">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="6eefd-215">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="6eefd-216">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="6eefd-216">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="6eefd-217">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="6eefd-217">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="6eefd-218">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="6eefd-218">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="6eefd-219">Všechny metody, které `StopAsync` jsou volány v nástroji, by měly vracet výzvu.</span><span class="sxs-lookup"><span data-stu-id="6eefd-219">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="6eefd-220">Úkoly se ale po zrušení žádosti&mdash;zruší, protože volající čeká na dokončení všech úkolů.</span><span class="sxs-lookup"><span data-stu-id="6eefd-220">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="6eefd-221">Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="6eefd-221">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="6eefd-222">Proto žádné metody, které jsou volány nebo `StopAsync` operací, které jsou prováděny v nástroji, nemusí nastat.</span><span class="sxs-lookup"><span data-stu-id="6eefd-222">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="6eefd-223">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="6eefd-223">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="6eefd-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Při použití obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="6eefd-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="6eefd-225">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="6eefd-225">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="6eefd-226">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="6eefd-226">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="6eefd-227">Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="6eefd-227">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="6eefd-228">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="6eefd-228">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="6eefd-229">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` měla by být volána i `StopAsync` v případě, že není volána.</span><span class="sxs-lookup"><span data-stu-id="6eefd-229">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="6eefd-230">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="6eefd-230">Timed background tasks</span></span>

<span data-ttu-id="6eefd-231">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="6eefd-231">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="6eefd-232">Časovač aktivuje `DoWork` metodu úkolu.</span><span class="sxs-lookup"><span data-stu-id="6eefd-232">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="6eefd-233">Časovač je zakázán `StopAsync` a vyřazen při vyřazení `Dispose`kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="6eefd-233">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="6eefd-234">Služba je zaregistrovaná `Startup.ConfigureServices` v `AddHostedService` s metodou rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6eefd-234">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="6eefd-235">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="6eefd-235">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="6eefd-236">Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `IHostedService`, vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="6eefd-236">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="6eefd-237">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="6eefd-237">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="6eefd-238">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="6eefd-238">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="6eefd-239">V následujícím příkladu <xref:Microsoft.Extensions.Logging.ILogger> je vložena do služby:</span><span class="sxs-lookup"><span data-stu-id="6eefd-239">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="6eefd-240">Hostovaná služba vytvoří obor pro řešení služby úlohy na pozadí, aby volal jeho `DoWork` metodu:</span><span class="sxs-lookup"><span data-stu-id="6eefd-240">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="6eefd-241">Služby jsou zaregistrované `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="6eefd-241">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6eefd-242">Implementace je registrována `AddHostedService` s metodou rozšíření: `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="6eefd-242">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="6eefd-243">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="6eefd-243">Queued background tasks</span></span>

<span data-ttu-id="6eefd-244">Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako integrované pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="6eefd-244">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="6eefd-245">V `QueueHostedService`nástroji se úlohy na pozadí ve frontě odřadí a spustí <xref:Microsoft.Extensions.Hosting.BackgroundService>jako, což je základní třída pro implementaci dlouhého běhu `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="6eefd-245">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="6eefd-246">Služby jsou zaregistrované `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="6eefd-246">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6eefd-247">Implementace je registrována `AddHostedService` s metodou rozšíření: `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="6eefd-247">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="6eefd-248">Ve třídě modelu stránky indexu:</span><span class="sxs-lookup"><span data-stu-id="6eefd-248">In the Index page model class:</span></span>

* <span data-ttu-id="6eefd-249">Je vložen do konstruktoru a přiřazen k `Queue`. `IBackgroundTaskQueue`</span><span class="sxs-lookup"><span data-stu-id="6eefd-249">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="6eefd-250">Je vložený a přiřazený k `_serviceScopeFactory`. <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory></span><span class="sxs-lookup"><span data-stu-id="6eefd-250">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="6eefd-251">Objekt pro vytváření se používá k vytvoření instancí <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, které se používají k vytváření služeb v rámci oboru.</span><span class="sxs-lookup"><span data-stu-id="6eefd-251">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="6eefd-252">Je vytvořen obor, aby bylo možné použít `AppDbContext` ( [vymezenou](xref:fundamentals/dependency-injection#service-lifetimes)) aplikaci k zápisu záznamů `IBackgroundTaskQueue` databáze do služby (singleton).</span><span class="sxs-lookup"><span data-stu-id="6eefd-252">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="6eefd-253">Je-li na stránce indexu vybráno tlačítko **Přidat úkol** , `OnPostAddTask` je metoda spuštěna.</span><span class="sxs-lookup"><span data-stu-id="6eefd-253">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="6eefd-254">`QueueBackgroundWorkItem`je volána pro zařazení pracovní položky do fronty:</span><span class="sxs-lookup"><span data-stu-id="6eefd-254">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6eefd-255">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6eefd-255">Additional resources</span></span>

* [<span data-ttu-id="6eefd-256">Implementace úloh na pozadí v mikroslužbách pomocí IHostedService a třídy BackgroundService</span><span class="sxs-lookup"><span data-stu-id="6eefd-256">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
