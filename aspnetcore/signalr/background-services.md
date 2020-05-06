---
title: Hostování ASP.NET Core SignalR ve službách na pozadí
author: bradygaster
description: Naučte se odesílat zprávy SignalR klientům z tříd .NET Core BackgroundService.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: d5f1668d601f520939956985e46c62f3a5bdfcfa
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777290"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="8901b-103">Hostování ASP.NET Core SignalR ve službách na pozadí</span><span class="sxs-lookup"><span data-stu-id="8901b-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="8901b-104">Od [Brady gastera](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="8901b-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="8901b-105">Tento článek poskytuje pokyny pro:</span><span class="sxs-lookup"><span data-stu-id="8901b-105">This article provides guidance for:</span></span>

* <span data-ttu-id="8901b-106">Hostování SignalR Center pomocí pracovního procesu na pozadí hostovaného s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8901b-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="8901b-107">Posílání zpráv pro připojené klienty z rozhraní .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span><span class="sxs-lookup"><span data-stu-id="8901b-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="8901b-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="8901b-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="enable-signalr-in-startup"></a><span data-ttu-id="8901b-109">Povolit SignalR při spuštění</span><span class="sxs-lookup"><span data-stu-id="8901b-109">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8901b-110">Hostování ASP.NET Corech SignalR Center v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8901b-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="8901b-111">V `Startup.ConfigureServices` metodě volání `services.AddSignalR` přidá požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di) do podpory. SignalR</span><span class="sxs-lookup"><span data-stu-id="8901b-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="8901b-112">V `Startup.Configure`je `MapHub` metoda volána ve `UseEndpoints` zpětném volání pro připojení koncových bodů centra v kanálu žádosti ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8901b-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8901b-113">Hostování ASP.NET Corech SignalR Center v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8901b-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="8901b-114">V `Startup.ConfigureServices` metodě volání `services.AddSignalR` přidá požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di) do podpory. SignalR</span><span class="sxs-lookup"><span data-stu-id="8901b-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="8901b-115">V `Startup.Configure`nástroji je `UseSignalR` metoda volána pro připojení koncových bodů centra v kanálu žádosti ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8901b-115">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="8901b-116">V předchozím příkladu `ClockHub` třída implementuje `Hub<T>` třídu pro vytvoření rozbočovače silného typu.</span><span class="sxs-lookup"><span data-stu-id="8901b-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="8901b-117">Ve třídě byl nakonfigurován tak, aby odpovídal na požadavky na koncovém bodu `/hubs/clock` `ClockHub` `Startup`</span><span class="sxs-lookup"><span data-stu-id="8901b-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="8901b-118">Další informace o rozbočovačích se silnými typy najdete v tématu [použití Center v SignalR pro ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="8901b-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="8901b-119">Tato funkce není omezená na třídu [>\<centra t](xref:Microsoft.AspNetCore.SignalR.Hub`1) .</span><span class="sxs-lookup"><span data-stu-id="8901b-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="8901b-120">Budou fungovat i všechny třídy, které dědí z [rozbočovače](xref:Microsoft.AspNetCore.SignalR.Hub), například [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub).</span><span class="sxs-lookup"><span data-stu-id="8901b-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="8901b-121">Rozhraní používané silným typem `ClockHub` je `IClock` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8901b-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="8901b-122">Volání SignalR centra ze služby na pozadí</span><span class="sxs-lookup"><span data-stu-id="8901b-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="8901b-123">Při spuštění je `Worker` třída `BackgroundService`povolena pomocí. `AddHostedService`</span><span class="sxs-lookup"><span data-stu-id="8901b-123">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="8901b-124">Vzhledem SignalR k tomu, že je zároveň `Startup` zapnutá i během fáze, kde je každé centrum připojené k jednotlivému koncovému bodu v kanálu požadavků HTTP ASP.NET Core, je každé `IHubContext<T>` centrum reprezentované na serveru.</span><span class="sxs-lookup"><span data-stu-id="8901b-124">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="8901b-125">Pomocí funkcí DI ASP.NET Core, jiné třídy, které jsou vytvořeny pomocí hostující vrstvy, jako `BackgroundService` jsou třídy, třídy kontroleru MVC nebo Razor modely stránek, mohou získat odkazy na centra na straně serveru tím, že přijímají instance `IHubContext<ClockHub, IClock>` během konstrukce.</span><span class="sxs-lookup"><span data-stu-id="8901b-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="8901b-126">Jak je `ExecuteAsync` metoda volána iterativním způsobem ve službě na pozadí, je aktuální datum a čas serveru odesíláno připojeným klientům pomocí `ClockHub`.</span><span class="sxs-lookup"><span data-stu-id="8901b-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="8901b-127">Reakce na SignalR události se službami na pozadí</span><span class="sxs-lookup"><span data-stu-id="8901b-127">React to SignalR events with background services</span></span>

<span data-ttu-id="8901b-128">Podobně jako jedna stránková aplikace, která používá klienta SignalR JavaScriptu pro nebo <xref:signalr/dotnet-client>desktopovou aplikaci .NET, může pomocí implementace použít, `BackgroundService` a `IHostedService` nebo použít implementaci, nebo taky pro připojení SignalR k centrům a reakci na události.</span><span class="sxs-lookup"><span data-stu-id="8901b-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="8901b-129">`ClockHubClient` Třída implementuje `IClock` rozhraní i `IHostedService` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8901b-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="8901b-130">Tímto způsobem je možné tuto možnost povolit `Startup` , aby běžela nepřetržitě a reagovala na události centra ze serveru.</span><span class="sxs-lookup"><span data-stu-id="8901b-130">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="8901b-131">Během inicializace `ClockHubClient` vytvoří instanci instance `HubConnection` a a povolí `IClock.ShowTime` metodu jako obslužnou rutinu pro `ShowTime` událost centra.</span><span class="sxs-lookup"><span data-stu-id="8901b-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="8901b-132">V `IHostedService.StartAsync` implementaci se modul `HubConnection` spouští asynchronně.</span><span class="sxs-lookup"><span data-stu-id="8901b-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="8901b-133">Během `IHostedService.StopAsync` metody `HubConnection` je vyřazení asynchronně.</span><span class="sxs-lookup"><span data-stu-id="8901b-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="8901b-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8901b-134">Additional resources</span></span>

* [<span data-ttu-id="8901b-135">Začínáme</span><span class="sxs-lookup"><span data-stu-id="8901b-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="8901b-136">Centra</span><span class="sxs-lookup"><span data-stu-id="8901b-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="8901b-137">Publikování aplikací do Azure</span><span class="sxs-lookup"><span data-stu-id="8901b-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="8901b-138">Rozbočovače silného typu</span><span class="sxs-lookup"><span data-stu-id="8901b-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
