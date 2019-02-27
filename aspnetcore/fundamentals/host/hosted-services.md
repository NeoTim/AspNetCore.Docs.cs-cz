---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: d10a335429752c1a52c1b3619adecc41725a819a
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899304"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="2a981-103">Úlohy na pozadí s hostovanými službami v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a981-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="2a981-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2a981-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2a981-105">V ASP.NET Core, je možné implementovat úlohy na pozadí jako *hostovaných služeb*.</span><span class="sxs-lookup"><span data-stu-id="2a981-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="2a981-106">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="2a981-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="2a981-107">Toto téma obsahuje tři příklady hostované služby:</span><span class="sxs-lookup"><span data-stu-id="2a981-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="2a981-108">Úlohy na pozadí, který běží na časovač.</span><span class="sxs-lookup"><span data-stu-id="2a981-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="2a981-109">Hostovaná služba, která se aktivuje [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="2a981-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="2a981-110">Injektáž závislostí můžete použít službu s vymezeným oborem.</span><span class="sxs-lookup"><span data-stu-id="2a981-110">The scoped service can use dependency injection.</span></span>
* <span data-ttu-id="2a981-111">Úkoly ve frontě na pozadí, které spouští sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="2a981-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="2a981-112">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2a981-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2a981-113">Ukázková aplikace je k dispozici ve dvou verzích:</span><span class="sxs-lookup"><span data-stu-id="2a981-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="2a981-114">Webového hostitele &ndash; webového hostitele je užitečné pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="2a981-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="2a981-115">Příklad kódu v tomto tématu se z webového hostitele verzi ukázky.</span><span class="sxs-lookup"><span data-stu-id="2a981-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="2a981-116">Další informace najdete v tématu [webového hostitele](xref:fundamentals/host/web-host) tématu.</span><span class="sxs-lookup"><span data-stu-id="2a981-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="2a981-117">Obecný hostitele &ndash; obecný hostitele je nového v ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="2a981-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="2a981-118">Další informace najdete v tématu [obecný hostitele](xref:fundamentals/host/generic-host) tématu.</span><span class="sxs-lookup"><span data-stu-id="2a981-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="2a981-119">Balíček</span><span class="sxs-lookup"><span data-stu-id="2a981-119">Package</span></span>

<span data-ttu-id="2a981-120">Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) balíčku.</span><span class="sxs-lookup"><span data-stu-id="2a981-120">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="2a981-121">IHostedService rozhraní</span><span class="sxs-lookup"><span data-stu-id="2a981-121">IHostedService interface</span></span>

<span data-ttu-id="2a981-122">Hostované služby, které implementují <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2a981-122">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="2a981-123">Rozhraní definuje dvě metody pro objekty, které se spravují přes hostitele:</span><span class="sxs-lookup"><span data-stu-id="2a981-123">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="2a981-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="2a981-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="2a981-125">Při použití [webového hostitele](xref:fundamentals/host/web-host), `StartAsync` se volá, když server spuštěn a [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="2a981-125">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="2a981-126">Při použití [obecný hostitele](xref:fundamentals/host/generic-host), `StartAsync` je volána před provedením `ApplicationStarted` se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="2a981-126">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="2a981-127">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; aktivuje, pokud hostitel provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="2a981-127">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="2a981-128">`StopAsync` obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="2a981-128">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="2a981-129">Implementace <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) k uvolnění nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="2a981-129">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="2a981-130">Token rušení, který má výchozí pět druhý časový limit k označení, že proces vypnutí by už nebude bezproblémové.</span><span class="sxs-lookup"><span data-stu-id="2a981-130">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="2a981-131">Když bude podán požadavek na token:</span><span class="sxs-lookup"><span data-stu-id="2a981-131">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="2a981-132">Všechny zbývající operace na pozadí, které provádí aplikace by měla přerušena.</span><span class="sxs-lookup"><span data-stu-id="2a981-132">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="2a981-133">Všechny metody v `StopAsync` by měla vrátit okamžitě.</span><span class="sxs-lookup"><span data-stu-id="2a981-133">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="2a981-134">Nicméně úlohy nejsou byly zanechány po zrušení požadováno&mdash;volající čeká na dokončení všech úloh.</span><span class="sxs-lookup"><span data-stu-id="2a981-134">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="2a981-135">Pokud se aplikace ukončí neočekávaně (například aplikace proces selže), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="2a981-135">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="2a981-136">Proto jakékoli metody volá nebo operací provedených v `StopAsync` nemusí být.</span><span class="sxs-lookup"><span data-stu-id="2a981-136">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="2a981-137">Chcete-li rozšířit výchozí pět druhý časový limit ukončení, nastavte:</span><span class="sxs-lookup"><span data-stu-id="2a981-137">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="2a981-138"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> jestli používáte obecný hostitele.</span><span class="sxs-lookup"><span data-stu-id="2a981-138"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="2a981-139">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="2a981-139">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="2a981-140">Vypnutí vypršení časového limitu hostitele konfigurace nastavení při použití webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="2a981-140">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="2a981-141">Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="2a981-141">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="2a981-142">Hostovaná služba je aktivována jednou při spuštění aplikace a řádné ukončení při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a981-142">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="2a981-143">Pokud dojde k chybě při provádění úlohy na pozadí, `Dispose` by měla být volána i v případě `StopAsync` není volána.</span><span class="sxs-lookup"><span data-stu-id="2a981-143">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="2a981-144">Úlohy na pozadí vypršel časový limit</span><span class="sxs-lookup"><span data-stu-id="2a981-144">Timed background tasks</span></span>

<span data-ttu-id="2a981-145">Úlohy na pozadí vypršel časový limit využívá [System.Threading.Timer](xref:System.Threading.Timer) třídy.</span><span class="sxs-lookup"><span data-stu-id="2a981-145">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="2a981-146">Časovač spustí úkolu `DoWork` metody.</span><span class="sxs-lookup"><span data-stu-id="2a981-146">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="2a981-147">Časovač je zakázáno na `StopAsync` a uvolněno při uvolnění má v kontejneru služby `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="2a981-147">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="2a981-148">Není registrován v `Startup.ConfigureServices` s `AddHostedService` – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2a981-148">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="2a981-149">Vymezené služby v rámci úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="2a981-149">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="2a981-150">Použití [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `IHostedService`, vytvoření oboru.</span><span class="sxs-lookup"><span data-stu-id="2a981-150">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="2a981-151">Ve výchozím nastavení je vytvořen žádný obor pro hostovanou službu.</span><span class="sxs-lookup"><span data-stu-id="2a981-151">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="2a981-152">Služba úloh na pozadí s vymezeným oborem obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="2a981-152">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="2a981-153">V následujícím příkladu <xref:Microsoft.Extensions.Logging.ILogger> se vloží do služby:</span><span class="sxs-lookup"><span data-stu-id="2a981-153">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="2a981-154">Hostovaná služba vytvoří obor vyřešit služba úloh na pozadí s vymezeným oborem volat jeho `DoWork` metody:</span><span class="sxs-lookup"><span data-stu-id="2a981-154">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="2a981-155">Služby jsou registrované ve `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2a981-155">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2a981-156">`IHostedService` Zaregistruje implementaci `AddHostedService` – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2a981-156">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="2a981-157">Úlohy na pozadí ve frontě</span><span class="sxs-lookup"><span data-stu-id="2a981-157">Queued background tasks</span></span>

<span data-ttu-id="2a981-158">Fronta úloh na pozadí je založená na platformě .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánované být integrované pro ASP.NET Core 3.0](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="2a981-158">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core 3.0](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="2a981-159">V `QueueHostedService`, úlohy na pozadí ve frontě jsou odstraněné z fronty a provést, protože <xref:Microsoft.Extensions.Hosting.BackgroundService>, což je základní třída pro implementaci dlouho běžící `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="2a981-159">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="2a981-160">Služby jsou registrované ve `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2a981-160">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2a981-161">`IHostedService` Zaregistruje implementaci `AddHostedService` – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2a981-161">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet3)]

<span data-ttu-id="2a981-162">V třídě modelu Index stránky:</span><span class="sxs-lookup"><span data-stu-id="2a981-162">In the Index page model class:</span></span>

* <span data-ttu-id="2a981-163">`IBackgroundTaskQueue` Se vloží do konstruktoru a přiřadí do `Queue`.</span><span class="sxs-lookup"><span data-stu-id="2a981-163">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="2a981-164"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> Se vloží a přiřadí do `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="2a981-164">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="2a981-165">Objekt factory slouží k vytvoření instance <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, který se používá k vytvoření služeb v rámci oboru.</span><span class="sxs-lookup"><span data-stu-id="2a981-165">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="2a981-166">Chcete-li použít aplikace se vytvoří obor `AppDbContext` ( [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes)) zapište záznamy databáze `IBackgroundTaskQueue` (služby typu singleton).</span><span class="sxs-lookup"><span data-stu-id="2a981-166">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="2a981-167">Když **přidat úkol** výběru tlačítka na indexovou stránku, `OnPostAddTask` provedení metody.</span><span class="sxs-lookup"><span data-stu-id="2a981-167">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="2a981-168">`QueueBackgroundWorkItem` je volána k zařazení do fronty pracovní položku:</span><span class="sxs-lookup"><span data-stu-id="2a981-168">`QueueBackgroundWorkItem` is called to enqueue the work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="additional-resources"></a><span data-ttu-id="2a981-169">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2a981-169">Additional resources</span></span>

* [<span data-ttu-id="2a981-170">Implementace úloh na pozadí v mikroslužbách s IHostedService a BackgroundService třídy</span><span class="sxs-lookup"><span data-stu-id="2a981-170">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
