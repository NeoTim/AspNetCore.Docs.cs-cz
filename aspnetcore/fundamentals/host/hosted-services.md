---
<span data-ttu-id="d0d02-101">title: úlohy na pozadí s hostovanými službami v ASP.NET Core autor: Rick-Anderson Popis: Naučte se implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0d02-101">title: Background tasks with hosted services in ASP.NET Core author: rick-anderson description: Learn how to implement background tasks with hosted services in ASP.NET Core.</span></span>
<span data-ttu-id="d0d02-102">monikerRange: ' >= aspnetcore-2,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 02/10/2020 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d0d02-102">monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date: 02/10/2020 no-loc:</span></span>
- <span data-ttu-id="d0d02-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0d02-103">'Blazor'</span></span>
- <span data-ttu-id="d0d02-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0d02-104">'Identity'</span></span>
- <span data-ttu-id="d0d02-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0d02-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0d02-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0d02-106">'Razor'</span></span>
- <span data-ttu-id="d0d02-107">SignalRUID: Základy/hostování/hostované služby</span><span class="sxs-lookup"><span data-stu-id="d0d02-107">'SignalR' uid: fundamentals/host/hosted-services</span></span>

---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="d0d02-108">Úlohy na pozadí s hostovanými službami v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0d02-108">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="d0d02-109">[Jeow li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="d0d02-109">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d0d02-110">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="d0d02-110">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="d0d02-111">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d0d02-111">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d0d02-112">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="d0d02-112">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="d0d02-113">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="d0d02-113">Background task that runs on a timer.</span></span>
* <span data-ttu-id="d0d02-114">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d0d02-114">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d0d02-115">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d0d02-115">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="d0d02-116">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="d0d02-116">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="d0d02-117">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0d02-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="d0d02-118">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="d0d02-118">Worker Service template</span></span>

<span data-ttu-id="d0d02-119">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="d0d02-119">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="d0d02-120">Aplikace vytvořená ze šablony pracovní služby určuje sadu SDK pracovního procesu v jeho souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d0d02-120">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="d0d02-121">Použití šablony jako základu pro aplikaci hostované služby:</span><span class="sxs-lookup"><span data-stu-id="d0d02-121">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="d0d02-122">Balíček</span><span class="sxs-lookup"><span data-stu-id="d0d02-122">Package</span></span>

<span data-ttu-id="d0d02-123">Aplikace založená na šabloně pracovní služby používá `Microsoft.NET.Sdk.Worker` sadu SDK a má explicitní odkaz na balíček na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="d0d02-123">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="d0d02-124">Podívejte se například na soubor projektu ukázkové aplikace (*BackgroundTasksSample. csproj*).</span><span class="sxs-lookup"><span data-stu-id="d0d02-124">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="d0d02-125">U webových aplikací, které používají `Microsoft.NET.Sdk.Web` sadu SDK, se na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) odkazuje implicitně ze sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d0d02-125">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="d0d02-126">V souboru projektu aplikace není vyžadován explicitní odkaz na balíček.</span><span class="sxs-lookup"><span data-stu-id="d0d02-126">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="d0d02-127">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="d0d02-127">IHostedService interface</span></span>

<span data-ttu-id="d0d02-128"><xref:Microsoft.Extensions.Hosting.IHostedService>Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="d0d02-128">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="d0d02-129">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d0d02-129">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="d0d02-130">`StartAsync`se volá *před*:</span><span class="sxs-lookup"><span data-stu-id="d0d02-130">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="d0d02-131">Je nakonfigurovaný kanál zpracování požadavků aplikace ( `Startup.Configure` ).</span><span class="sxs-lookup"><span data-stu-id="d0d02-131">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="d0d02-132">Server se spustí a spustí se [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="d0d02-132">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="d0d02-133">Výchozí chování se dá změnit tak, aby se hostovaná služba `StartAsync` spouštěla po nakonfigurování kanálu aplikace a `ApplicationStarted` zavolala se.</span><span class="sxs-lookup"><span data-stu-id="d0d02-133">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="d0d02-134">Chcete-li změnit výchozí chování, přidejte hostovanou službu ( `VideosWatcher` v následujícím příkladu) po volání `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="d0d02-134">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="d0d02-135">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): aktivovaná v případě, že hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d0d02-135">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="d0d02-136">`StopAsync`obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d0d02-136">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="d0d02-137">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="d0d02-137">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="d0d02-138">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="d0d02-138">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="d0d02-139">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="d0d02-139">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="d0d02-140">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="d0d02-140">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="d0d02-141">Všechny metody, které jsou volány v nástroji, `StopAsync` by měly vracet výzvu.</span><span class="sxs-lookup"><span data-stu-id="d0d02-141">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="d0d02-142">Úkoly se ale po zrušení žádosti zruší, protože &mdash; volající čeká na dokončení všech úkolů.</span><span class="sxs-lookup"><span data-stu-id="d0d02-142">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="d0d02-143">Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="d0d02-143">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="d0d02-144">Proto žádné metody, které jsou volány nebo operací, které jsou prováděny v nástroji, nemusí `StopAsync` nastat.</span><span class="sxs-lookup"><span data-stu-id="d0d02-144">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="d0d02-145">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="d0d02-145">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="d0d02-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Při použití obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="d0d02-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="d0d02-147">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="d0d02-147">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="d0d02-148">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="d0d02-148">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="d0d02-149">Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="d0d02-149">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="d0d02-150">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0d02-150">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="d0d02-151">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` měla by být volána i v případě, že `StopAsync` není volána.</span><span class="sxs-lookup"><span data-stu-id="d0d02-151">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="d0d02-152">Základní třída BackgroundService</span><span class="sxs-lookup"><span data-stu-id="d0d02-152">BackgroundService base class</span></span>

<span data-ttu-id="d0d02-153"><xref:Microsoft.Extensions.Hosting.BackgroundService>je základní třídou pro implementaci dlouhého běhu <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="d0d02-153"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="d0d02-154">[Metody ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) se volá za účelem spuštění služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d0d02-154">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="d0d02-155">Implementace vrátí <xref:System.Threading.Tasks.Task> , která představuje celou dobu života služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d0d02-155">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="d0d02-156">Žádné další služby nejsou spuštěny, dokud [metody ExecuteAsync nebude asynchronní](https://github.com/dotnet/extensions/issues/2149), například voláním `await` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-156">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="d0d02-157">Vyhněte se provádění dlouhého a blokujícího inicializačního fungování v `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-157">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="d0d02-158">Hostitelské bloky v [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) čekají na `ExecuteAsync` dokončení.</span><span class="sxs-lookup"><span data-stu-id="d0d02-158">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="d0d02-159">Token zrušení se aktivuje, když se zavolá [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) .</span><span class="sxs-lookup"><span data-stu-id="d0d02-159">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="d0d02-160">Vaše implementace `ExecuteAsync` by měla být dokončena okamžitě při vyvolání tokenu zrušení, aby bylo možné službu řádně vypnout.</span><span class="sxs-lookup"><span data-stu-id="d0d02-160">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="d0d02-161">V opačném případě se služba nekorektně ukončí v časovém limitu vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d0d02-161">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="d0d02-162">Další informace najdete v části [rozhraní IHostedService](#ihostedservice-interface) .</span><span class="sxs-lookup"><span data-stu-id="d0d02-162">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="d0d02-163">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="d0d02-163">Timed background tasks</span></span>

<span data-ttu-id="d0d02-164">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="d0d02-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="d0d02-165">Časovač aktivuje `DoWork` metodu úkolu.</span><span class="sxs-lookup"><span data-stu-id="d0d02-165">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="d0d02-166">Časovač je zakázán `StopAsync` a vyřazen při vyřazení kontejneru služby `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="d0d02-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="d0d02-167">Nečeká na <xref:System.Threading.Timer> Dokončení předchozích spuštění `DoWork` , takže zobrazený přístup nemusí být vhodný pro každý scénář.</span><span class="sxs-lookup"><span data-stu-id="d0d02-167">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="d0d02-168">[Prolínání. přírůstek](xref:System.Threading.Interlocked.Increment*) se používá ke zvýšení čítače spouštění jako atomické operace, což zajistí, že se více vláken současně neaktualizuje `executionCount` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-168">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="d0d02-169">Služba je registrována v `IHostBuilder.ConfigureServices` (*program.cs*) s `AddHostedService` metodou rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d0d02-169">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="d0d02-170">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="d0d02-170">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="d0d02-171">Pokud chcete používat [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci [BackgroundService](#backgroundservice-base-class), vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="d0d02-171">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="d0d02-172">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="d0d02-172">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="d0d02-173">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d0d02-173">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="d0d02-174">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d0d02-174">In the following example:</span></span>

* <span data-ttu-id="d0d02-175">Služba je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="d0d02-175">The service is asynchronous.</span></span> <span data-ttu-id="d0d02-176">`DoWork`Metoda vrátí `Task` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-176">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="d0d02-177">Pro demonstrační účely je v metodě očekáváno zpoždění deseti sekund `DoWork` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-177">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="d0d02-178"><xref:Microsoft.Extensions.Logging.ILogger>Do služby se vloží.</span><span class="sxs-lookup"><span data-stu-id="d0d02-178">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="d0d02-179">Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí pro volání `DoWork` metody.</span><span class="sxs-lookup"><span data-stu-id="d0d02-179">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="d0d02-180">`DoWork`Vrátí `Task` , který je očekáván v `ExecuteAsync` :</span><span class="sxs-lookup"><span data-stu-id="d0d02-180">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="d0d02-181">Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="d0d02-181">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="d0d02-182">Hostovaná služba je zaregistrovaná v rámci `AddHostedService` metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d0d02-182">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="d0d02-183">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="d0d02-183">Queued background tasks</span></span>

<span data-ttu-id="d0d02-184">Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> :</span><span class="sxs-lookup"><span data-stu-id="d0d02-184">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="d0d02-185">V následujícím `QueueHostedService` příkladu:</span><span class="sxs-lookup"><span data-stu-id="d0d02-185">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="d0d02-186">`BackgroundProcessing`Metoda vrátí hodnotu `Task` , která je očekávána v `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-186">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="d0d02-187">Úlohy na pozadí ve frontě se odřadí a spustí v `BackgroundProcessing` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-187">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="d0d02-188">Pracovní položky jsou očekávány před zastavením služby `StopAsync` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-188">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="d0d02-189">`MonitorLoop`Služba zpracovává enqueuing úlohy pro hostovanou službu vždy, když `w` je vybraný klíč na vstupním zařízení:</span><span class="sxs-lookup"><span data-stu-id="d0d02-189">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="d0d02-190">`IBackgroundTaskQueue`Je vložen do `MonitorLoop` služby.</span><span class="sxs-lookup"><span data-stu-id="d0d02-190">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="d0d02-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem`je volána pro zařazení pracovní položky do fronty.</span><span class="sxs-lookup"><span data-stu-id="d0d02-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="d0d02-192">Pracovní položka simuluje dlouho běžící úlohu na pozadí:</span><span class="sxs-lookup"><span data-stu-id="d0d02-192">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="d0d02-193">Spustí se tři prodlevy 5 sekund ( `Task.Delay` ).</span><span class="sxs-lookup"><span data-stu-id="d0d02-193">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="d0d02-194">`try-catch`Příkaz zachytávání, <xref:System.OperationCanceledException> Pokud je úkol zrušen.</span><span class="sxs-lookup"><span data-stu-id="d0d02-194">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="d0d02-195">Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="d0d02-195">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="d0d02-196">Hostovaná služba je zaregistrovaná v rámci `AddHostedService` metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d0d02-196">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="d0d02-197">`MonitorLoop`je spuštěn v `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="d0d02-197">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d0d02-198">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="d0d02-198">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="d0d02-199">Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d0d02-199">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d0d02-200">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="d0d02-200">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="d0d02-201">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="d0d02-201">Background task that runs on a timer.</span></span>
* <span data-ttu-id="d0d02-202">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d0d02-202">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d0d02-203">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="d0d02-203">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="d0d02-204">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="d0d02-204">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="d0d02-205">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0d02-205">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="d0d02-206">Balíček</span><span class="sxs-lookup"><span data-stu-id="d0d02-206">Package</span></span>

<span data-ttu-id="d0d02-207">Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="d0d02-207">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="d0d02-208">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="d0d02-208">IHostedService interface</span></span>

<span data-ttu-id="d0d02-209">Hostované služby implementují <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d0d02-209">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d0d02-210">Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="d0d02-210">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="d0d02-211">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d0d02-211">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="d0d02-212">Při použití [webového hostitele](xref:fundamentals/host/web-host) `StartAsync` se volá po spuštění serveru a aktivaci [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="d0d02-212">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="d0d02-213">Při použití [obecného hostitele](xref:fundamentals/host/generic-host) `StartAsync` se před `ApplicationStarted` aktivací volá.</span><span class="sxs-lookup"><span data-stu-id="d0d02-213">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="d0d02-214">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): aktivovaná v případě, že hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d0d02-214">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="d0d02-215">`StopAsync`obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d0d02-215">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="d0d02-216">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="d0d02-216">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="d0d02-217">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="d0d02-217">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="d0d02-218">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="d0d02-218">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="d0d02-219">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="d0d02-219">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="d0d02-220">Všechny metody, které jsou volány v nástroji, `StopAsync` by měly vracet výzvu.</span><span class="sxs-lookup"><span data-stu-id="d0d02-220">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="d0d02-221">Úkoly se ale po zrušení žádosti zruší, protože &mdash; volající čeká na dokončení všech úkolů.</span><span class="sxs-lookup"><span data-stu-id="d0d02-221">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="d0d02-222">Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="d0d02-222">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="d0d02-223">Proto žádné metody, které jsou volány nebo operací, které jsou prováděny v nástroji, nemusí `StopAsync` nastat.</span><span class="sxs-lookup"><span data-stu-id="d0d02-223">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="d0d02-224">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="d0d02-224">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="d0d02-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Při použití obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="d0d02-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="d0d02-226">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="d0d02-226">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="d0d02-227">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="d0d02-227">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="d0d02-228">Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="d0d02-228">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="d0d02-229">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0d02-229">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="d0d02-230">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` měla by být volána i v případě, že `StopAsync` není volána.</span><span class="sxs-lookup"><span data-stu-id="d0d02-230">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="d0d02-231">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="d0d02-231">Timed background tasks</span></span>

<span data-ttu-id="d0d02-232">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="d0d02-232">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="d0d02-233">Časovač aktivuje `DoWork` metodu úkolu.</span><span class="sxs-lookup"><span data-stu-id="d0d02-233">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="d0d02-234">Časovač je zakázán `StopAsync` a vyřazen při vyřazení kontejneru služby `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="d0d02-234">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="d0d02-235">Nečeká na <xref:System.Threading.Timer> Dokončení předchozích spuštění `DoWork` , takže zobrazený přístup nemusí být vhodný pro každý scénář.</span><span class="sxs-lookup"><span data-stu-id="d0d02-235">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="d0d02-236">Služba je zaregistrovaná v `Startup.ConfigureServices` s `AddHostedService` metodou rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d0d02-236">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="d0d02-237">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="d0d02-237">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="d0d02-238">Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `IHostedService` , vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="d0d02-238">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="d0d02-239">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="d0d02-239">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="d0d02-240">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d0d02-240">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="d0d02-241">V následujícím příkladu je vložena <xref:Microsoft.Extensions.Logging.ILogger> do služby:</span><span class="sxs-lookup"><span data-stu-id="d0d02-241">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="d0d02-242">Hostovaná služba vytvoří obor pro řešení služby úlohy na pozadí, aby volal jeho `DoWork` metodu:</span><span class="sxs-lookup"><span data-stu-id="d0d02-242">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="d0d02-243">Služby jsou zaregistrované v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d0d02-244">`IHostedService`Implementace je registrována s `AddHostedService` metodou rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d0d02-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="d0d02-245">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="d0d02-245">Queued background tasks</span></span>

<span data-ttu-id="d0d02-246">Fronta úloh na pozadí je založena na .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánovaná jako integrovaná pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="d0d02-246">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="d0d02-247">V nástroji se `QueueHostedService` úlohy na pozadí ve frontě odřadí a spustí jako [BackgroundService](#backgroundservice-base-class), což je základní třída pro implementaci dlouhého běhu `IHostedService` :</span><span class="sxs-lookup"><span data-stu-id="d0d02-247">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="d0d02-248">Služby jsou zaregistrované v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-248">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d0d02-249">`IHostedService`Implementace je registrována s `AddHostedService` metodou rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d0d02-249">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="d0d02-250">Ve třídě modelu stránky indexu:</span><span class="sxs-lookup"><span data-stu-id="d0d02-250">In the Index page model class:</span></span>

* <span data-ttu-id="d0d02-251">`IBackgroundTaskQueue`Je vložen do konstruktoru a přiřazen k `Queue` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-251">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="d0d02-252"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>Je vložený a přiřazený k `_serviceScopeFactory` .</span><span class="sxs-lookup"><span data-stu-id="d0d02-252">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="d0d02-253">Objekt pro vytváření se používá k vytvoření instancí <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> , které se používají k vytváření služeb v rámci oboru.</span><span class="sxs-lookup"><span data-stu-id="d0d02-253">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="d0d02-254">Je vytvořen obor, aby bylo možné použít `AppDbContext` ( [vymezenou](xref:fundamentals/dependency-injection#service-lifetimes)) aplikaci k zápisu záznamů databáze do `IBackgroundTaskQueue` služby (singleton).</span><span class="sxs-lookup"><span data-stu-id="d0d02-254">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="d0d02-255">Je-li na stránce indexu vybráno tlačítko **Přidat úkol** , `OnPostAddTask` je metoda spuštěna.</span><span class="sxs-lookup"><span data-stu-id="d0d02-255">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="d0d02-256">`QueueBackgroundWorkItem`je volána pro zařazení pracovní položky do fronty:</span><span class="sxs-lookup"><span data-stu-id="d0d02-256">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d0d02-257">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d0d02-257">Additional resources</span></span>

* [<span data-ttu-id="d0d02-258">Implementace úloh na pozadí v mikroslužbách pomocí IHostedService a třídy BackgroundService</span><span class="sxs-lookup"><span data-stu-id="d0d02-258">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="d0d02-259">Spouštění úloh na pozadí pomocí WebJobs v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d0d02-259">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
