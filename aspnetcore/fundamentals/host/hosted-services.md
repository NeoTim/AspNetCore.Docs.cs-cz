---
title: Background tasks with hosted services in ASP.NET Core
author: guardrex
description: Learn how to implement background tasks with hosted services in ASP.NET Core.
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
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="424d1-103">Background tasks with hosted services in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="424d1-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="424d1-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="424d1-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="424d1-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span><span class="sxs-lookup"><span data-stu-id="424d1-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="424d1-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span><span class="sxs-lookup"><span data-stu-id="424d1-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="424d1-107">This topic provides three hosted service examples:</span><span class="sxs-lookup"><span data-stu-id="424d1-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="424d1-108">Background task that runs on a timer.</span><span class="sxs-lookup"><span data-stu-id="424d1-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="424d1-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="424d1-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="424d1-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="424d1-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="424d1-111">Queued background tasks that run sequentially.</span><span class="sxs-lookup"><span data-stu-id="424d1-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="424d1-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="424d1-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="424d1-113">Worker Service template</span><span class="sxs-lookup"><span data-stu-id="424d1-113">Worker Service template</span></span>

<span data-ttu-id="424d1-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span><span class="sxs-lookup"><span data-stu-id="424d1-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="424d1-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span><span class="sxs-lookup"><span data-stu-id="424d1-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="424d1-116">To use the template as a basis for a hosted services app:</span><span class="sxs-lookup"><span data-stu-id="424d1-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="424d1-117">Balíček</span><span class="sxs-lookup"><span data-stu-id="424d1-117">Package</span></span>

<span data-ttu-id="424d1-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span><span class="sxs-lookup"><span data-stu-id="424d1-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="424d1-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span><span class="sxs-lookup"><span data-stu-id="424d1-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="424d1-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span><span class="sxs-lookup"><span data-stu-id="424d1-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="424d1-121">An explicit package reference in the app's project file isn't required.</span><span class="sxs-lookup"><span data-stu-id="424d1-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="424d1-122">IHostedService interface</span><span class="sxs-lookup"><span data-stu-id="424d1-122">IHostedService interface</span></span>

<span data-ttu-id="424d1-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span><span class="sxs-lookup"><span data-stu-id="424d1-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="424d1-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span><span class="sxs-lookup"><span data-stu-id="424d1-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="424d1-125">`StartAsync` is called *before*:</span><span class="sxs-lookup"><span data-stu-id="424d1-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="424d1-126">The app's request processing pipeline is configured (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="424d1-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="424d1-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span><span class="sxs-lookup"><span data-stu-id="424d1-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="424d1-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span><span class="sxs-lookup"><span data-stu-id="424d1-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="424d1-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="424d1-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="424d1-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span><span class="sxs-lookup"><span data-stu-id="424d1-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="424d1-131">`StopAsync` contains the logic to end the background task.</span><span class="sxs-lookup"><span data-stu-id="424d1-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="424d1-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span><span class="sxs-lookup"><span data-stu-id="424d1-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="424d1-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span><span class="sxs-lookup"><span data-stu-id="424d1-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="424d1-134">When cancellation is requested on the token:</span><span class="sxs-lookup"><span data-stu-id="424d1-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="424d1-135">Any remaining background operations that the app is performing should be aborted.</span><span class="sxs-lookup"><span data-stu-id="424d1-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="424d1-136">Any methods called in `StopAsync` should return promptly.</span><span class="sxs-lookup"><span data-stu-id="424d1-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="424d1-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span><span class="sxs-lookup"><span data-stu-id="424d1-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="424d1-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span><span class="sxs-lookup"><span data-stu-id="424d1-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="424d1-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span><span class="sxs-lookup"><span data-stu-id="424d1-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="424d1-140">To extend the default five second shutdown timeout, set:</span><span class="sxs-lookup"><span data-stu-id="424d1-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="424d1-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span><span class="sxs-lookup"><span data-stu-id="424d1-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="424d1-142">Další informace najdete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="424d1-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="424d1-143">Shutdown timeout host configuration setting when using Web Host.</span><span class="sxs-lookup"><span data-stu-id="424d1-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="424d1-144">Další informace najdete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="424d1-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="424d1-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span><span class="sxs-lookup"><span data-stu-id="424d1-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="424d1-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span><span class="sxs-lookup"><span data-stu-id="424d1-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="424d1-147">BackgroundService base class</span><span class="sxs-lookup"><span data-stu-id="424d1-147">BackgroundService base class</span></span>

<span data-ttu-id="424d1-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="424d1-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="424d1-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span><span class="sxs-lookup"><span data-stu-id="424d1-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="424d1-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span><span class="sxs-lookup"><span data-stu-id="424d1-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="424d1-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/aspnet/Extensions/issues/2149), such as by calling `await`.</span><span class="sxs-lookup"><span data-stu-id="424d1-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/aspnet/Extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="424d1-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="424d1-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="424d1-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span><span class="sxs-lookup"><span data-stu-id="424d1-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="424d1-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span><span class="sxs-lookup"><span data-stu-id="424d1-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="424d1-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span><span class="sxs-lookup"><span data-stu-id="424d1-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="424d1-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span><span class="sxs-lookup"><span data-stu-id="424d1-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="424d1-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span><span class="sxs-lookup"><span data-stu-id="424d1-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="424d1-158">Timed background tasks</span><span class="sxs-lookup"><span data-stu-id="424d1-158">Timed background tasks</span></span>

<span data-ttu-id="424d1-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span><span class="sxs-lookup"><span data-stu-id="424d1-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="424d1-160">The timer triggers the task's `DoWork` method.</span><span class="sxs-lookup"><span data-stu-id="424d1-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="424d1-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="424d1-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="424d1-162">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span><span class="sxs-lookup"><span data-stu-id="424d1-162">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="424d1-163">Consuming a scoped service in a background task</span><span class="sxs-lookup"><span data-stu-id="424d1-163">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="424d1-164">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span><span class="sxs-lookup"><span data-stu-id="424d1-164">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="424d1-165">No scope is created for a hosted service by default.</span><span class="sxs-lookup"><span data-stu-id="424d1-165">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="424d1-166">The scoped background task service contains the background task's logic.</span><span class="sxs-lookup"><span data-stu-id="424d1-166">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="424d1-167">In the following example:</span><span class="sxs-lookup"><span data-stu-id="424d1-167">In the following example:</span></span>

* <span data-ttu-id="424d1-168">The service is asynchronous.</span><span class="sxs-lookup"><span data-stu-id="424d1-168">The service is asynchronous.</span></span> <span data-ttu-id="424d1-169">The `DoWork` method returns a `Task`.</span><span class="sxs-lookup"><span data-stu-id="424d1-169">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="424d1-170">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span><span class="sxs-lookup"><span data-stu-id="424d1-170">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="424d1-171">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span><span class="sxs-lookup"><span data-stu-id="424d1-171">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="424d1-172">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span><span class="sxs-lookup"><span data-stu-id="424d1-172">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="424d1-173">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="424d1-173">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="424d1-174">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="424d1-174">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="424d1-175">The hosted service is registered with the `AddHostedService` extension method:</span><span class="sxs-lookup"><span data-stu-id="424d1-175">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="424d1-176">Queued background tasks</span><span class="sxs-lookup"><span data-stu-id="424d1-176">Queued background tasks</span></span>

<span data-ttu-id="424d1-177">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="424d1-177">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="424d1-178">In the following `QueueHostedService` example:</span><span class="sxs-lookup"><span data-stu-id="424d1-178">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="424d1-179">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="424d1-179">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="424d1-180">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span><span class="sxs-lookup"><span data-stu-id="424d1-180">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="424d1-181">Work items are awaited before the service stops in `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="424d1-181">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="424d1-182">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span><span class="sxs-lookup"><span data-stu-id="424d1-182">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="424d1-183">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span><span class="sxs-lookup"><span data-stu-id="424d1-183">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="424d1-184">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span><span class="sxs-lookup"><span data-stu-id="424d1-184">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="424d1-185">The work item simulates a long-running background task:</span><span class="sxs-lookup"><span data-stu-id="424d1-185">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="424d1-186">Three 5-second delays are executed (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="424d1-186">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="424d1-187">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span><span class="sxs-lookup"><span data-stu-id="424d1-187">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="424d1-188">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="424d1-188">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="424d1-189">The hosted service is registered with the `AddHostedService` extension method:</span><span class="sxs-lookup"><span data-stu-id="424d1-189">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="424d1-190">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span><span class="sxs-lookup"><span data-stu-id="424d1-190">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="424d1-191">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span><span class="sxs-lookup"><span data-stu-id="424d1-191">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="424d1-192">This topic provides three hosted service examples:</span><span class="sxs-lookup"><span data-stu-id="424d1-192">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="424d1-193">Background task that runs on a timer.</span><span class="sxs-lookup"><span data-stu-id="424d1-193">Background task that runs on a timer.</span></span>
* <span data-ttu-id="424d1-194">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="424d1-194">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="424d1-195">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="424d1-195">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="424d1-196">Queued background tasks that run sequentially.</span><span class="sxs-lookup"><span data-stu-id="424d1-196">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="424d1-197">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="424d1-197">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="424d1-198">Balíček</span><span class="sxs-lookup"><span data-stu-id="424d1-198">Package</span></span>

<span data-ttu-id="424d1-199">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span><span class="sxs-lookup"><span data-stu-id="424d1-199">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="424d1-200">IHostedService interface</span><span class="sxs-lookup"><span data-stu-id="424d1-200">IHostedService interface</span></span>

<span data-ttu-id="424d1-201">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span><span class="sxs-lookup"><span data-stu-id="424d1-201">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="424d1-202">The interface defines two methods for objects that are managed by the host:</span><span class="sxs-lookup"><span data-stu-id="424d1-202">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="424d1-203">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span><span class="sxs-lookup"><span data-stu-id="424d1-203">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="424d1-204">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span><span class="sxs-lookup"><span data-stu-id="424d1-204">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="424d1-205">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span><span class="sxs-lookup"><span data-stu-id="424d1-205">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="424d1-206">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span><span class="sxs-lookup"><span data-stu-id="424d1-206">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="424d1-207">`StopAsync` contains the logic to end the background task.</span><span class="sxs-lookup"><span data-stu-id="424d1-207">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="424d1-208">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span><span class="sxs-lookup"><span data-stu-id="424d1-208">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="424d1-209">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span><span class="sxs-lookup"><span data-stu-id="424d1-209">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="424d1-210">When cancellation is requested on the token:</span><span class="sxs-lookup"><span data-stu-id="424d1-210">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="424d1-211">Any remaining background operations that the app is performing should be aborted.</span><span class="sxs-lookup"><span data-stu-id="424d1-211">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="424d1-212">Any methods called in `StopAsync` should return promptly.</span><span class="sxs-lookup"><span data-stu-id="424d1-212">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="424d1-213">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span><span class="sxs-lookup"><span data-stu-id="424d1-213">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="424d1-214">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span><span class="sxs-lookup"><span data-stu-id="424d1-214">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="424d1-215">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span><span class="sxs-lookup"><span data-stu-id="424d1-215">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="424d1-216">To extend the default five second shutdown timeout, set:</span><span class="sxs-lookup"><span data-stu-id="424d1-216">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="424d1-217"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span><span class="sxs-lookup"><span data-stu-id="424d1-217"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="424d1-218">Další informace najdete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="424d1-218">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="424d1-219">Shutdown timeout host configuration setting when using Web Host.</span><span class="sxs-lookup"><span data-stu-id="424d1-219">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="424d1-220">Další informace najdete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="424d1-220">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="424d1-221">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span><span class="sxs-lookup"><span data-stu-id="424d1-221">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="424d1-222">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span><span class="sxs-lookup"><span data-stu-id="424d1-222">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="424d1-223">Timed background tasks</span><span class="sxs-lookup"><span data-stu-id="424d1-223">Timed background tasks</span></span>

<span data-ttu-id="424d1-224">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span><span class="sxs-lookup"><span data-stu-id="424d1-224">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="424d1-225">The timer triggers the task's `DoWork` method.</span><span class="sxs-lookup"><span data-stu-id="424d1-225">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="424d1-226">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="424d1-226">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="424d1-227">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span><span class="sxs-lookup"><span data-stu-id="424d1-227">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="424d1-228">Consuming a scoped service in a background task</span><span class="sxs-lookup"><span data-stu-id="424d1-228">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="424d1-229">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span><span class="sxs-lookup"><span data-stu-id="424d1-229">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="424d1-230">No scope is created for a hosted service by default.</span><span class="sxs-lookup"><span data-stu-id="424d1-230">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="424d1-231">The scoped background task service contains the background task's logic.</span><span class="sxs-lookup"><span data-stu-id="424d1-231">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="424d1-232">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span><span class="sxs-lookup"><span data-stu-id="424d1-232">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="424d1-233">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span><span class="sxs-lookup"><span data-stu-id="424d1-233">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="424d1-234">The services are registered in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="424d1-234">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="424d1-235">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span><span class="sxs-lookup"><span data-stu-id="424d1-235">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="424d1-236">Queued background tasks</span><span class="sxs-lookup"><span data-stu-id="424d1-236">Queued background tasks</span></span>

<span data-ttu-id="424d1-237">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="424d1-237">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="424d1-238">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span><span class="sxs-lookup"><span data-stu-id="424d1-238">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="424d1-239">The services are registered in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="424d1-239">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="424d1-240">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span><span class="sxs-lookup"><span data-stu-id="424d1-240">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="424d1-241">In the Index page model class:</span><span class="sxs-lookup"><span data-stu-id="424d1-241">In the Index page model class:</span></span>

* <span data-ttu-id="424d1-242">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span><span class="sxs-lookup"><span data-stu-id="424d1-242">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="424d1-243">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="424d1-243">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="424d1-244">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span><span class="sxs-lookup"><span data-stu-id="424d1-244">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="424d1-245">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span><span class="sxs-lookup"><span data-stu-id="424d1-245">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="424d1-246">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span><span class="sxs-lookup"><span data-stu-id="424d1-246">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="424d1-247">`QueueBackgroundWorkItem` is called to enqueue a work item:</span><span class="sxs-lookup"><span data-stu-id="424d1-247">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="424d1-248">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="424d1-248">Additional resources</span></span>

* [<span data-ttu-id="424d1-249">Implement background tasks in microservices with IHostedService and the BackgroundService class</span><span class="sxs-lookup"><span data-stu-id="424d1-249">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
