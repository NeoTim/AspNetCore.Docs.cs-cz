---
title: SignalR hostitelských ASP.NET Core ve službách na pozadí
author: bradygaster
description: Naučte se, jak odesílat zprávy klientům SignalR z tříd .NET Core BackgroundService.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 000732115153eeafed3948c2a07acf77ffc34218
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73964046"
---
# <a name="host-aspnet-core-opno-locsignalr-in-background-services"></a><span data-ttu-id="967a0-103">SignalR hostitelských ASP.NET Core ve službách na pozadí</span><span class="sxs-lookup"><span data-stu-id="967a0-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="967a0-104">Od [Brady gastera](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="967a0-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="967a0-105">Tento článek poskytuje pokyny pro:</span><span class="sxs-lookup"><span data-stu-id="967a0-105">This article provides guidance for:</span></span>

* <span data-ttu-id="967a0-106">Hostování SignalRch Center pomocí pracovního procesu na pozadí hostovaného s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="967a0-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="967a0-107">Posílání zpráv pro připojené klienty z rozhraní .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span><span class="sxs-lookup"><span data-stu-id="967a0-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="967a0-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="967a0-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="wire-up-opno-locsignalr-during-startup"></a><span data-ttu-id="967a0-109">Při spuštění SignalR síťový telefon</span><span class="sxs-lookup"><span data-stu-id="967a0-109">Wire up SignalR during startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="967a0-110">Hostování ASP.NET Core SignalR centra v kontextu pracovního procesu na pozadí je stejné jako hostování centra ve webové aplikaci v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="967a0-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="967a0-111">V metodě `Startup.ConfigureServices` volání `services.AddSignalR` přidá požadované služby do vrstvy ASP.NET Core (DI) pro podporu SignalR.</span><span class="sxs-lookup"><span data-stu-id="967a0-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="967a0-112">V `Startup.Configure`je metoda `MapHub` volána ve zpětném volání `UseEndpoints` pro přenos koncových bodů rozbočovače v kanálu žádosti ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="967a0-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

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

<span data-ttu-id="967a0-113">Hostování ASP.NET Core SignalR centra v kontextu pracovního procesu na pozadí je stejné jako hostování centra ve webové aplikaci v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="967a0-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="967a0-114">V metodě `Startup.ConfigureServices` volání `services.AddSignalR` přidá požadované služby do vrstvy ASP.NET Core (DI) pro podporu SignalR.</span><span class="sxs-lookup"><span data-stu-id="967a0-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="967a0-115">V `Startup.Configure`je volána metoda `UseSignalR` pro přenos koncových bodů rozbočovače v kanálu žádosti ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="967a0-115">In `Startup.Configure`, the `UseSignalR` method is called to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="967a0-116">V předchozím příkladu třída `ClockHub` implementuje `Hub<T>` třídu pro vytvoření rozbočovače se silným typem.</span><span class="sxs-lookup"><span data-stu-id="967a0-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="967a0-117">`ClockHub` byla nakonfigurovaná v třídě `Startup`, aby reagovala na žádosti na `/hubs/clock`koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="967a0-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="967a0-118">Další informace o rozbočovačích se silnými typy najdete v tématu [použití Center v SignalR pro ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="967a0-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="967a0-119">Tato funkce se neomezuje na [> třídy centra\<t](xref:Microsoft.AspNetCore.SignalR.Hub`1) .</span><span class="sxs-lookup"><span data-stu-id="967a0-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="967a0-120">Budou fungovat i všechny třídy, které dědí z [rozbočovače](xref:Microsoft.AspNetCore.SignalR.Hub), například [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub).</span><span class="sxs-lookup"><span data-stu-id="967a0-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="967a0-121">Rozhraní používané `ClockHub` silným typem je rozhraní `IClock`.</span><span class="sxs-lookup"><span data-stu-id="967a0-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-opno-locsignalr-hub-from-a-background-service"></a><span data-ttu-id="967a0-122">Volání centra SignalR ze služby na pozadí</span><span class="sxs-lookup"><span data-stu-id="967a0-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="967a0-123">Při spuštění je `Worker` třída `BackgroundService`, která je zapojená pomocí `AddHostedService`.</span><span class="sxs-lookup"><span data-stu-id="967a0-123">During startup, the `Worker` class, a `BackgroundService`, is wired up using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="967a0-124">Vzhledem k tomu, že SignalR je v rámci `Startup` fáze připojená i v případě, že je každé centrum připojené k jednotlivému koncovému bodu v kanálu požadavků HTTP ASP.NET Core, je každé z nich reprezentované `IHubContext<T>` na serveru.</span><span class="sxs-lookup"><span data-stu-id="967a0-124">Since SignalR is also wired up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="967a0-125">Pomocí funkcí ASP.NET Core DI jsou jiné třídy, které jsou vytvořeny instancí hostující vrstvy, jako jsou `BackgroundService` třídy, třídy kontroleru MVC nebo modely stránek Razor, schopny získat odkazy na centra na straně serveru tím, že během konstrukce přijímají instance `IHubContext<ClockHub, IClock>`.</span><span class="sxs-lookup"><span data-stu-id="967a0-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="967a0-126">Jelikož je metoda `ExecuteAsync` volána iterativním způsobem ve službě na pozadí, je aktuální datum a čas serveru odesíláno připojeným klientům pomocí `ClockHub`.</span><span class="sxs-lookup"><span data-stu-id="967a0-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-opno-locsignalr-events-with-background-services"></a><span data-ttu-id="967a0-127">Reakce na události SignalR se službami na pozadí</span><span class="sxs-lookup"><span data-stu-id="967a0-127">React to SignalR events with background services</span></span>

<span data-ttu-id="967a0-128">Podobně jako jedna stránková aplikace, která používá klienta JavaScriptu pro SignalR nebo desktopovou aplikaci .NET, může pomocí <xref:signalr/dotnet-client>použít `BackgroundService` nebo `IHostedService` implementaci k připojení k SignalRm rozbočovači a reagovat na události.</span><span class="sxs-lookup"><span data-stu-id="967a0-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="967a0-129">Třída `ClockHubClient` implementuje rozhraní `IClock` i rozhraní `IHostedService`.</span><span class="sxs-lookup"><span data-stu-id="967a0-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="967a0-130">Tímto způsobem je možné během `Startup` pracovat nepřetržitě a reagovat na události centra ze serveru.</span><span class="sxs-lookup"><span data-stu-id="967a0-130">This way it can be wired up during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="967a0-131">Během inicializace `ClockHubClient` vytvoří instanci `HubConnection` a drátovou metodu `IClock.ShowTime` jako obslužnou rutinu pro `ShowTime` událost centra.</span><span class="sxs-lookup"><span data-stu-id="967a0-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and wires up the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="967a0-132">V `IHostedService.StartAsync` implementaci je `HubConnection` spouštěn asynchronně.</span><span class="sxs-lookup"><span data-stu-id="967a0-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="967a0-133">Během metody `IHostedService.StopAsync` je `HubConnection` odstraněn asynchronně.</span><span class="sxs-lookup"><span data-stu-id="967a0-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="967a0-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="967a0-134">Additional resources</span></span>

* [<span data-ttu-id="967a0-135">Začínáme</span><span class="sxs-lookup"><span data-stu-id="967a0-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="967a0-136">Centra</span><span class="sxs-lookup"><span data-stu-id="967a0-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="967a0-137">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="967a0-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="967a0-138">Rozbočovače silného typu</span><span class="sxs-lookup"><span data-stu-id="967a0-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
