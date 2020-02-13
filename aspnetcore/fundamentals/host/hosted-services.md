---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 9b7224c07df027c9466db34dcc23505410893f1f
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171786"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="df2b7-103">Úlohy na pozadí s hostovanými službami v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df2b7-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="df2b7-104">Od [Luke Latham](https://github.com/guardrex) a [Jeow li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="df2b7-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="df2b7-105">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="df2b7-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="df2b7-106">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="df2b7-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="df2b7-107">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="df2b7-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="df2b7-108">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="df2b7-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="df2b7-109">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="df2b7-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="df2b7-110">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="df2b7-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="df2b7-111">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="df2b7-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="df2b7-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="df2b7-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="df2b7-113">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="df2b7-113">Worker Service template</span></span>

<span data-ttu-id="df2b7-114">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="df2b7-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="df2b7-115">Aplikace vytvořená ze šablony pracovní služby určuje sadu SDK pracovního procesu v jeho souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="df2b7-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="df2b7-116">Použití šablony jako základu pro aplikaci hostované služby:</span><span class="sxs-lookup"><span data-stu-id="df2b7-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="df2b7-117">Balíček</span><span class="sxs-lookup"><span data-stu-id="df2b7-117">Package</span></span>

<span data-ttu-id="df2b7-118">Aplikace založená na šabloně pracovní služby používá sadu `Microsoft.NET.Sdk.Worker` SDK a obsahuje explicitní odkaz na balíček na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="df2b7-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="df2b7-119">Podívejte se například na soubor projektu ukázkové aplikace (*BackgroundTasksSample. csproj*).</span><span class="sxs-lookup"><span data-stu-id="df2b7-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="df2b7-120">U webových aplikací, které používají sadu SDK `Microsoft.NET.Sdk.Web`, se na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) odkazuje implicitně ze sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="df2b7-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="df2b7-121">V souboru projektu aplikace není vyžadován explicitní odkaz na balíček.</span><span class="sxs-lookup"><span data-stu-id="df2b7-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="df2b7-122">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="df2b7-122">IHostedService interface</span></span>

<span data-ttu-id="df2b7-123">Rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService> definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="df2b7-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="df2b7-124">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="df2b7-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="df2b7-125">`StartAsync` se volá *před*:</span><span class="sxs-lookup"><span data-stu-id="df2b7-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="df2b7-126">Je nakonfigurovaný kanál pro zpracování požadavků aplikace (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="df2b7-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="df2b7-127">Server se spustí a spustí se [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="df2b7-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="df2b7-128">Výchozí chování se dá změnit tak, aby se `StartAsync` hostované služby spouštěla po konfiguraci kanálu aplikace a volání `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="df2b7-129">Chcete-li změnit výchozí chování, přidejte hostovanou službu (`VideosWatcher` v následujícím příkladu) po volání `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="df2b7-130">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; aktivované, když hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="df2b7-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="df2b7-131">`StopAsync` obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="df2b7-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="df2b7-132">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) k Dispose jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="df2b7-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="df2b7-133">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="df2b7-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="df2b7-134">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="df2b7-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="df2b7-135">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="df2b7-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="df2b7-136">Jakékoli metody, které jsou volány v `StopAsync` by měly vracet dotaz znovu.</span><span class="sxs-lookup"><span data-stu-id="df2b7-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="df2b7-137">Úkoly se ale neruší po zrušení žádosti&mdash;volající čeká na dokončení všech úkolů.</span><span class="sxs-lookup"><span data-stu-id="df2b7-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="df2b7-138">Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="df2b7-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="df2b7-139">Proto nemusí nastat žádné metody, které jsou volány nebo operace prováděné v `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="df2b7-140">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="df2b7-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="df2b7-141">Při použití obecného hostitele <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>.</span><span class="sxs-lookup"><span data-stu-id="df2b7-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="df2b7-142">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="df2b7-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="df2b7-143">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="df2b7-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="df2b7-144">Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="df2b7-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="df2b7-145">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="df2b7-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="df2b7-146">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` by měla být volána i v případě, že `StopAsync` není volána.</span><span class="sxs-lookup"><span data-stu-id="df2b7-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="df2b7-147">Základní třída BackgroundService</span><span class="sxs-lookup"><span data-stu-id="df2b7-147">BackgroundService base class</span></span>

<span data-ttu-id="df2b7-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> je základní třídou pro implementaci dlouhého běžícího <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="df2b7-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="df2b7-149">[Metody ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) se volá za účelem spuštění služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="df2b7-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="df2b7-150">Implementace vrací <xref:System.Threading.Tasks.Task>, která představuje celou dobu života služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="df2b7-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="df2b7-151">Žádné další služby nejsou spuštěny, dokud [metody ExecuteAsync nebude asynchronní](https://github.com/dotnet/extensions/issues/2149), například voláním `await`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="df2b7-152">Vyhněte se provádění dlouhého a blokujícího inicializačního fungování v `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="df2b7-153">Bloky hostitele v [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) čekají na dokončení `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="df2b7-154">Token zrušení se aktivuje, když se zavolá [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) .</span><span class="sxs-lookup"><span data-stu-id="df2b7-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="df2b7-155">Vaše implementace `ExecuteAsync` by měla být dokončena okamžitě při vyvolání tokenu zrušení, aby bylo možné službu řádně vypnout.</span><span class="sxs-lookup"><span data-stu-id="df2b7-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="df2b7-156">V opačném případě se služba nekorektně ukončí v časovém limitu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="df2b7-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="df2b7-157">Další informace najdete v části [rozhraní IHostedService](#ihostedservice-interface) .</span><span class="sxs-lookup"><span data-stu-id="df2b7-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="df2b7-158">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="df2b7-158">Timed background tasks</span></span>

<span data-ttu-id="df2b7-159">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="df2b7-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="df2b7-160">Časovač aktivuje metodu `DoWork` úlohy.</span><span class="sxs-lookup"><span data-stu-id="df2b7-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="df2b7-161">Časovač je zakázán na `StopAsync` a zlikvidován při vyřazení kontejneru služby v `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="df2b7-162"><xref:System.Threading.Timer> nečeká na dokončení předchozích spuštění `DoWork`, takže zobrazený přístup nemusí být vhodný pro každý scénář.</span><span class="sxs-lookup"><span data-stu-id="df2b7-162">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="df2b7-163">[Prolínání. přírůstek](xref:System.Threading.Interlocked.Increment*) se používá ke zvýšení čítače spouštění jako atomické operace, což zajistí, že se více vláken `executionCount` souběžně neaktualizuje.</span><span class="sxs-lookup"><span data-stu-id="df2b7-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="df2b7-164">Služba je zaregistrovaná v `IHostBuilder.ConfigureServices` (*program.cs*) s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-164">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="df2b7-165">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="df2b7-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="df2b7-166">Pokud chcete používat [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci [BackgroundService](#backgroundservice-base-class), vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="df2b7-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="df2b7-167">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="df2b7-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="df2b7-168">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="df2b7-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="df2b7-169">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="df2b7-169">In the following example:</span></span>

* <span data-ttu-id="df2b7-170">Služba je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="df2b7-170">The service is asynchronous.</span></span> <span data-ttu-id="df2b7-171">Metoda `DoWork` vrátí `Task`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-171">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="df2b7-172">Pro demonstrační účely je v metodě `DoWork` očekáváno zpoždění deseti sekund.</span><span class="sxs-lookup"><span data-stu-id="df2b7-172">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="df2b7-173">Do služby se vloží <xref:Microsoft.Extensions.Logging.ILogger>.</span><span class="sxs-lookup"><span data-stu-id="df2b7-173">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="df2b7-174">Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí pro volání metody `DoWork`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-174">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="df2b7-175">`DoWork` vrátí `Task`, která je očekávána v `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-175">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="df2b7-176">Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="df2b7-176">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="df2b7-177">Hostovaná služba je registrovaná s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-177">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="df2b7-178">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="df2b7-178">Queued background tasks</span></span>

<span data-ttu-id="df2b7-179">Fronta úloh na pozadí je založená na <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> .NET 4. x ([nezávazně naplánovaná jako integrovaná pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="df2b7-179">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="df2b7-180">V následujícím příkladu `QueueHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-180">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="df2b7-181">Metoda `BackgroundProcessing` vrátí `Task`, která je očekávána v `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-181">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="df2b7-182">Úlohy na pozadí ve frontě se odřadí a spustí v `BackgroundProcessing`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-182">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="df2b7-183">Pracovní položky jsou očekávány před zastavením služby v `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-183">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="df2b7-184">`MonitorLoop` služba zpracovává úlohy enqueuing pro hostovanou službu vždy, když je na vstupním zařízení vybraná `w` klíč:</span><span class="sxs-lookup"><span data-stu-id="df2b7-184">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="df2b7-185">`IBackgroundTaskQueue` je vložen do služby `MonitorLoop`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-185">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="df2b7-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` je volána pro zařazení pracovní položky do fronty.</span><span class="sxs-lookup"><span data-stu-id="df2b7-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="df2b7-187">Pracovní položka simuluje dlouho běžící úlohu na pozadí:</span><span class="sxs-lookup"><span data-stu-id="df2b7-187">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="df2b7-188">Spustí se tři prodlevy 5 sekund (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="df2b7-188">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="df2b7-189">Příkaz `try-catch` depeše <xref:System.OperationCanceledException>, pokud se úkol zruší.</span><span class="sxs-lookup"><span data-stu-id="df2b7-189">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="df2b7-190">Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="df2b7-190">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="df2b7-191">Hostovaná služba je registrovaná s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-191">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="df2b7-192">`MontiorLoop` se spouští v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-192">`MontiorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="df2b7-193">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="df2b7-193">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="df2b7-194">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="df2b7-194">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="df2b7-195">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="df2b7-195">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="df2b7-196">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="df2b7-196">Background task that runs on a timer.</span></span>
* <span data-ttu-id="df2b7-197">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="df2b7-197">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="df2b7-198">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="df2b7-198">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="df2b7-199">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="df2b7-199">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="df2b7-200">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="df2b7-200">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="df2b7-201">Balíček</span><span class="sxs-lookup"><span data-stu-id="df2b7-201">Package</span></span>

<span data-ttu-id="df2b7-202">Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="df2b7-202">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="df2b7-203">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="df2b7-203">IHostedService interface</span></span>

<span data-ttu-id="df2b7-204">Hostované služby implementují rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="df2b7-204">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="df2b7-205">Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="df2b7-205">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="df2b7-206">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="df2b7-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="df2b7-207">Při použití [webového hostitele](xref:fundamentals/host/web-host)se `StartAsync` volá po spuštění serveru a aktivaci [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="df2b7-207">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="df2b7-208">Při použití [obecného hostitele](xref:fundamentals/host/generic-host)se `StartAsync` zavolá před tím, než se aktivuje `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-208">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="df2b7-209">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; aktivované, když hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="df2b7-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="df2b7-210">`StopAsync` obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="df2b7-210">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="df2b7-211">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) k Dispose jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="df2b7-211">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="df2b7-212">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="df2b7-212">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="df2b7-213">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="df2b7-213">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="df2b7-214">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="df2b7-214">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="df2b7-215">Jakékoli metody, které jsou volány v `StopAsync` by měly vracet dotaz znovu.</span><span class="sxs-lookup"><span data-stu-id="df2b7-215">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="df2b7-216">Úkoly se ale neruší po zrušení žádosti&mdash;volající čeká na dokončení všech úkolů.</span><span class="sxs-lookup"><span data-stu-id="df2b7-216">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="df2b7-217">Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="df2b7-217">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="df2b7-218">Proto nemusí nastat žádné metody, které jsou volány nebo operace prováděné v `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-218">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="df2b7-219">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="df2b7-219">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="df2b7-220">Při použití obecného hostitele <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>.</span><span class="sxs-lookup"><span data-stu-id="df2b7-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="df2b7-221">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="df2b7-221">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="df2b7-222">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="df2b7-222">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="df2b7-223">Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="df2b7-223">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="df2b7-224">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="df2b7-224">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="df2b7-225">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` by měla být volána i v případě, že `StopAsync` není volána.</span><span class="sxs-lookup"><span data-stu-id="df2b7-225">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="df2b7-226">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="df2b7-226">Timed background tasks</span></span>

<span data-ttu-id="df2b7-227">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="df2b7-227">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="df2b7-228">Časovač aktivuje metodu `DoWork` úlohy.</span><span class="sxs-lookup"><span data-stu-id="df2b7-228">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="df2b7-229">Časovač je zakázán na `StopAsync` a zlikvidován při vyřazení kontejneru služby v `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-229">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="df2b7-230"><xref:System.Threading.Timer> nečeká na dokončení předchozích spuštění `DoWork`, takže zobrazený přístup nemusí být vhodný pro každý scénář.</span><span class="sxs-lookup"><span data-stu-id="df2b7-230">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="df2b7-231">Služba je zaregistrovaná v `Startup.ConfigureServices` s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-231">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="df2b7-232">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="df2b7-232">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="df2b7-233">Pokud chcete v rámci `IHostedService`použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) , vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="df2b7-233">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="df2b7-234">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="df2b7-234">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="df2b7-235">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="df2b7-235">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="df2b7-236">V následujícím příkladu je do služby vložena <xref:Microsoft.Extensions.Logging.ILogger>:</span><span class="sxs-lookup"><span data-stu-id="df2b7-236">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="df2b7-237">Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí, aby volala metodu `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-237">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="df2b7-238">Služby jsou zaregistrované v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-238">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="df2b7-239">Implementace `IHostedService` je zaregistrovaná s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-239">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="df2b7-240">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="df2b7-240">Queued background tasks</span></span>

<span data-ttu-id="df2b7-241">Fronta úloh na pozadí je založena na .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako předdefinované pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="df2b7-241">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="df2b7-242">V `QueueHostedService`se úlohy na pozadí ve frontě odřadí a spustí jako [BackgroundService](#backgroundservice-base-class), což je základní třída pro implementaci dlouhotrvající `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-242">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="df2b7-243">Služby jsou zaregistrované v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="df2b7-244">Implementace `IHostedService` je zaregistrovaná s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="df2b7-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="df2b7-245">Ve třídě modelu stránky indexu:</span><span class="sxs-lookup"><span data-stu-id="df2b7-245">In the Index page model class:</span></span>

* <span data-ttu-id="df2b7-246">`IBackgroundTaskQueue` se vloží do konstruktoru a přiřadí se k `Queue`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-246">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="df2b7-247"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> se vloží do `_serviceScopeFactory`a přiřadí se k němu.</span><span class="sxs-lookup"><span data-stu-id="df2b7-247">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="df2b7-248">Továrna slouží k vytváření instancí <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, které slouží k vytváření služeb v rámci oboru.</span><span class="sxs-lookup"><span data-stu-id="df2b7-248">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="df2b7-249">Je vytvořen obor, aby bylo možné použít `AppDbContext` aplikace ( [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes)) k zápisu záznamů databáze v `IBackgroundTaskQueue` (služba s jedním prvkem).</span><span class="sxs-lookup"><span data-stu-id="df2b7-249">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="df2b7-250">Když je na stránce index vybrané tlačítko **Přidat úlohu** , je spuštěna metoda `OnPostAddTask`.</span><span class="sxs-lookup"><span data-stu-id="df2b7-250">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="df2b7-251">`QueueBackgroundWorkItem` je volána pro zařazení pracovní položky do fronty:</span><span class="sxs-lookup"><span data-stu-id="df2b7-251">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="df2b7-252">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="df2b7-252">Additional resources</span></span>

* [<span data-ttu-id="df2b7-253">Implementace úloh na pozadí v mikroslužbách pomocí IHostedService a třídy BackgroundService</span><span class="sxs-lookup"><span data-stu-id="df2b7-253">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="df2b7-254">Spouštění úloh na pozadí pomocí WebJobs v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="df2b7-254">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
