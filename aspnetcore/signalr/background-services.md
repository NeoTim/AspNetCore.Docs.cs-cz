---
title: Signál hostitelského ASP.NET Core ve službách na pozadí
author: bradygaster
description: Naučte se odesílat zprávy klientům signalizace z tříd .NET Core BackgroundService.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: 23a53f33a03ce3b76cf6846c3f214a6cad055209
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773942"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="07666-103">Signál hostitelského ASP.NET Core ve službách na pozadí</span><span class="sxs-lookup"><span data-stu-id="07666-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="07666-104">Od [Brady gastera](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="07666-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="07666-105">Tento článek poskytuje pokyny pro:</span><span class="sxs-lookup"><span data-stu-id="07666-105">This article provides guidance for:</span></span>

* <span data-ttu-id="07666-106">Hostování Center signálů pomocí pracovního procesu na pozadí hostovaného s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07666-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="07666-107">Posílání zpráv pro připojené klienty z rozhraní .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span><span class="sxs-lookup"><span data-stu-id="07666-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="07666-108">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(stažení)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="07666-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="wire-up-signalr-during-startup"></a><span data-ttu-id="07666-109">Síťový signál při spuštění</span><span class="sxs-lookup"><span data-stu-id="07666-109">Wire up SignalR during startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07666-110">Hostování Center ASP.NET Corech signálů v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="07666-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="07666-111">V metodě volání `services.AddSignalR` přidá k podpoře signálu požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di). `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="07666-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="07666-112">V `Startup.Configure`je metodavolánavezpětnémvolánípropřenoskoncovýchbodůrozbočovačevkanálužádostiASP.NETCore.`MapHub` `UseEndpoints`</span><span class="sxs-lookup"><span data-stu-id="07666-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

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

<span data-ttu-id="07666-113">Hostování Center ASP.NET Corech signálů v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="07666-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="07666-114">V metodě volání `services.AddSignalR` přidá k podpoře signálu požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di). `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="07666-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="07666-115">V `Startup.Configure`nástrojije metodavolánapropřenoskoncovýchbodůrozbočovačevkanálužádostiASP.NETCore.`UseSignalR`</span><span class="sxs-lookup"><span data-stu-id="07666-115">In `Startup.Configure`, the `UseSignalR` method is called to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="07666-116">V předchozím příkladu `ClockHub` třída `Hub<T>` implementuje třídu pro vytvoření rozbočovače silného typu.</span><span class="sxs-lookup"><span data-stu-id="07666-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="07666-117">Ve třídě byl nakonfigurován tak, aby odpovídal na požadavky na koncovém bodu `/hubs/clock`. `ClockHub` `Startup`</span><span class="sxs-lookup"><span data-stu-id="07666-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="07666-118">Další informace o rozbočovačích se silnými typy najdete v tématu [použití Center v nástroji Signal pro ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="07666-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="07666-119">Tato funkce není omezená na třídu [>\<centra t](xref:Microsoft.AspNetCore.SignalR.Hub`1) .</span><span class="sxs-lookup"><span data-stu-id="07666-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="07666-120">Budou fungovat i všechny třídy, které dědí z [rozbočovače](xref:Microsoft.AspNetCore.SignalR.Hub), například [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub).</span><span class="sxs-lookup"><span data-stu-id="07666-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="07666-121">Rozhraní používané silným typem `ClockHub` `IClock` je rozhraní.</span><span class="sxs-lookup"><span data-stu-id="07666-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="07666-122">Volání centra signalizace ze služby na pozadí</span><span class="sxs-lookup"><span data-stu-id="07666-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="07666-123">Během spouštění `Worker` je třída `BackgroundService`, a, připojená pomocí `AddHostedService`.</span><span class="sxs-lookup"><span data-stu-id="07666-123">During startup, the `Worker` class, a `BackgroundService`, is wired up using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="07666-124">Vzhledem k tomu, že je signál v průběhu `Startup` fáze také zapojený, kdy je každé centrum připojené k jednotlivému koncovému bodu v kanálu požadavků HTTP ASP.NET Core, je každé centrum reprezentované `IHubContext<T>` na serveru.</span><span class="sxs-lookup"><span data-stu-id="07666-124">Since SignalR is also wired up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="07666-125">Pomocí funkcí ASP.NET Core di jsou jiné třídy, jejichž instance je vytvořena hostující vrstvou, `BackgroundService` jako jsou třídy, třídy kontroleru MVC nebo modely stránek Razor, schopny získat odkazy na centra na straně serveru tím, `IHubContext<ClockHub, IClock>` že přijímají instance během konstrukce.</span><span class="sxs-lookup"><span data-stu-id="07666-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="07666-126">Jak je `ClockHub`metoda volána iterativním způsobem ve službě na pozadí, je aktuální datum a čas serveru odesíláno připojeným klientům pomocí. `ExecuteAsync`</span><span class="sxs-lookup"><span data-stu-id="07666-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="07666-127">Reakce na události signalizace pomocí služeb na pozadí</span><span class="sxs-lookup"><span data-stu-id="07666-127">React to SignalR events with background services</span></span>

<span data-ttu-id="07666-128">Podobně jako jedna stránková aplikace, která používá klienta JavaScriptu pro Signal nebo desktopovou aplikaci .NET <xref:signalr/dotnet-client> `BackgroundService` , se dá použít k připojení k centrům signálů a k reagování na události pomocí implementace, a nebo `IHostedService` .</span><span class="sxs-lookup"><span data-stu-id="07666-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="07666-129">Třída implementuje `IClock` rozhraní i`IHostedService` rozhraní. `ClockHubClient`</span><span class="sxs-lookup"><span data-stu-id="07666-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="07666-130">Tímto způsobem je možné během `Startup` nepřetržitého chodu a reakce na události centra ze serveru pracovat.</span><span class="sxs-lookup"><span data-stu-id="07666-130">This way it can be wired up during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="07666-131">Během inicializace `ClockHubClient` vytvoří instance `HubConnection` a vodiče `IClock.ShowTime` `ShowTime` metodu jako obslužnou rutinu pro událost centra.</span><span class="sxs-lookup"><span data-stu-id="07666-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and wires up the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="07666-132">V implementaci se modul `HubConnection` spouští asynchronně. `IHostedService.StartAsync`</span><span class="sxs-lookup"><span data-stu-id="07666-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="07666-133">`IHostedService.StopAsync` Během metody `HubConnection` je vyřazení asynchronně.</span><span class="sxs-lookup"><span data-stu-id="07666-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="07666-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="07666-134">Additional resources</span></span>

* [<span data-ttu-id="07666-135">Začínáme</span><span class="sxs-lookup"><span data-stu-id="07666-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="07666-136">Centra</span><span class="sxs-lookup"><span data-stu-id="07666-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="07666-137">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="07666-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="07666-138">Rozbočovače silného typu</span><span class="sxs-lookup"><span data-stu-id="07666-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
