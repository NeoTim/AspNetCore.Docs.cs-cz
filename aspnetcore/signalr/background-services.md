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
ms.openlocfilehash: 7bc3b9535055e3fccf23ffa4638bd29676910348
ms.sourcegitcommit: e87dfa08fec0be1008249b1be678e5f79dcc5acb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382566"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="4573d-103">Hostování ASP.NET Core SignalR ve službách na pozadí</span><span class="sxs-lookup"><span data-stu-id="4573d-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="4573d-104">Od [Brady gastera](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="4573d-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="4573d-105">Tento článek poskytuje pokyny pro:</span><span class="sxs-lookup"><span data-stu-id="4573d-105">This article provides guidance for:</span></span>

* <span data-ttu-id="4573d-106">Hostování SignalR Center pomocí pracovního procesu na pozadí hostovaného s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4573d-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="4573d-107">Posílání zpráv pro připojené klienty z rozhraní .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span><span class="sxs-lookup"><span data-stu-id="4573d-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4573d-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4573d-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="4573d-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4573d-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end

## <a name="enable-signalr-in-startup"></a><span data-ttu-id="4573d-110">Povolit SignalR při spuštění</span><span class="sxs-lookup"><span data-stu-id="4573d-110">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4573d-111">Hostování ASP.NET Corech SignalR Center v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4573d-111">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="4573d-112">V `Startup.ConfigureServices` metodě volání `services.AddSignalR` přidá požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di) do podpory SignalR .</span><span class="sxs-lookup"><span data-stu-id="4573d-112">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="4573d-113">V je `Startup.Configure` `MapHub` metoda volána ve `UseEndpoints` zpětném volání pro připojení koncových bodů centra v kanálu žádosti ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4573d-113">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="4573d-114">Hostování ASP.NET Corech SignalR Center v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4573d-114">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="4573d-115">V `Startup.ConfigureServices` metodě volání `services.AddSignalR` přidá požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di) do podpory SignalR .</span><span class="sxs-lookup"><span data-stu-id="4573d-115">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="4573d-116">V nástroji `Startup.Configure` `UseSignalR` je metoda volána pro připojení koncových bodů centra v kanálu žádosti ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4573d-116">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="4573d-117">V předchozím příkladu `ClockHub` Třída implementuje `Hub<T>` třídu pro vytvoření rozbočovače silného typu.</span><span class="sxs-lookup"><span data-stu-id="4573d-117">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="4573d-118">`ClockHub`Ve třídě byl nakonfigurován tak `Startup` , aby odpovídal na požadavky na koncovém bodu `/hubs/clock` .</span><span class="sxs-lookup"><span data-stu-id="4573d-118">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="4573d-119">Další informace o rozbočovačích se silnými typy najdete v tématu [použití Center v SignalR pro ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="4573d-119">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="4573d-120">Tato funkce není omezená na třídu [ \<>centra t](xref:Microsoft.AspNetCore.SignalR.Hub`1) .</span><span class="sxs-lookup"><span data-stu-id="4573d-120">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="4573d-121">Libovolná třída, která dědí z [rozbočovače](xref:Microsoft.AspNetCore.SignalR.Hub), například [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funguje.</span><span class="sxs-lookup"><span data-stu-id="4573d-121">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), works.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

<span data-ttu-id="4573d-122">Rozhraní používané silným typem `ClockHub` je `IClock` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4573d-122">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="4573d-123">Volání SignalR centra ze služby na pozadí</span><span class="sxs-lookup"><span data-stu-id="4573d-123">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="4573d-124">Při spuštění `Worker` `BackgroundService` je třída povolena pomocí `AddHostedService` .</span><span class="sxs-lookup"><span data-stu-id="4573d-124">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="4573d-125">Vzhledem k tomu SignalR , že je zároveň zapnutá i během `Startup` fáze, kde je každé centrum připojené k jednotlivému koncovému bodu v kanálu požadavků HTTP ASP.NET Core, je každé centrum reprezentované `IHubContext<T>` na serveru.</span><span class="sxs-lookup"><span data-stu-id="4573d-125">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="4573d-126">Pomocí funkcí DI ASP.NET Core, jiné třídy, které jsou vytvořeny pomocí hostující vrstvy, jako jsou `BackgroundService` třídy, třídy KONTROLERU MVC nebo Razor modely stránek, mohou získat odkazy na centra na straně serveru tím, že přijímají instance `IHubContext<ClockHub, IClock>` během konstrukce.</span><span class="sxs-lookup"><span data-stu-id="4573d-126">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

<span data-ttu-id="4573d-127">Jak `ExecuteAsync` je metoda volána iterativním způsobem ve službě na pozadí, je aktuální datum a čas serveru odesíláno připojeným klientům pomocí `ClockHub` .</span><span class="sxs-lookup"><span data-stu-id="4573d-127">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="4573d-128">Reakce na SignalR události se službami na pozadí</span><span class="sxs-lookup"><span data-stu-id="4573d-128">React to SignalR events with background services</span></span>

<span data-ttu-id="4573d-129">Podobně jako jedna stránková aplikace, která používá klienta JavaScriptu pro SignalR nebo desktopovou aplikaci .NET, může pomocí <xref:signalr/dotnet-client> implementace použít, a `BackgroundService` nebo použít implementaci, nebo `IHostedService` taky pro připojení k centrům SignalR a reakci na události.</span><span class="sxs-lookup"><span data-stu-id="4573d-129">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="4573d-130">`ClockHubClient`Třída implementuje `IClock` rozhraní i `IHostedService` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4573d-130">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="4573d-131">Tímto způsobem je možné tuto možnost povolit, `Startup` aby běžela nepřetržitě a reagovala na události centra ze serveru.</span><span class="sxs-lookup"><span data-stu-id="4573d-131">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="4573d-132">Během inicializace `ClockHubClient` vytvoří instanci instance `HubConnection` a a povolí `IClock.ShowTime` metodu jako obslužnou rutinu pro `ShowTime` událost centra.</span><span class="sxs-lookup"><span data-stu-id="4573d-132">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="4573d-133">V `IHostedService.StartAsync` implementaci `HubConnection` se modul spouští asynchronně.</span><span class="sxs-lookup"><span data-stu-id="4573d-133">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="4573d-134">Během `IHostedService.StopAsync` metody `HubConnection` je vyřazení asynchronně.</span><span class="sxs-lookup"><span data-stu-id="4573d-134">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="4573d-135">V `IHostedService.StartAsync` implementaci `HubConnection` se modul spouští asynchronně.</span><span class="sxs-lookup"><span data-stu-id="4573d-135">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="4573d-136">Během `IHostedService.StopAsync` metody `HubConnection` je vyřazení asynchronně.</span><span class="sxs-lookup"><span data-stu-id="4573d-136">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4573d-137">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="4573d-137">Additional resources</span></span>

* [<span data-ttu-id="4573d-138">Začínáme</span><span class="sxs-lookup"><span data-stu-id="4573d-138">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="4573d-139">Centra</span><span class="sxs-lookup"><span data-stu-id="4573d-139">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="4573d-140">Publikování aplikací do Azure</span><span class="sxs-lookup"><span data-stu-id="4573d-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="4573d-141">Rozbočovače silného typu</span><span class="sxs-lookup"><span data-stu-id="4573d-141">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
