---
title: Hostování ASP.NET Core SignalR ve službách na pozadí
author: bradygaster
description: Naučte se odesílat zprávy SignalR klientům z tříd .NET Core BackgroundService.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 409ace5e3eaa4ab1de0b9d5f0cbd0e10d9243ea9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022378"
---
# <a name="host-aspnet-core-no-locsignalr-in-background-services"></a>Hostování ASP.NET Core SignalR ve službách na pozadí

Od [Brady gastera](https://twitter.com/bradygaster)

Tento článek poskytuje pokyny pro:

* Hostování SignalR Center pomocí pracovního procesu na pozadí hostovaného s ASP.NET Core.
* Posílání zpráv pro připojené klienty z rozhraní .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).

::: moniker range=">= aspnetcore-3.0"

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(jak stáhnout)](xref:index#how-to-download-a-sample)

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(jak stáhnout)](xref:index#how-to-download-a-sample)

::: moniker-end

## <a name="enable-no-locsignalr-in-startup"></a>Povolit SignalR při spuštění

::: moniker range=">= aspnetcore-3.0"

Hostování ASP.NET Corech SignalR Center v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci. V `Startup.ConfigureServices` metodě volání `services.AddSignalR` přidá požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di) do podpory SignalR . V je `Startup.Configure` `MapHub` metoda volána ve `UseEndpoints` zpětném volání pro připojení koncových bodů centra v kanálu žádosti ASP.NET Core.

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

Hostování ASP.NET Corech SignalR Center v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci. V `Startup.ConfigureServices` metodě volání `services.AddSignalR` přidá požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di) do podpory SignalR . V nástroji `Startup.Configure` `UseSignalR` je metoda volána pro připojení koncových bodů centra v kanálu žádosti ASP.NET Core.

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

V předchozím příkladu `ClockHub` Třída implementuje `Hub<T>` třídu pro vytvoření rozbočovače silného typu. `ClockHub`Ve třídě byl nakonfigurován tak `Startup` , aby odpovídal na požadavky na koncovém bodu `/hubs/clock` .

Další informace o rozbočovačích se silnými typy najdete v tématu [použití Center v SignalR pro ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Tato funkce není omezená na třídu [centra \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Libovolná třída, která dědí z [rozbočovače](xref:Microsoft.AspNetCore.SignalR.Hub), například [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funguje.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

Rozhraní používané silným typem `ClockHub` je `IClock` rozhraní.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-no-locsignalr-hub-from-a-background-service"></a>Volání SignalR centra ze služby na pozadí

Při spuštění `Worker` `BackgroundService` je třída povolena pomocí `AddHostedService` .

```csharp
services.AddHostedService<Worker>();
```

Vzhledem k tomu SignalR , že je zároveň zapnutá i během `Startup` fáze, kde je každé centrum připojené k jednotlivému koncovému bodu v kanálu požadavků HTTP ASP.NET Core, je každé centrum reprezentované `IHubContext<T>` na serveru. Pomocí funkcí DI ASP.NET Core, jiné třídy, které jsou vytvořeny pomocí hostující vrstvy, jako jsou `BackgroundService` třídy, třídy KONTROLERU MVC nebo Razor modely stránek, mohou získat odkazy na centra na straně serveru tím, že přijímají instance `IHubContext<ClockHub, IClock>` během konstrukce.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

Jak `ExecuteAsync` je metoda volána iterativním způsobem ve službě na pozadí, je aktuální datum a čas serveru odesíláno připojeným klientům pomocí `ClockHub` .

## <a name="react-to-no-locsignalr-events-with-background-services"></a>Reakce na SignalR události se službami na pozadí

Podobně jako jedna stránková aplikace, která používá klienta JavaScriptu pro SignalR nebo desktopovou aplikaci .NET, může pomocí <xref:signalr/dotnet-client> implementace použít, a `BackgroundService` nebo použít implementaci, nebo `IHostedService` taky pro připojení k centrům SignalR a reakci na události.

`ClockHubClient`Třída implementuje `IClock` rozhraní i `IHostedService` rozhraní. Tímto způsobem je možné tuto možnost povolit, `Startup` aby běžela nepřetržitě a reagovala na události centra ze serveru.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Během inicializace `ClockHubClient` vytvoří instanci instance `HubConnection` a a povolí `IClock.ShowTime` metodu jako obslužnou rutinu pro `ShowTime` událost centra.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

V `IHostedService.StartAsync` implementaci `HubConnection` se modul spouští asynchronně.

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Během `IHostedService.StopAsync` metody `HubConnection` je vyřazení asynchronně.

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

V `IHostedService.StartAsync` implementaci `HubConnection` se modul spouští asynchronně.

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Během `IHostedService.StopAsync` metody `HubConnection` je vyřazení asynchronně.

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Začínáme](xref:tutorials/signalr)
* [Centra](xref:signalr/hubs)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
* [Rozbočovače silného typu](xref:signalr/hubs#strongly-typed-hubs)
