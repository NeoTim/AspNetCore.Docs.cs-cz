---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/19/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: da3c2679005714a3d82de94cf3bc3c809aa3500d
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239722"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="644ef-103">Úlohy na pozadí s hostovanými službami v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="644ef-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="644ef-104">Od [Luke Latham](https://github.com/guardrex) a [Jeow li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="644ef-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="644ef-105">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="644ef-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="644ef-106">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="644ef-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="644ef-107">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="644ef-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="644ef-108">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="644ef-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="644ef-109">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="644ef-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="644ef-110">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="644ef-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="644ef-111">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="644ef-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="644ef-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="644ef-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="644ef-113">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="644ef-113">Worker Service template</span></span>

<span data-ttu-id="644ef-114">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="644ef-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="644ef-115">Aplikace vytvořená ze šablony pracovní služby určuje sadu SDK pracovního procesu v jeho souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="644ef-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="644ef-116">Použití šablony jako základu pro aplikaci hostované služby:</span><span class="sxs-lookup"><span data-stu-id="644ef-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="644ef-117">Balíček</span><span class="sxs-lookup"><span data-stu-id="644ef-117">Package</span></span>

<span data-ttu-id="644ef-118">Aplikace založená na šabloně pracovní služby používá sadu `Microsoft.NET.Sdk.Worker` SDK a obsahuje explicitní odkaz na balíček na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="644ef-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="644ef-119">Podívejte se například na soubor projektu ukázkové aplikace (*BackgroundTasksSample. csproj*).</span><span class="sxs-lookup"><span data-stu-id="644ef-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="644ef-120">U webových aplikací, které používají sadu SDK `Microsoft.NET.Sdk.Web`, se na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) odkazuje implicitně ze sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="644ef-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="644ef-121">V souboru projektu aplikace není vyžadován explicitní odkaz na balíček.</span><span class="sxs-lookup"><span data-stu-id="644ef-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="644ef-122">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="644ef-122">IHostedService interface</span></span>

<span data-ttu-id="644ef-123">Rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService> definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="644ef-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="644ef-124">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="644ef-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="644ef-125">`StartAsync` se volá *před*:</span><span class="sxs-lookup"><span data-stu-id="644ef-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="644ef-126">Je nakonfigurovaný kanál pro zpracování požadavků aplikace (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="644ef-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="644ef-127">Server se spustí a spustí se [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="644ef-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="644ef-128">Výchozí chování se dá změnit tak, aby se `StartAsync` hostované služby spouštěla po konfiguraci kanálu aplikace a volání `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="644ef-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="644ef-129">Chcete-li změnit výchozí chování, přidejte hostovanou službu (`VideosWatcher` v následujícím příkladu) po volání `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="644ef-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="644ef-130">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; aktivované, když hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="644ef-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="644ef-131">`StopAsync` obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="644ef-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="644ef-132">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) k Dispose jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="644ef-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="644ef-133">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="644ef-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="644ef-134">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="644ef-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="644ef-135">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="644ef-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="644ef-136">Jakékoli metody, které jsou volány v `StopAsync` by měly vracet dotaz znovu.</span><span class="sxs-lookup"><span data-stu-id="644ef-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="644ef-137">Úkoly se ale neruší po zrušení žádosti&mdash;volající čeká na dokončení všech úkolů.</span><span class="sxs-lookup"><span data-stu-id="644ef-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="644ef-138">Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="644ef-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="644ef-139">Proto nemusí nastat žádné metody, které jsou volány nebo operace prováděné v `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="644ef-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="644ef-140">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="644ef-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="644ef-141">Při použití obecného hostitele <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>.</span><span class="sxs-lookup"><span data-stu-id="644ef-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="644ef-142">Další informace najdete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="644ef-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="644ef-143">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="644ef-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="644ef-144">Další informace najdete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="644ef-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="644ef-145">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="644ef-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="644ef-146">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` by měla být volána i v případě, že `StopAsync` není volána.</span><span class="sxs-lookup"><span data-stu-id="644ef-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="644ef-147">Základní třída BackgroundService</span><span class="sxs-lookup"><span data-stu-id="644ef-147">BackgroundService base class</span></span>

<span data-ttu-id="644ef-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> je základní třídou pro implementaci dlouhého běžícího <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="644ef-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="644ef-149">[Metody ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) se volá za účelem spuštění služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="644ef-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="644ef-150">Implementace vrací <xref:System.Threading.Tasks.Task>, která představuje celou dobu života služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="644ef-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="644ef-151">Žádné další služby nejsou spuštěny, dokud [metody ExecuteAsync nebude asynchronní](https://github.com/aspnet/Extensions/issues/2149), například voláním `await`.</span><span class="sxs-lookup"><span data-stu-id="644ef-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/aspnet/Extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="644ef-152">Vyhněte se provádění dlouhého a blokujícího inicializačního fungování v `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="644ef-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="644ef-153">Bloky hostitele v [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) čekají na dokončení `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="644ef-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="644ef-154">Token zrušení se aktivuje, když se zavolá [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) .</span><span class="sxs-lookup"><span data-stu-id="644ef-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="644ef-155">Vaše implementace `ExecuteAsync` by měla být dokončena okamžitě při vyvolání tokenu zrušení, aby bylo možné službu řádně vypnout.</span><span class="sxs-lookup"><span data-stu-id="644ef-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="644ef-156">V opačném případě se služba nekorektně ukončí v časovém limitu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="644ef-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="644ef-157">Další informace najdete v části [rozhraní IHostedService](#ihostedservice-interface) .</span><span class="sxs-lookup"><span data-stu-id="644ef-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="644ef-158">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="644ef-158">Timed background tasks</span></span>

<span data-ttu-id="644ef-159">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="644ef-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="644ef-160">Časovač aktivuje metodu `DoWork` úlohy.</span><span class="sxs-lookup"><span data-stu-id="644ef-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="644ef-161">Časovač je zakázán na `StopAsync` a zlikvidován při vyřazení kontejneru služby v `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="644ef-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="644ef-162">Služba je zaregistrovaná v `IHostBuilder.ConfigureServices` (*program.cs*) s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="644ef-162">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="644ef-163">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="644ef-163">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="644ef-164">Pokud chcete používat [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci [BackgroundService](#backgroundservice-base-class), vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="644ef-164">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="644ef-165">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="644ef-165">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="644ef-166">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="644ef-166">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="644ef-167">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="644ef-167">In the following example:</span></span>

* <span data-ttu-id="644ef-168">Služba je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="644ef-168">The service is asynchronous.</span></span> <span data-ttu-id="644ef-169">Metoda `DoWork` vrátí `Task`.</span><span class="sxs-lookup"><span data-stu-id="644ef-169">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="644ef-170">Pro demonstrační účely je v metodě `DoWork` očekáváno zpoždění deseti sekund.</span><span class="sxs-lookup"><span data-stu-id="644ef-170">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="644ef-171">Do služby se vloží <xref:Microsoft.Extensions.Logging.ILogger>.</span><span class="sxs-lookup"><span data-stu-id="644ef-171">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="644ef-172">Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí pro volání metody `DoWork`.</span><span class="sxs-lookup"><span data-stu-id="644ef-172">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="644ef-173">`DoWork` vrátí `Task`, která je očekávána v `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="644ef-173">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="644ef-174">Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="644ef-174">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="644ef-175">Hostovaná služba je registrovaná s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="644ef-175">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="644ef-176">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="644ef-176">Queued background tasks</span></span>

<span data-ttu-id="644ef-177">Fronta úloh na pozadí je založená na <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> .NET 4. x ([nezávazně naplánovaná jako integrovaná pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="644ef-177">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="644ef-178">V následujícím příkladu `QueueHostedService`:</span><span class="sxs-lookup"><span data-stu-id="644ef-178">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="644ef-179">Metoda `BackgroundProcessing` vrátí `Task`, která je očekávána v `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="644ef-179">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="644ef-180">Úlohy na pozadí ve frontě se odřadí a spustí v `BackgroundProcessing`.</span><span class="sxs-lookup"><span data-stu-id="644ef-180">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="644ef-181">Pracovní položky jsou očekávány před zastavením služby v `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="644ef-181">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="644ef-182">`MonitorLoop` služba zpracovává úlohy enqueuing pro hostovanou službu vždy, když je na vstupním zařízení vybraná `w` klíč:</span><span class="sxs-lookup"><span data-stu-id="644ef-182">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="644ef-183">`IBackgroundTaskQueue` je vložen do služby `MonitorLoop`.</span><span class="sxs-lookup"><span data-stu-id="644ef-183">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="644ef-184">`IBackgroundTaskQueue.QueueBackgroundWorkItem` je volána pro zařazení pracovní položky do fronty.</span><span class="sxs-lookup"><span data-stu-id="644ef-184">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="644ef-185">Pracovní položka simuluje dlouho běžící úlohu na pozadí:</span><span class="sxs-lookup"><span data-stu-id="644ef-185">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="644ef-186">Spustí se tři prodlevy 5 sekund (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="644ef-186">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="644ef-187">Příkaz `try-catch` depeše <xref:System.OperationCanceledException>, pokud se úkol zruší.</span><span class="sxs-lookup"><span data-stu-id="644ef-187">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="644ef-188">Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="644ef-188">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="644ef-189">Hostovaná služba je registrovaná s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="644ef-189">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="644ef-190">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="644ef-190">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="644ef-191">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="644ef-191">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="644ef-192">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="644ef-192">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="644ef-193">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="644ef-193">Background task that runs on a timer.</span></span>
* <span data-ttu-id="644ef-194">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="644ef-194">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="644ef-195">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="644ef-195">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="644ef-196">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="644ef-196">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="644ef-197">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="644ef-197">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="644ef-198">Balíček</span><span class="sxs-lookup"><span data-stu-id="644ef-198">Package</span></span>

<span data-ttu-id="644ef-199">Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="644ef-199">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="644ef-200">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="644ef-200">IHostedService interface</span></span>

<span data-ttu-id="644ef-201">Hostované služby implementují rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="644ef-201">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="644ef-202">Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="644ef-202">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="644ef-203">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="644ef-203">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="644ef-204">Při použití [webového hostitele](xref:fundamentals/host/web-host)se `StartAsync` volá po spuštění serveru a aktivaci [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="644ef-204">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="644ef-205">Při použití [obecného hostitele](xref:fundamentals/host/generic-host)se `StartAsync` zavolá před tím, než se aktivuje `ApplicationStarted`.</span><span class="sxs-lookup"><span data-stu-id="644ef-205">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="644ef-206">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; aktivované, když hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="644ef-206">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="644ef-207">`StopAsync` obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="644ef-207">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="644ef-208">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) k Dispose jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="644ef-208">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="644ef-209">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="644ef-209">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="644ef-210">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="644ef-210">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="644ef-211">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="644ef-211">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="644ef-212">Jakékoli metody, které jsou volány v `StopAsync` by měly vracet dotaz znovu.</span><span class="sxs-lookup"><span data-stu-id="644ef-212">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="644ef-213">Úkoly se ale neruší po zrušení žádosti&mdash;volající čeká na dokončení všech úkolů.</span><span class="sxs-lookup"><span data-stu-id="644ef-213">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="644ef-214">Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="644ef-214">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="644ef-215">Proto nemusí nastat žádné metody, které jsou volány nebo operace prováděné v `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="644ef-215">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="644ef-216">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="644ef-216">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="644ef-217">Při použití obecného hostitele <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>.</span><span class="sxs-lookup"><span data-stu-id="644ef-217"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="644ef-218">Další informace najdete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="644ef-218">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="644ef-219">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="644ef-219">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="644ef-220">Další informace najdete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="644ef-220">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="644ef-221">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="644ef-221">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="644ef-222">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` by měla být volána i v případě, že `StopAsync` není volána.</span><span class="sxs-lookup"><span data-stu-id="644ef-222">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="644ef-223">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="644ef-223">Timed background tasks</span></span>

<span data-ttu-id="644ef-224">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="644ef-224">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="644ef-225">Časovač aktivuje metodu `DoWork` úlohy.</span><span class="sxs-lookup"><span data-stu-id="644ef-225">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="644ef-226">Časovač je zakázán na `StopAsync` a zlikvidován při vyřazení kontejneru služby v `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="644ef-226">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="644ef-227">Služba je zaregistrovaná v `Startup.ConfigureServices` s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="644ef-227">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="644ef-228">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="644ef-228">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="644ef-229">Pokud chcete v rámci `IHostedService`použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) , vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="644ef-229">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="644ef-230">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="644ef-230">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="644ef-231">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="644ef-231">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="644ef-232">V následujícím příkladu je do služby vložena <xref:Microsoft.Extensions.Logging.ILogger>:</span><span class="sxs-lookup"><span data-stu-id="644ef-232">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="644ef-233">Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí, aby volala metodu `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="644ef-233">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="644ef-234">Služby jsou zaregistrované v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="644ef-234">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="644ef-235">Implementace `IHostedService` je zaregistrovaná s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="644ef-235">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="644ef-236">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="644ef-236">Queued background tasks</span></span>

<span data-ttu-id="644ef-237">Fronta úloh na pozadí je založena na .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako předdefinované pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="644ef-237">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="644ef-238">V `QueueHostedService`se úlohy na pozadí ve frontě odřadí a spustí jako [BackgroundService](#backgroundservice-base-class), což je základní třída pro implementaci dlouhotrvající `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="644ef-238">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="644ef-239">Služby jsou zaregistrované v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="644ef-239">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="644ef-240">Implementace `IHostedService` je zaregistrovaná s metodou rozšíření `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="644ef-240">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="644ef-241">Ve třídě modelu stránky indexu:</span><span class="sxs-lookup"><span data-stu-id="644ef-241">In the Index page model class:</span></span>

* <span data-ttu-id="644ef-242">`IBackgroundTaskQueue` se vloží do konstruktoru a přiřadí se k `Queue`.</span><span class="sxs-lookup"><span data-stu-id="644ef-242">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="644ef-243"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> se vloží do `_serviceScopeFactory`a přiřadí se k němu.</span><span class="sxs-lookup"><span data-stu-id="644ef-243">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="644ef-244">Továrna slouží k vytváření instancí <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, které slouží k vytváření služeb v rámci oboru.</span><span class="sxs-lookup"><span data-stu-id="644ef-244">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="644ef-245">Je vytvořen obor, aby bylo možné použít `AppDbContext` aplikace ( [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes)) k zápisu záznamů databáze v `IBackgroundTaskQueue` (služba s jedním prvkem).</span><span class="sxs-lookup"><span data-stu-id="644ef-245">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="644ef-246">Když je na stránce index vybrané tlačítko **Přidat úlohu** , je spuštěna metoda `OnPostAddTask`.</span><span class="sxs-lookup"><span data-stu-id="644ef-246">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="644ef-247">`QueueBackgroundWorkItem` je volána pro zařazení pracovní položky do fronty:</span><span class="sxs-lookup"><span data-stu-id="644ef-247">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="644ef-248">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="644ef-248">Additional resources</span></span>

* [<span data-ttu-id="644ef-249">Implementace úloh na pozadí v mikroslužbách pomocí IHostedService a třídy BackgroundService</span><span class="sxs-lookup"><span data-stu-id="644ef-249">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
