---
title: Funkce SignalR technologie ASP.NET Core hostitele služby na pozadí
author: bradygaster
description: Zjistěte, jak odesílat zprávy ze tříd .NET Core BackgroundService klientům SignalR.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: b359bd7f6b0667aeb8d9c8f5eb450637b1347b19
ms.sourcegitcommit: e418cb9cddeb3de06fa0cb4fdb5529da03ff6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55739667"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="4f88c-103">Funkce SignalR technologie ASP.NET Core hostitele služby na pozadí</span><span class="sxs-lookup"><span data-stu-id="4f88c-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="4f88c-104">Podle [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="4f88c-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="4f88c-105">Tento článek obsahuje pokyny pro:</span><span class="sxs-lookup"><span data-stu-id="4f88c-105">This article provides guidance for:</span></span>

* <span data-ttu-id="4f88c-106">Hostování rozbočovače SignalR pomocí pracovní proces na pozadí hostované pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4f88c-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="4f88c-107">Zasílání zpráv pro připojení klientů z v rámci .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span><span class="sxs-lookup"><span data-stu-id="4f88c-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="4f88c-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4f88c-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="wire-up-signalr-during-startup"></a><span data-ttu-id="4f88c-109">Nastavit SignalR při spuštění</span><span class="sxs-lookup"><span data-stu-id="4f88c-109">Wire up SignalR during startup</span></span>

<span data-ttu-id="4f88c-110">Hostování rozbočovače SignalR technologie ASP.NET Core v rámci pracovní proces na pozadí se shoduje s hostující Hubu ve webové aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4f88c-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="4f88c-111">V `Startup.ConfigureServices` metody, volání `services.AddSignalR` přidá k požadovaným službám vrstvu ASP.NET Core Dependency Injection (DI) pro podporu systému SignalR.</span><span class="sxs-lookup"><span data-stu-id="4f88c-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="4f88c-112">V `Startup.Configure`, `UseSignalR` metoda je volána k přenosu do centra koncových bodů v kanálu požadavku ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4f88c-112">In `Startup.Configure`, the `UseSignalR` method is called to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

<span data-ttu-id="4f88c-113">V předchozím příkladu `ClockHub` implementuje třída `Hub<T>` třídy za účelem vytvoření rozbočovač silného typu.</span><span class="sxs-lookup"><span data-stu-id="4f88c-113">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="4f88c-114">`ClockHub` Nakonfigurovanou `Startup` třídy reagovat na požadavky na koncovém bodu `/hubs/clock`.</span><span class="sxs-lookup"><span data-stu-id="4f88c-114">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="4f88c-115">Další informace o silného typu rozbočovače, naleznete v tématu [použití rozbočovače signalr pro ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="4f88c-115">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="4f88c-116">Tato funkce se neomezuje na [centra\<T >](xref:Microsoft.AspNetCore.SignalR.Hub`1) třídy.</span><span class="sxs-lookup"><span data-stu-id="4f88c-116">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="4f88c-117">Všechny třídy, která dědí z [centra](xref:Microsoft.AspNetCore.SignalR.Hub), jako například [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), budou fungovat i.</span><span class="sxs-lookup"><span data-stu-id="4f88c-117">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="4f88c-118">Rozhraní používá silného typu `ClockHub` je `IClock` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4f88c-118">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="4f88c-119">Volání rozbočovače SignalR ze služby na pozadí</span><span class="sxs-lookup"><span data-stu-id="4f88c-119">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="4f88c-120">Při spuštění `Worker` třídy, `BackgroundService`, je připraveno, pomocí `AddHostedService`.</span><span class="sxs-lookup"><span data-stu-id="4f88c-120">During startup, the `Worker` class, a `BackgroundService`, is wired up using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="4f88c-121">Protože SignalR je také připraveno, během `Startup` fáze, ve kterém každé centrum je připojen k jednotlivé koncový bod v ASP.NET Core kanál požadavků HTTP, je reprezentována každé centrum `IHubContext<T>` na serveru.</span><span class="sxs-lookup"><span data-stu-id="4f88c-121">Since SignalR is also wired up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="4f88c-122">Pomocí ASP.NET Core nabízí DI, dalším třídám, mohl vytvořit jeho instanci hostující vrstvy, jako je třeba `BackgroundService` tříd, kontroler MVC a modely stránky Razor, lze získat odkazy na rozbočovače na straně serveru tak, že přijímá instance `IHubContext<ClockHub, IClock>` během konstrukce.</span><span class="sxs-lookup"><span data-stu-id="4f88c-122">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="4f88c-123">Jako `ExecuteAsync` metoda je volána zavádět postupně ve službě na pozadí, aktuální datum a čas na serveru se odesílají připojeným klientům pomocí `ClockHub`.</span><span class="sxs-lookup"><span data-stu-id="4f88c-123">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="4f88c-124">Reagujte na události SignalR s služby na pozadí</span><span class="sxs-lookup"><span data-stu-id="4f88c-124">React to SignalR events with background services</span></span>

<span data-ttu-id="4f88c-125">Stejně jako jednotlivé stránky aplikace pomocí jazyka JavaScript klienta pro funkci SignalR nebo desktopové aplikace .NET, můžete provést pomocí <xref:signalr/dotnet-client>, `BackgroundService` nebo `IHostedService` implementace lze také použít k připojení k rozbočovačům SignalR a reagovat na události.</span><span class="sxs-lookup"><span data-stu-id="4f88c-125">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="4f88c-126">`ClockHubClient` Třída implementuje oba `IClock` rozhraní a `IHostedService` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4f88c-126">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="4f88c-127">Tímto způsobem ji můžou být připraveno, během `Startup` spouštět nepřetržitě a reagovat na události v centru ze serveru.</span><span class="sxs-lookup"><span data-stu-id="4f88c-127">This way it can be wired up during `Startup` to run continuously and respond to Hub events from the server.</span></span> 

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="4f88c-128">Během inicializace `ClockHubClient` vytvoří instanci `HubConnection` a sváže `IClock.ShowTime` metody jako obslužnou rutinu pro centra `ShowTime` událostí.</span><span class="sxs-lookup"><span data-stu-id="4f88c-128">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and wires up the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="4f88c-129">V `IHostedService.StartAsync` implementace, `HubConnection` se spustí asynchronně.</span><span class="sxs-lookup"><span data-stu-id="4f88c-129">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="4f88c-130">Během `IHostedService.StopAsync` metody `HubConnection` vyřazen asynchronně.</span><span class="sxs-lookup"><span data-stu-id="4f88c-130">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="4f88c-131">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4f88c-131">Additional resources</span></span>

* [<span data-ttu-id="4f88c-132">Začínáme</span><span class="sxs-lookup"><span data-stu-id="4f88c-132">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="4f88c-133">Centra</span><span class="sxs-lookup"><span data-stu-id="4f88c-133">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="4f88c-134">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="4f88c-134">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="4f88c-135">Silného typu rozbočovače</span><span class="sxs-lookup"><span data-stu-id="4f88c-135">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
