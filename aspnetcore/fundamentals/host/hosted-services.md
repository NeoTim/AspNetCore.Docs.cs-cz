---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 5a29952c4e50edb953fa03c6ea1a1ae27b728bb0
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317724"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="0149d-103">Úlohy na pozadí s hostovanými službami v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0149d-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="0149d-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0149d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0149d-105">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="0149d-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="0149d-106">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="0149d-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="0149d-107">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="0149d-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="0149d-108">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="0149d-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="0149d-109">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="0149d-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="0149d-110">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0149d-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="0149d-111">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="0149d-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="0149d-112">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0149d-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0149d-113">Ukázková aplikace je k dispozici ve dvou verzích:</span><span class="sxs-lookup"><span data-stu-id="0149d-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="0149d-114">Webový hostitel &ndash; webového hostitele je vhodný pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="0149d-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="0149d-115">Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="0149d-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="0149d-116">Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .</span><span class="sxs-lookup"><span data-stu-id="0149d-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="0149d-117">Obecný hostitel &ndash; obecného hostitele je v ASP.NET Core 2,1 nový.</span><span class="sxs-lookup"><span data-stu-id="0149d-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="0149d-118">Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="0149d-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="0149d-119">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="0149d-119">Worker Service template</span></span>

<span data-ttu-id="0149d-120">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="0149d-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="0149d-121">Použití šablony jako základu pro aplikaci hostované služby:</span><span class="sxs-lookup"><span data-stu-id="0149d-121">To use the template as a basis for a hosted services app:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0149d-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0149d-122">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0149d-123">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="0149d-123">Create a new project.</span></span>
1. <span data-ttu-id="0149d-124">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0149d-124">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="0149d-125">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="0149d-125">Select **Next**.</span></span>
1. <span data-ttu-id="0149d-126">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="0149d-126">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="0149d-127">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="0149d-127">Select **Create**.</span></span>
1. <span data-ttu-id="0149d-128">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="0149d-128">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="0149d-129">Vyberte šablonu **služby pracovního procesu** .</span><span class="sxs-lookup"><span data-stu-id="0149d-129">Select the **Worker Service** template.</span></span> <span data-ttu-id="0149d-130">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="0149d-130">Select **Create**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0149d-131">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0149d-131">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0149d-132">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="0149d-132">Create a new project.</span></span>
1. <span data-ttu-id="0149d-133">Na bočním panelu vyberte **aplikaci** v části **.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="0149d-133">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="0149d-134">V části **ASP.NET Core**vyberte **Worker** .</span><span class="sxs-lookup"><span data-stu-id="0149d-134">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="0149d-135">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="0149d-135">Select **Next**.</span></span>
1. <span data-ttu-id="0149d-136">Pro **cílovou architekturu**vyberte **.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="0149d-136">Select **.NET Core 3.0** for the **Target Framework**.</span></span> <span data-ttu-id="0149d-137">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="0149d-137">Select **Next**.</span></span>
1. <span data-ttu-id="0149d-138">Do pole **název projektu** zadejte název.</span><span class="sxs-lookup"><span data-stu-id="0149d-138">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="0149d-139">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="0149d-139">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0149d-140">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="0149d-140">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0149d-141">Použijte šablonu Worker Service`worker`() s příkazem [dotnet New](/dotnet/core/tools/dotnet-new) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="0149d-141">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="0149d-142">V následujícím příkladu se vytvoří aplikace pracovní služby s názvem `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="0149d-142">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="0149d-143">Složka pro `ContosoWorker` aplikaci se vytvoří automaticky při spuštění příkazu.</span><span class="sxs-lookup"><span data-stu-id="0149d-143">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---

## <a name="package"></a><span data-ttu-id="0149d-144">Balíček</span><span class="sxs-lookup"><span data-stu-id="0149d-144">Package</span></span>

<span data-ttu-id="0149d-145">Odkaz na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) se přidá implicitně pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="0149d-145">A package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is added implicitly for ASP.NET Core apps.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="0149d-146">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="0149d-146">IHostedService interface</span></span>

<span data-ttu-id="0149d-147"><xref:Microsoft.Extensions.Hosting.IHostedService> Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="0149d-147">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="0149d-148">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; obsahujelogikupro`StartAsync` zahájení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="0149d-148">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="0149d-149">`StartAsync`se volá *před*:</span><span class="sxs-lookup"><span data-stu-id="0149d-149">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="0149d-150">Je nakonfigurovaný kanál zpracování požadavků aplikace (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="0149d-150">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="0149d-151">Server se spustí a spustí se [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="0149d-151">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="0149d-152">Výchozí chování se dá změnit tak, aby se hostovaná služba `StartAsync` spouštěla po nakonfigurování kanálu aplikace a `ApplicationStarted` zavolala se.</span><span class="sxs-lookup"><span data-stu-id="0149d-152">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="0149d-153">Chcete-li změnit výchozí chování, přidejte hostovanou službu`VideosWatcher` (v následujícím příkladu) po volání `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="0149d-153">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="0149d-154">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Aktivováno, když hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0149d-154">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="0149d-155">`StopAsync`obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="0149d-155">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="0149d-156">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="0149d-156">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="0149d-157">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="0149d-157">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="0149d-158">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="0149d-158">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="0149d-159">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="0149d-159">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="0149d-160">Všechny metody, které `StopAsync` jsou volány v nástroji, by měly vracet výzvu.</span><span class="sxs-lookup"><span data-stu-id="0149d-160">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="0149d-161">Úkoly se ale po zrušení žádosti&mdash;zruší, protože volající čeká na dokončení všech úkolů.</span><span class="sxs-lookup"><span data-stu-id="0149d-161">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="0149d-162">Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="0149d-162">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="0149d-163">Proto žádné metody, které jsou volány nebo `StopAsync` operací, které jsou prováděny v nástroji, nemusí nastat.</span><span class="sxs-lookup"><span data-stu-id="0149d-163">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="0149d-164">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="0149d-164">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="0149d-165"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Při použití obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="0149d-165"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="0149d-166">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="0149d-166">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="0149d-167">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="0149d-167">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="0149d-168">Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="0149d-168">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="0149d-169">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="0149d-169">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="0149d-170">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` měla by být volána i `StopAsync` v případě, že není volána.</span><span class="sxs-lookup"><span data-stu-id="0149d-170">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="0149d-171">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="0149d-171">BackgroundService</span></span>

<span data-ttu-id="0149d-172">`BackgroundService`je základní třídou pro implementaci dlouhého běhu <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="0149d-172">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="0149d-173">`BackgroundService``ExecuteAsync(CancellationToken stoppingToken)` poskytuje abstraktní metodu, která bude obsahovat logiku služby.</span><span class="sxs-lookup"><span data-stu-id="0149d-173">`BackgroundService` provides the `ExecuteAsync(CancellationToken stoppingToken)` abstract method to contain the service's logic.</span></span> <span data-ttu-id="0149d-174">Je `stoppingToken` aktivována, když je volána metoda [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) .</span><span class="sxs-lookup"><span data-stu-id="0149d-174">The `stoppingToken` is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="0149d-175">Implementace této metody vrátí `Task` , která představuje celou dobu života služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="0149d-175">The implementation of this method returns a `Task` that represents the entire lifetime of the background service.</span></span>

<span data-ttu-id="0149d-176">*Volitelně můžete* také přepsat metody definované v `IHostedService` pro spuštění spouštěcího a ukončovacího kódu pro vaši službu:</span><span class="sxs-lookup"><span data-stu-id="0149d-176">In addition, *optionally* override the methods defined on `IHostedService` to run startup and shutdown code for your service:</span></span>

* <span data-ttu-id="0149d-177">`StopAsync(CancellationToken cancellationToken)`&ndash; sevolá,kdyžhostitel`StopAsync` aplikace provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0149d-177">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` is called when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="0149d-178">Je `cancellationToken` správcem signalizována, když se hostitel rozhodne vynutit ukončení služby.</span><span class="sxs-lookup"><span data-stu-id="0149d-178">The `cancellationToken` is signalled when the host decides to forcibly terminate the service.</span></span> <span data-ttu-id="0149d-179">Pokud je tato metoda přepsána, je **nutné** zavolat `await`(a) metodu základní třídy, aby se služba správně vypnula.</span><span class="sxs-lookup"><span data-stu-id="0149d-179">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service shuts down properly.</span></span>
* <span data-ttu-id="0149d-180">`StartAsync(CancellationToken cancellationToken)`&ndash; sevoláke`StartAsync` spuštění služby na pozadí.</span><span class="sxs-lookup"><span data-stu-id="0149d-180">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` is called to start the background service.</span></span> <span data-ttu-id="0149d-181">`cancellationToken` Je správcem signalizována, pokud je proces spuštění přerušený.</span><span class="sxs-lookup"><span data-stu-id="0149d-181">The `cancellationToken` is signalled if the startup process is interrupted.</span></span> <span data-ttu-id="0149d-182">Implementace vrátí `Task` , který představuje proces spuštění služby.</span><span class="sxs-lookup"><span data-stu-id="0149d-182">The implementation returns a `Task` that represents the startup process for the service.</span></span> <span data-ttu-id="0149d-183">Žádné další služby nejsou spuštěny, `Task` dokud se nedokončí.</span><span class="sxs-lookup"><span data-stu-id="0149d-183">No further services are started until this `Task` completes.</span></span> <span data-ttu-id="0149d-184">Pokud je tato metoda přepsána, je **nutné** zavolat `await`(a) metodu základní třídy, aby bylo zajištěno, že služba bude spuštěna správně.</span><span class="sxs-lookup"><span data-stu-id="0149d-184">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service starts properly.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="0149d-185">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="0149d-185">Timed background tasks</span></span>

<span data-ttu-id="0149d-186">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="0149d-186">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="0149d-187">Časovač aktivuje `DoWork` metodu úkolu.</span><span class="sxs-lookup"><span data-stu-id="0149d-187">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="0149d-188">Časovač je zakázán `StopAsync` a vyřazen při vyřazení `Dispose`kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="0149d-188">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="0149d-189">Služba je registrována v `IHostBuilder.ConfigureServices` (*program.cs*) s `AddHostedService` metodou rozšíření:</span><span class="sxs-lookup"><span data-stu-id="0149d-189">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="0149d-190">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="0149d-190">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="0149d-191">Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `BackgroundService`, vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="0149d-191">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="0149d-192">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="0149d-192">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="0149d-193">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="0149d-193">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="0149d-194">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="0149d-194">In the following example:</span></span>

* <span data-ttu-id="0149d-195">Služba je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="0149d-195">The service is asynchronous.</span></span> <span data-ttu-id="0149d-196">`DoWork` Metoda`Task`vrátí.</span><span class="sxs-lookup"><span data-stu-id="0149d-196">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="0149d-197">Pro demonstrační účely je v `DoWork` metodě očekáváno zpoždění deseti sekund.</span><span class="sxs-lookup"><span data-stu-id="0149d-197">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="0149d-198">Do <xref:Microsoft.Extensions.Logging.ILogger> služby se vloží.</span><span class="sxs-lookup"><span data-stu-id="0149d-198">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="0149d-199">Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí pro volání `DoWork` metody.</span><span class="sxs-lookup"><span data-stu-id="0149d-199">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="0149d-200">`DoWork`Vrátí, který je očekáván v `ExecuteAsync`: `Task`</span><span class="sxs-lookup"><span data-stu-id="0149d-200">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="0149d-201">Služby jsou zaregistrované `IHostBuilder.ConfigureServices` v (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="0149d-201">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="0149d-202">Hostovaná služba je zaregistrovaná `AddHostedService` v rámci metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="0149d-202">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="0149d-203">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="0149d-203">Queued background tasks</span></span>

<span data-ttu-id="0149d-204">Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako integrované pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="0149d-204">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="0149d-205">V následujícím `QueueHostedService` příkladu:</span><span class="sxs-lookup"><span data-stu-id="0149d-205">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="0149d-206">Metoda vrátí hodnotu ,která`ExecuteAsync`je očekávána v. `Task` `BackgroundProcessing`</span><span class="sxs-lookup"><span data-stu-id="0149d-206">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="0149d-207">Úlohy na pozadí ve frontě se odřadí a spustí v `BackgroundProcessing`.</span><span class="sxs-lookup"><span data-stu-id="0149d-207">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28,39-40,44)]

<span data-ttu-id="0149d-208">Služba zpracovává enqueuing úlohy pro hostovanou službu vždy, `w` když je vybraný klíč na vstupním zařízení: `MonitorLoop`</span><span class="sxs-lookup"><span data-stu-id="0149d-208">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="0149d-209">`IBackgroundTaskQueue` Je vložen`MonitorLoop` do služby.</span><span class="sxs-lookup"><span data-stu-id="0149d-209">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="0149d-210">`IBackgroundTaskQueue.QueueBackgroundWorkItem`je volána pro zařazení pracovní položky do fronty.</span><span class="sxs-lookup"><span data-stu-id="0149d-210">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="0149d-211">Služby jsou zaregistrované `IHostBuilder.ConfigureServices` v (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="0149d-211">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="0149d-212">Hostovaná služba je zaregistrovaná `AddHostedService` v rámci metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="0149d-212">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0149d-213">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="0149d-213">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="0149d-214">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="0149d-214">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="0149d-215">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="0149d-215">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="0149d-216">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="0149d-216">Background task that runs on a timer.</span></span>
* <span data-ttu-id="0149d-217">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="0149d-217">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="0149d-218">Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="0149d-218">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="0149d-219">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="0149d-219">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="0149d-220">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0149d-220">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0149d-221">Ukázková aplikace je k dispozici ve dvou verzích:</span><span class="sxs-lookup"><span data-stu-id="0149d-221">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="0149d-222">Webový hostitel &ndash; webového hostitele je vhodný pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="0149d-222">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="0149d-223">Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="0149d-223">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="0149d-224">Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .</span><span class="sxs-lookup"><span data-stu-id="0149d-224">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="0149d-225">Obecný hostitel &ndash; obecného hostitele je v ASP.NET Core 2,1 nový.</span><span class="sxs-lookup"><span data-stu-id="0149d-225">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="0149d-226">Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="0149d-226">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="0149d-227">Balíček</span><span class="sxs-lookup"><span data-stu-id="0149d-227">Package</span></span>

<span data-ttu-id="0149d-228">Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="0149d-228">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="0149d-229">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="0149d-229">IHostedService interface</span></span>

<span data-ttu-id="0149d-230">Hostované služby implementují <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0149d-230">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="0149d-231">Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="0149d-231">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="0149d-232">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; obsahujelogikupro`StartAsync` zahájení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="0149d-232">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="0149d-233">Při použití [webového hostitele](xref:fundamentals/host/web-host) `StartAsync` se volá po spuštění serveru a aktivaci [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="0149d-233">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="0149d-234">Při použití [obecného hostitele](xref:fundamentals/host/generic-host) `StartAsync` se před `ApplicationStarted` aktivací volá.</span><span class="sxs-lookup"><span data-stu-id="0149d-234">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="0149d-235">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Aktivováno, když hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="0149d-235">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="0149d-236">`StopAsync`obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="0149d-236">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="0149d-237">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="0149d-237">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="0149d-238">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="0149d-238">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="0149d-239">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="0149d-239">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="0149d-240">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="0149d-240">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="0149d-241">Všechny metody, které `StopAsync` jsou volány v nástroji, by měly vracet výzvu.</span><span class="sxs-lookup"><span data-stu-id="0149d-241">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="0149d-242">Úkoly se ale po zrušení žádosti&mdash;zruší, protože volající čeká na dokončení všech úkolů.</span><span class="sxs-lookup"><span data-stu-id="0149d-242">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="0149d-243">Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="0149d-243">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="0149d-244">Proto žádné metody, které jsou volány nebo `StopAsync` operací, které jsou prováděny v nástroji, nemusí nastat.</span><span class="sxs-lookup"><span data-stu-id="0149d-244">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="0149d-245">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="0149d-245">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="0149d-246"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Při použití obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="0149d-246"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="0149d-247">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="0149d-247">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="0149d-248">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="0149d-248">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="0149d-249">Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="0149d-249">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="0149d-250">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="0149d-250">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="0149d-251">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` měla by být volána i `StopAsync` v případě, že není volána.</span><span class="sxs-lookup"><span data-stu-id="0149d-251">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="0149d-252">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="0149d-252">Timed background tasks</span></span>

<span data-ttu-id="0149d-253">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="0149d-253">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="0149d-254">Časovač aktivuje `DoWork` metodu úkolu.</span><span class="sxs-lookup"><span data-stu-id="0149d-254">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="0149d-255">Časovač je zakázán `StopAsync` a vyřazen při vyřazení `Dispose`kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="0149d-255">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="0149d-256">Služba je zaregistrovaná `Startup.ConfigureServices` v `AddHostedService` s metodou rozšíření:</span><span class="sxs-lookup"><span data-stu-id="0149d-256">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="0149d-257">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="0149d-257">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="0149d-258">Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `IHostedService`, vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="0149d-258">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="0149d-259">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="0149d-259">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="0149d-260">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="0149d-260">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="0149d-261">V následujícím příkladu <xref:Microsoft.Extensions.Logging.ILogger> je vložena do služby:</span><span class="sxs-lookup"><span data-stu-id="0149d-261">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="0149d-262">Hostovaná služba vytvoří obor pro řešení služby úlohy na pozadí, aby volal jeho `DoWork` metodu:</span><span class="sxs-lookup"><span data-stu-id="0149d-262">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="0149d-263">Služby jsou zaregistrované `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="0149d-263">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0149d-264">Implementace je registrována `AddHostedService` s metodou rozšíření: `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="0149d-264">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="0149d-265">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="0149d-265">Queued background tasks</span></span>

<span data-ttu-id="0149d-266">Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako integrované pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="0149d-266">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="0149d-267">V `QueueHostedService`nástroji se úlohy na pozadí ve frontě odřadí a spustí <xref:Microsoft.Extensions.Hosting.BackgroundService>jako, což je základní třída pro implementaci dlouhého běhu `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="0149d-267">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="0149d-268">Služby jsou zaregistrované `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="0149d-268">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0149d-269">Implementace je registrována `AddHostedService` s metodou rozšíření: `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="0149d-269">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="0149d-270">Ve třídě modelu stránky indexu:</span><span class="sxs-lookup"><span data-stu-id="0149d-270">In the Index page model class:</span></span>

* <span data-ttu-id="0149d-271">Je vložen do konstruktoru a přiřazen k `Queue`. `IBackgroundTaskQueue`</span><span class="sxs-lookup"><span data-stu-id="0149d-271">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="0149d-272">Je vložený a přiřazený k `_serviceScopeFactory`. <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory></span><span class="sxs-lookup"><span data-stu-id="0149d-272">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="0149d-273">Objekt pro vytváření se používá k vytvoření instancí <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, které se používají k vytváření služeb v rámci oboru.</span><span class="sxs-lookup"><span data-stu-id="0149d-273">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="0149d-274">Je vytvořen obor, aby bylo možné použít `AppDbContext` ( [vymezenou](xref:fundamentals/dependency-injection#service-lifetimes)) aplikaci k zápisu záznamů `IBackgroundTaskQueue` databáze do služby (singleton).</span><span class="sxs-lookup"><span data-stu-id="0149d-274">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="0149d-275">Je-li na stránce indexu vybráno tlačítko **Přidat úkol** , `OnPostAddTask` je metoda spuštěna.</span><span class="sxs-lookup"><span data-stu-id="0149d-275">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="0149d-276">`QueueBackgroundWorkItem`je volána pro zařazení pracovní položky do fronty:</span><span class="sxs-lookup"><span data-stu-id="0149d-276">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0149d-277">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0149d-277">Additional resources</span></span>

* [<span data-ttu-id="0149d-278">Implementace úloh na pozadí v mikroslužbách pomocí IHostedService a třídy BackgroundService</span><span class="sxs-lookup"><span data-stu-id="0149d-278">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
