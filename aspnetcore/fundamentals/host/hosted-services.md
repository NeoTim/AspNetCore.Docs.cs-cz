---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/03/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 1db3ee1a9bcc0d41edf24df55bcd8d54fb0e9724
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081784"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="cbf3b-103">Úlohy na pozadí s hostovanými službami v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cbf3b-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="cbf3b-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cbf3b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cbf3b-105">V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="cbf3b-106">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="cbf3b-107">V tomto tématu najdete tři příklady hostovaných služeb:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="cbf3b-108">Úloha na pozadí, která běží na časovači.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="cbf3b-109">Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="cbf3b-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="cbf3b-110">Vymezená služba může používat vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-110">The scoped service can use dependency injection.</span></span>
* <span data-ttu-id="cbf3b-111">Úlohy na pozadí, které běží sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="cbf3b-112">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cbf3b-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cbf3b-113">Ukázková aplikace je k dispozici ve dvou verzích:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="cbf3b-114">Webový hostitel &ndash; webového hostitele je vhodný pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="cbf3b-115">Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="cbf3b-116">Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .</span><span class="sxs-lookup"><span data-stu-id="cbf3b-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="cbf3b-117">Obecný hostitel &ndash; obecného hostitele je v ASP.NET Core 2,1 nový.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="cbf3b-118">Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="cbf3b-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="cbf3b-119">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="cbf3b-119">Worker Service template</span></span>

<span data-ttu-id="cbf3b-120">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="cbf3b-121">Použití šablony jako základu pro aplikaci hostované služby:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-121">To use the template as a basis for a hosted services app:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cbf3b-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbf3b-122">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cbf3b-123">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-123">Create a new project.</span></span>
1. <span data-ttu-id="cbf3b-124">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-124">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cbf3b-125">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-125">Select **Next**.</span></span>
1. <span data-ttu-id="cbf3b-126">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-126">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="cbf3b-127">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-127">Select **Create**.</span></span>
1. <span data-ttu-id="cbf3b-128">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="cbf3b-128">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="cbf3b-129">Vyberte šablonu **služby pracovního procesu** .</span><span class="sxs-lookup"><span data-stu-id="cbf3b-129">Select the **Worker Service** template.</span></span> <span data-ttu-id="cbf3b-130">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-130">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="cbf3b-131">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="cbf3b-131">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cbf3b-132">Použijte šablonu Worker Service`worker`() s příkazem [dotnet New](/dotnet/core/tools/dotnet-new) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-132">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="cbf3b-133">V následujícím příkladu se vytvoří aplikace pracovní služby s názvem `ContosoWorkerService`.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-133">In the following example, a Worker Service app is created named `ContosoWorkerService`.</span></span> <span data-ttu-id="cbf3b-134">Složka pro `ContosoWorkerService` aplikaci se vytvoří automaticky při spuštění příkazu.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-134">A folder for the `ContosoWorkerService` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="package"></a><span data-ttu-id="cbf3b-135">Balíček</span><span class="sxs-lookup"><span data-stu-id="cbf3b-135">Package</span></span>

<span data-ttu-id="cbf3b-136">Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="cbf3b-136">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="cbf3b-137">Rozhraní IHostedService</span><span class="sxs-lookup"><span data-stu-id="cbf3b-137">IHostedService interface</span></span>

<span data-ttu-id="cbf3b-138">Hostované služby implementují <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-138">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="cbf3b-139">Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-139">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="cbf3b-140">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; obsahujelogikupro`StartAsync` zahájení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-140">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="cbf3b-141">Při použití [webového hostitele](xref:fundamentals/host/web-host) `StartAsync` se volá po spuštění serveru a aktivaci [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="cbf3b-141">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="cbf3b-142">Při použití [obecného hostitele](xref:fundamentals/host/generic-host) `StartAsync` se před `ApplicationStarted` aktivací volá.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-142">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="cbf3b-143">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Aktivováno, když hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-143">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="cbf3b-144">`StopAsync`obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-144">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="cbf3b-145">Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-145">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="cbf3b-146">Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-146">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="cbf3b-147">Když se na tokenu požaduje zrušení:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-147">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="cbf3b-148">Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-148">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="cbf3b-149">Všechny metody, které `StopAsync` jsou volány v nástroji, by měly vracet výzvu.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-149">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="cbf3b-150">Úkoly se ale po zrušení žádosti&mdash;zruší, protože volající čeká na dokončení všech úkolů.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-150">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="cbf3b-151">Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-151">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="cbf3b-152">Proto žádné metody, které jsou volány nebo `StopAsync` operací, které jsou prováděny v nástroji, nemusí nastat.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-152">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="cbf3b-153">Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-153">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="cbf3b-154"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Při použití obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-154"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="cbf3b-155">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-155">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="cbf3b-156">Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele</span><span class="sxs-lookup"><span data-stu-id="cbf3b-156">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="cbf3b-157">Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-157">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="cbf3b-158">Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-158">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="cbf3b-159">Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` měla by být volána i `StopAsync` v případě, že není volána.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-159">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="cbf3b-160">Časované úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="cbf3b-160">Timed background tasks</span></span>

<span data-ttu-id="cbf3b-161">Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="cbf3b-161">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="cbf3b-162">Časovač aktivuje `DoWork` metodu úkolu.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-162">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="cbf3b-163">Časovač je zakázán `StopAsync` a vyřazen při vyřazení `Dispose`kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-163">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="cbf3b-164">Služba je zaregistrovaná `Startup.ConfigureServices` v `AddHostedService` s metodou rozšíření:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-164">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="cbf3b-165">Využívání vymezené služby v úloze na pozadí</span><span class="sxs-lookup"><span data-stu-id="cbf3b-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="cbf3b-166">Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `IHostedService`, vytvořte obor.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="cbf3b-167">Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="cbf3b-168">Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="cbf3b-169">V následujícím příkladu <xref:Microsoft.Extensions.Logging.ILogger> je vložena do služby:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-169">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="cbf3b-170">Hostovaná služba vytvoří obor pro řešení služby úlohy na pozadí, aby volal jeho `DoWork` metodu:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-170">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="cbf3b-171">Služby jsou zaregistrované `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-171">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cbf3b-172">Implementace je registrována `AddHostedService` s metodou rozšíření: `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="cbf3b-172">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="cbf3b-173">Úlohy na pozadí zařazené do fronty</span><span class="sxs-lookup"><span data-stu-id="cbf3b-173">Queued background tasks</span></span>

<span data-ttu-id="cbf3b-174">Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako integrované pro ASP.NET Core 3,0](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="cbf3b-174">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core 3.0](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="cbf3b-175">V `QueueHostedService`nástroji se úlohy na pozadí ve frontě odřadí a spustí <xref:Microsoft.Extensions.Hosting.BackgroundService>jako, což je základní třída pro implementaci dlouhého běhu `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-175">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="cbf3b-176">Služby jsou zaregistrované `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-176">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cbf3b-177">Implementace je registrována `AddHostedService` s metodou rozšíření: `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="cbf3b-177">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet3)]

<span data-ttu-id="cbf3b-178">Ve třídě modelu stránky indexu:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-178">In the Index page model class:</span></span>

* <span data-ttu-id="cbf3b-179">Je vložen do konstruktoru a přiřazen k `Queue`. `IBackgroundTaskQueue`</span><span class="sxs-lookup"><span data-stu-id="cbf3b-179">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="cbf3b-180">Je vložený a přiřazený k `_serviceScopeFactory`. <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory></span><span class="sxs-lookup"><span data-stu-id="cbf3b-180">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="cbf3b-181">Objekt pro vytváření se používá k vytvoření instancí <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, které se používají k vytváření služeb v rámci oboru.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-181">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="cbf3b-182">Je vytvořen obor, aby bylo možné použít `AppDbContext` ( [vymezenou](xref:fundamentals/dependency-injection#service-lifetimes)) aplikaci k zápisu záznamů `IBackgroundTaskQueue` databáze do služby (singleton).</span><span class="sxs-lookup"><span data-stu-id="cbf3b-182">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="cbf3b-183">Je-li na stránce indexu vybráno tlačítko **Přidat úkol** , `OnPostAddTask` je metoda spuštěna.</span><span class="sxs-lookup"><span data-stu-id="cbf3b-183">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="cbf3b-184">`QueueBackgroundWorkItem`je volána k zařazení pracovní položky do fronty:</span><span class="sxs-lookup"><span data-stu-id="cbf3b-184">`QueueBackgroundWorkItem` is called to enqueue the work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="additional-resources"></a><span data-ttu-id="cbf3b-185">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cbf3b-185">Additional resources</span></span>

* [<span data-ttu-id="cbf3b-186">Implementace úloh na pozadí v mikroslužbách pomocí IHostedService a třídy BackgroundService</span><span class="sxs-lookup"><span data-stu-id="cbf3b-186">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
