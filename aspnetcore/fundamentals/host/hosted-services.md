---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Naučte se implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: c1fbb5ae8ffc4ee506f42df6a4cbbe845b2b903d
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333661"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="e9eef-103">Úlohy na pozadí s hostovanými službami v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9eef-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="e9eef-104">Od [Luke Latham](https://github.com/guardrex) a [Jeow li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="e9eef-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e9eef-105">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="e9eef-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="e9eef-106">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="e9eef-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="e9eef-107">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="e9eef-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="e9eef-108">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="e9eef-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="e9eef-109">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="e9eef-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="e9eef-110">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e9eef-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="e9eef-111">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="e9eef-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="e9eef-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e9eef-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e9eef-113">Ukázková aplikace je k dispozici ve dvou verzích:</span><span class="sxs-lookup"><span data-stu-id="e9eef-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="e9eef-114">Webový hostitel &ndash; webový hostitel je vhodný pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="e9eef-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="e9eef-115">Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="e9eef-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="e9eef-116">Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .</span><span class="sxs-lookup"><span data-stu-id="e9eef-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="e9eef-117">Generic host &ndash; obecný hostitel je v ASP.NET Core 2,1 nový.</span><span class="sxs-lookup"><span data-stu-id="e9eef-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="e9eef-118">Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="e9eef-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="e9eef-119">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="e9eef-119">Worker Service template</span></span>

<span data-ttu-id="e9eef-120">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="e9eef-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="e9eef-121">Použití šablony jako základu pro aplikaci hostované služby:</span><span class="sxs-lookup"><span data-stu-id="e9eef-121">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

## <a name="package"></a><span data-ttu-id="e9eef-122">Balíček</span><span class="sxs-lookup"><span data-stu-id="e9eef-122">Package</span></span>

<span data-ttu-id="e9eef-123">Odkaz na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) se přidá implicitně pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="e9eef-123">A package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is added implicitly for ASP.NET Core apps.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="e9eef-124">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="e9eef-124">IHostedService interface</span></span>

<span data-ttu-id="e9eef-125">Rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService> definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="e9eef-125">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="e9eef-126">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku ke spuštění úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="e9eef-127">`StartAsync` se volá *před*:</span><span class="sxs-lookup"><span data-stu-id="e9eef-127">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="e9eef-128">Je nakonfigurovaný kanál zpracování požadavků aplikace (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="e9eef-128">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="e9eef-129">Server se spustí a spustí se [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="e9eef-129">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="e9eef-130">Výchozí chování se dá změnit tak, aby se po konfiguraci kanálu aplikace `StartAsync` hostované služby spouštěla a volala se `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-130">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="e9eef-131">Chcete-li změnit výchozí chování, přidejte hostovanou službu (`VideosWatcher` v následujícím příkladu) po volání `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-131">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="e9eef-132">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; spouštěna v případě, že hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-132">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="e9eef-133">`StopAsync` obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-133">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="e9eef-134">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="e9eef-134">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="e9eef-135">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="e9eef-135">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="e9eef-136">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="e9eef-136">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="e9eef-137">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="e9eef-137">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="e9eef-138">Všechny metody, které jsou volány v `StopAsync`, by měly vracet dotaz znovu.</span><span class="sxs-lookup"><span data-stu-id="e9eef-138">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="e9eef-139">Po zrušení se ale úkoly neopustily, protože volající @ no__t-0the očekává, že se všechny úkoly dokončí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-139">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="e9eef-140">Pokud se aplikace neočekávaně ukončí (například proces aplikace nebude úspěšný), `StopAsync` se nemusí volat.</span><span class="sxs-lookup"><span data-stu-id="e9eef-140">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="e9eef-141">Proto se nemusí vyskytovat žádné metody volané nebo operace prováděné v `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-141">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="e9eef-142">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="e9eef-142">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="e9eef-143">Při použití obecného hostitele <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>.</span><span class="sxs-lookup"><span data-stu-id="e9eef-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="e9eef-144">Další informace najdete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="e9eef-144">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="e9eef-145">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="e9eef-145">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="e9eef-146">Další informace najdete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="e9eef-146">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="e9eef-147">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e9eef-147">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="e9eef-148">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` by měla být volána i v případě, že není volána `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-148">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="e9eef-149">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="e9eef-149">BackgroundService</span></span>

<span data-ttu-id="e9eef-150">`BackgroundService` je základní třídou pro implementaci dlouhého běhu <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="e9eef-150">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="e9eef-151">`BackgroundService` poskytuje abstraktní metodu `ExecuteAsync(CancellationToken stoppingToken)`, která bude obsahovat logiku služby.</span><span class="sxs-lookup"><span data-stu-id="e9eef-151">`BackgroundService` provides the `ExecuteAsync(CancellationToken stoppingToken)` abstract method to contain the service's logic.</span></span> <span data-ttu-id="e9eef-152">@No__t-0 se aktivuje, když se zavolá [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) .</span><span class="sxs-lookup"><span data-stu-id="e9eef-152">The `stoppingToken` is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="e9eef-153">Implementace této metody vrací `Task`, který představuje celou dobu života služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-153">The implementation of this method returns a `Task` that represents the entire lifetime of the background service.</span></span>

<span data-ttu-id="e9eef-154">*Volitelně můžete* také přepsat metody definované v `IHostedService` pro spuštění kódu spuštění a vypnutí pro vaši službu:</span><span class="sxs-lookup"><span data-stu-id="e9eef-154">In addition, *optionally* override the methods defined on `IHostedService` to run startup and shutdown code for your service:</span></span>

* <span data-ttu-id="e9eef-155">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` se volá, když hostitel aplikace provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-155">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` is called when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="e9eef-156">@No__t-0 je správcem signalizována, když se hostitel rozhodne vynutit ukončení služby.</span><span class="sxs-lookup"><span data-stu-id="e9eef-156">The `cancellationToken` is signalled when the host decides to forcibly terminate the service.</span></span> <span data-ttu-id="e9eef-157">Pokud je tato metoda přepsána, je **nutné** zavolat (a `await`) metody základní třídy, aby se zajistilo, že se služba správně ukončí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-157">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service shuts down properly.</span></span>
* <span data-ttu-id="e9eef-158">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` se volá ke spuštění služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-158">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` is called to start the background service.</span></span> <span data-ttu-id="e9eef-159">@No__t-0 je správcem signalizována, pokud dojde k přerušení procesu spouštění.</span><span class="sxs-lookup"><span data-stu-id="e9eef-159">The `cancellationToken` is signalled if the startup process is interrupted.</span></span> <span data-ttu-id="e9eef-160">Implementace vrací `Task`, který představuje proces spuštění služby.</span><span class="sxs-lookup"><span data-stu-id="e9eef-160">The implementation returns a `Task` that represents the startup process for the service.</span></span> <span data-ttu-id="e9eef-161">Žádné další služby nejsou spuštěny, dokud se nedokončí tato `Task`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-161">No further services are started until this `Task` completes.</span></span> <span data-ttu-id="e9eef-162">Pokud je tato metoda přepsána, je **nutné** zavolat (a `await`) metody základní třídy, aby se zajistilo, že se služba spustí správně.</span><span class="sxs-lookup"><span data-stu-id="e9eef-162">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service starts properly.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="e9eef-163">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="e9eef-163">Timed background tasks</span></span>

<span data-ttu-id="e9eef-164">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="e9eef-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="e9eef-165">Časovač aktivuje metodu @no__t 0 úlohy.</span><span class="sxs-lookup"><span data-stu-id="e9eef-165">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="e9eef-166">Časovač je zakázán na `StopAsync` a odstraněn, když je kontejner služby vyřazen v `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="e9eef-167">Služba je zaregistrovaná v `IHostBuilder.ConfigureServices` (*program.cs*) s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-167">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="e9eef-168">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="e9eef-168">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="e9eef-169">Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `BackgroundService`, vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="e9eef-169">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="e9eef-170">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="e9eef-170">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="e9eef-171">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-171">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="e9eef-172">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e9eef-172">In the following example:</span></span>

* <span data-ttu-id="e9eef-173">Služba je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="e9eef-173">The service is asynchronous.</span></span> <span data-ttu-id="e9eef-174">Metoda `DoWork` vrátí `Task`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-174">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="e9eef-175">Pro demonstrační účely je v metodě `DoWork` očekáváno zpoždění deseti sekund.</span><span class="sxs-lookup"><span data-stu-id="e9eef-175">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="e9eef-176">Do služby se vloží <xref:Microsoft.Extensions.Logging.ILogger>.</span><span class="sxs-lookup"><span data-stu-id="e9eef-176">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="e9eef-177">Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí, aby volala metodu `DoWork`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-177">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="e9eef-178">`DoWork` vrátí `Task`, který je očekáván v `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-178">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="e9eef-179">Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="e9eef-179">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="e9eef-180">Hostovaná služba je registrovaná s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-180">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="e9eef-181">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="e9eef-181">Queued background tasks</span></span>

<span data-ttu-id="e9eef-182">Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako integrované pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="e9eef-182">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="e9eef-183">V následujícím příkladu `QueueHostedService`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-183">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="e9eef-184">Metoda `BackgroundProcessing` vrátí hodnotu `Task`, která je očekávána v `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-184">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="e9eef-185">Úlohy na pozadí ve frontě se odřadí a spustí v `BackgroundProcessing`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-185">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="e9eef-186">Pracovní položky jsou očekávány před zastavením služby v `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-186">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="e9eef-187">Služba `MonitorLoop` zpracovává úlohy enqueuing pro hostovanou službu pokaždé, když se na vstupním zařízení vybere klíč `w`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-187">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="e9eef-188">@No__t-0 se vloží do služby `MonitorLoop`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-188">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="e9eef-189">pro zařazení pracovní položky do fronty je volána hodnota `IBackgroundTaskQueue.QueueBackgroundWorkItem`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-189">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="e9eef-190">Pracovní položka simuluje dlouho běžící úlohu na pozadí:</span><span class="sxs-lookup"><span data-stu-id="e9eef-190">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="e9eef-191">Spustí se tři prodlevy 5 sekund (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="e9eef-191">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="e9eef-192">Příkaz `try-catch` depeše <xref:System.OperationCanceledException>, pokud se úkol zruší.</span><span class="sxs-lookup"><span data-stu-id="e9eef-192">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="e9eef-193">Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="e9eef-193">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="e9eef-194">Hostovaná služba je registrovaná s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-194">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e9eef-195">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="e9eef-195">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="e9eef-196">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="e9eef-196">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="e9eef-197">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="e9eef-197">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="e9eef-198">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="e9eef-198">Background task that runs on a timer.</span></span>
* <span data-ttu-id="e9eef-199">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="e9eef-199">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="e9eef-200">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="e9eef-200">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="e9eef-201">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="e9eef-201">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="e9eef-202">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e9eef-202">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e9eef-203">Ukázková aplikace je k dispozici ve dvou verzích:</span><span class="sxs-lookup"><span data-stu-id="e9eef-203">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="e9eef-204">Webový hostitel &ndash; webový hostitel je vhodný pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="e9eef-204">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="e9eef-205">Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="e9eef-205">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="e9eef-206">Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .</span><span class="sxs-lookup"><span data-stu-id="e9eef-206">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="e9eef-207">Generic host &ndash; obecný hostitel je v ASP.NET Core 2,1 nový.</span><span class="sxs-lookup"><span data-stu-id="e9eef-207">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="e9eef-208">Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="e9eef-208">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="e9eef-209">Balíček</span><span class="sxs-lookup"><span data-stu-id="e9eef-209">Package</span></span>

<span data-ttu-id="e9eef-210">Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="e9eef-210">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="e9eef-211">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="e9eef-211">IHostedService interface</span></span>

<span data-ttu-id="e9eef-212">Hostované služby implementují rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="e9eef-212">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="e9eef-213">Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="e9eef-213">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="e9eef-214">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku ke spuštění úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-214">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="e9eef-215">Při použití [webového hostitele](xref:fundamentals/host/web-host)je `StartAsync` volána po spuštění serveru a aktivaci [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="e9eef-215">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="e9eef-216">Při použití [obecného hostitele](xref:fundamentals/host/generic-host)se zavolá `StartAsync` před aktivací `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-216">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="e9eef-217">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; spouštěna v případě, že hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-217">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="e9eef-218">`StopAsync` obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-218">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="e9eef-219">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="e9eef-219">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="e9eef-220">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="e9eef-220">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="e9eef-221">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="e9eef-221">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="e9eef-222">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="e9eef-222">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="e9eef-223">Všechny metody, které jsou volány v `StopAsync`, by měly vracet dotaz znovu.</span><span class="sxs-lookup"><span data-stu-id="e9eef-223">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="e9eef-224">Po zrušení se ale úkoly neopustily, protože volající @ no__t-0the očekává, že se všechny úkoly dokončí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-224">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="e9eef-225">Pokud se aplikace neočekávaně ukončí (například proces aplikace nebude úspěšný), `StopAsync` se nemusí volat.</span><span class="sxs-lookup"><span data-stu-id="e9eef-225">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="e9eef-226">Proto se nemusí vyskytovat žádné metody volané nebo operace prováděné v `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-226">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="e9eef-227">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="e9eef-227">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="e9eef-228">Při použití obecného hostitele <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>.</span><span class="sxs-lookup"><span data-stu-id="e9eef-228"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="e9eef-229">Další informace najdete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="e9eef-229">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="e9eef-230">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="e9eef-230">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="e9eef-231">Další informace najdete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="e9eef-231">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="e9eef-232">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e9eef-232">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="e9eef-233">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` by měla být volána i v případě, že není volána `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-233">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="e9eef-234">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="e9eef-234">Timed background tasks</span></span>

<span data-ttu-id="e9eef-235">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="e9eef-235">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="e9eef-236">Časovač aktivuje metodu @no__t 0 úlohy.</span><span class="sxs-lookup"><span data-stu-id="e9eef-236">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="e9eef-237">Časovač je zakázán na `StopAsync` a odstraněn, když je kontejner služby vyřazen v `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-237">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="e9eef-238">Služba je zaregistrovaná v `Startup.ConfigureServices` s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-238">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="e9eef-239">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="e9eef-239">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="e9eef-240">Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `IHostedService`, vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="e9eef-240">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="e9eef-241">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="e9eef-241">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="e9eef-242">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="e9eef-242">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="e9eef-243">V následujícím příkladu je <xref:Microsoft.Extensions.Logging.ILogger> vložen do služby:</span><span class="sxs-lookup"><span data-stu-id="e9eef-243">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="e9eef-244">Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí, aby volala metodu `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-244">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="e9eef-245">Služby jsou zaregistrované v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-245">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e9eef-246">Implementace `IHostedService` je zaregistrována s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-246">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="e9eef-247">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="e9eef-247">Queued background tasks</span></span>

<span data-ttu-id="e9eef-248">Fronta úloh na pozadí je založena na .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánovala být integrovaná pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="e9eef-248">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="e9eef-249">V `QueueHostedService` se úlohy na pozadí ve frontě odřadí a spustí se jako <xref:Microsoft.Extensions.Hosting.BackgroundService>, což je základní třída pro implementaci dlouhého běhu `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-249">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="e9eef-250">Služby jsou zaregistrované v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-250">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e9eef-251">Implementace `IHostedService` je zaregistrována s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-251">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="e9eef-252">Ve třídě modelu stránky indexu:</span><span class="sxs-lookup"><span data-stu-id="e9eef-252">In the Index page model class:</span></span>

* <span data-ttu-id="e9eef-253">@No__t-0 se vloží do konstruktoru a přiřadí se k `Queue`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-253">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="e9eef-254">@No__t-0 je vložen a přiřazeno `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-254">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="e9eef-255">Objekt pro vytváření se používá k vytvoření instancí <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, která se používá k vytváření služeb v rámci oboru.</span><span class="sxs-lookup"><span data-stu-id="e9eef-255">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="e9eef-256">Je vytvořen obor, aby bylo možné použít `AppDbContext` (s [vymezenou službou](xref:fundamentals/dependency-injection#service-lifetimes)) k zápisu záznamů databáze do `IBackgroundTaskQueue` (služba s jedním prvkem).</span><span class="sxs-lookup"><span data-stu-id="e9eef-256">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="e9eef-257">Když je na stránce index vybrané tlačítko **Přidat úlohu** , spustí se metoda `OnPostAddTask`.</span><span class="sxs-lookup"><span data-stu-id="e9eef-257">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="e9eef-258">pro zařazení pracovní položky do fronty je volána hodnota `QueueBackgroundWorkItem`:</span><span class="sxs-lookup"><span data-stu-id="e9eef-258">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e9eef-259">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e9eef-259">Additional resources</span></span>

* [<span data-ttu-id="e9eef-260">Implementace úloh na pozadí v mikroslužbách pomocí IHostedService a třídy BackgroundService</span><span class="sxs-lookup"><span data-stu-id="e9eef-260">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
