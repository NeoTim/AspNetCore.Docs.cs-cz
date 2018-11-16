---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2018
uid: fundamentals/host/hosted-services
ms.openlocfilehash: f8e13e13af22f1be4f14d5e59807c4dae3b78e84
ms.sourcegitcommit: 09bcda59a58019fdf47b2db5259fe87acf19dd38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51708488"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="dc9e1-103">Úlohy na pozadí s hostovanými službami v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dc9e1-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="dc9e1-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dc9e1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dc9e1-105">V ASP.NET Core, je možné implementovat úlohy na pozadí jako *hostovaných služeb*.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="dc9e1-106">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="dc9e1-107">Toto téma obsahuje tři příklady hostované služby:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="dc9e1-108">Úlohy na pozadí, který běží na časovač.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="dc9e1-109">Hostovanou službu, která aktivuje vymezené služby.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-109">Hosted service that activates a scoped service.</span></span> <span data-ttu-id="dc9e1-110">Injektáž závislostí můžete použít službu s vymezeným oborem.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-110">The scoped service can use dependency injection.</span></span>
* <span data-ttu-id="dc9e1-111">Úkoly ve frontě na pozadí, které spouští sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="dc9e1-112">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dc9e1-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="dc9e1-113">Ukázková aplikace je k dispozici ve dvou verzích:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="dc9e1-114">Webového hostitele &ndash; The webového hostitele je užitečné pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-114">Web Host &ndash; The Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="dc9e1-115">Příklad kódu v tomto tématu je od verze webového hostitele vzorku.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-115">The example code shown in this topic is from the Web Host version of the sample.</span></span> <span data-ttu-id="dc9e1-116">Další informace najdete v tématu [webového hostitele](xref:fundamentals/host/web-host) tématu.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="dc9e1-117">Obecný hostitele &ndash; obecný hostitelské Novinky v ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-117">Generic Host &ndash; The Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="dc9e1-118">Další informace najdete v tématu [obecný hostitele](xref:fundamentals/host/generic-host) tématu.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="dc9e1-119">Balíček</span><span class="sxs-lookup"><span data-stu-id="dc9e1-119">Package</span></span>

<span data-ttu-id="dc9e1-120">Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) balíčku.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-120">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="dc9e1-121">IHostedService rozhraní</span><span class="sxs-lookup"><span data-stu-id="dc9e1-121">IHostedService interface</span></span>

<span data-ttu-id="dc9e1-122">Hostované služby, které implementují <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-122">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="dc9e1-123">Rozhraní definuje dvě metody pro objekty, které se spravují přes hostitele:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-123">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="dc9e1-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="dc9e1-125">Při použití [webového hostitele](xref:fundamentals/host/web-host), `StartAsync` se volá, když server spuštěn a [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-125">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="dc9e1-126">Při použití [obecný hostitele](xref:fundamentals/host/generic-host), `StartAsync` je volána před provedením `ApplicationStarted` se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-126">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="dc9e1-127">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; aktivuje, pokud hostitel provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-127">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="dc9e1-128">`StopAsync` obsahuje logiku pro ukončení úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-128">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="dc9e1-129">Implementace <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) k uvolnění nespravovaných prostředků.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-129">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span> 

  <span data-ttu-id="dc9e1-130">Token rušení, který má výchozí pět druhý časový limit k označení, že proces vypnutí by už nebude bezproblémové.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-130">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="dc9e1-131">Když bude podán požadavek na token:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-131">When cancellation is requested on the token:</span></span>
  
  * <span data-ttu-id="dc9e1-132">Všechny zbývající operace na pozadí, které provádí aplikace by měla přerušena.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-132">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="dc9e1-133">Všechny metody v `StopAsync` by měla vrátit okamžitě.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-133">Any methods called in `StopAsync` should return promptly.</span></span>
  
  <span data-ttu-id="dc9e1-134">Nicméně úlohy nejsou byly zanechány po zrušení požadováno&mdash;volající čeká na dokončení všech úloh.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-134">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="dc9e1-135">Pokud se aplikace ukončí neočekávaně (například aplikace proces selže), `StopAsync` nemusí být volána.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-135">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="dc9e1-136">Proto jakékoli metody volá nebo operací provedených v `StopAsync` nemusí být.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-136">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

<span data-ttu-id="dc9e1-137">Hostovaná služba je aktivována jednou při spuštění aplikace a řádné ukončení při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-137">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="dc9e1-138">Pokud dojde k chybě při provádění úlohy na pozadí, `Dispose` by měla být volána i v případě `StopAsync` není volána.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-138">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="dc9e1-139">Úlohy na pozadí vypršel časový limit</span><span class="sxs-lookup"><span data-stu-id="dc9e1-139">Timed background tasks</span></span>

<span data-ttu-id="dc9e1-140">Úlohy na pozadí vypršel časový limit využívá [System.Threading.Timer](xref:System.Threading.Timer) třídy.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-140">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="dc9e1-141">Časovač spustí úkolu `DoWork` metody.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-141">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="dc9e1-142">Časovač je zakázáno na `StopAsync` a uvolněno při uvolnění má v kontejneru služby `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-142">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="dc9e1-143">Není registrován v `Startup.ConfigureServices` s `AddHostedService` – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-143">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="dc9e1-144">Vymezené služby v rámci úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="dc9e1-144">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="dc9e1-145">Použití vymezené služby v rámci `IHostedService`, vytvoření oboru.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-145">To use scoped services within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="dc9e1-146">Ve výchozím nastavení je vytvořen žádný obor pro hostovanou službu.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-146">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="dc9e1-147">Služba úloh na pozadí s vymezeným oborem obsahuje logiku úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-147">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="dc9e1-148">V následujícím příkladu <xref:Microsoft.Extensions.Logging.ILogger> se vloží do služby:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-148">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="dc9e1-149">Hostovaná služba vytvoří obor vyřešit služba úloh na pozadí s vymezeným oborem volat jeho `DoWork` metody:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-149">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="dc9e1-150">Služby jsou registrované ve `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-150">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dc9e1-151">`IHostedService` Zaregistruje implementaci `AddHostedService` – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-151">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="dc9e1-152">Úlohy na pozadí ve frontě</span><span class="sxs-lookup"><span data-stu-id="dc9e1-152">Queued background tasks</span></span>

<span data-ttu-id="dc9e1-153">Fronta úloh na pozadí je založená na platformě .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánované být integrované pro ASP.NET Core 3.0](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="dc9e1-153">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core 3.0](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="dc9e1-154">V `QueueHostedService`, úlohy na pozadí ve frontě jsou odstraněné z fronty a provést, protože <xref:Microsoft.Extensions.Hosting.BackgroundService>, což je základní třída pro implementaci dlouho běžící `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-154">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="dc9e1-155">Služby jsou registrované ve `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-155">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dc9e1-156">`IHostedService` Zaregistruje implementaci `AddHostedService` – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-156">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet3)]

<span data-ttu-id="dc9e1-157">V třídě modelu Index stránky `IBackgroundTaskQueue` se vloží do konstruktoru a přiřadí do `Queue`:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-157">In the Index page model class, the `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="dc9e1-158">Když **přidat úkol** výběru tlačítka na indexovou stránku, `OnPostAddTask` provedení metody.</span><span class="sxs-lookup"><span data-stu-id="dc9e1-158">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="dc9e1-159">`QueueBackgroundWorkItem` je volána k zařazení do fronty pracovní položku:</span><span class="sxs-lookup"><span data-stu-id="dc9e1-159">`QueueBackgroundWorkItem` is called to enqueue the work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="additional-resources"></a><span data-ttu-id="dc9e1-160">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="dc9e1-160">Additional resources</span></span>

* [<span data-ttu-id="dc9e1-161">Implementace úloh na pozadí v mikroslužbách s IHostedService a BackgroundService třídy</span><span class="sxs-lookup"><span data-stu-id="dc9e1-161">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="dc9e1-162">System.Threading.Timer</span><span class="sxs-lookup"><span data-stu-id="dc9e1-162">System.Threading.Timer</span></span>](xref:System.Threading.Timer)
