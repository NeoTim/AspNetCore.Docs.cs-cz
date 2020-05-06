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
# <a name="host-aspnet-core-signalr-in-background-services"></a>Hostování ASP.NET Core SignalR ve službách na pozadí

Od [Brady gastera](https://twitter.com/bradygaster)

Tento článek poskytuje pokyny pro:

* Hostování SignalR Center pomocí pracovního procesu na pozadí hostovaného s ASP.NET Core.
* Posílání zpráv pro připojené klienty z rozhraní .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="enable-signalr-in-startup"></a>Povolit SignalR při spuštění

::: moniker range=">= aspnetcore-3.0"

Hostování ASP.NET Corech SignalR Center v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci. V `Startup.ConfigureServices` metodě volání `services.AddSignalR` přidá požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di) do podpory. SignalR V `Startup.Configure`je `MapHub` metoda volána ve `UseEndpoints` zpětném volání pro připojení koncových bodů centra v kanálu žádosti ASP.NET Core.

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

Hostování ASP.NET Corech SignalR Center v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci. V `Startup.ConfigureServices` metodě volání `services.AddSignalR` přidá požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di) do podpory. SignalR V `Startup.Configure`nástroji je `UseSignalR` metoda volána pro připojení koncových bodů centra v kanálu žádosti ASP.NET Core.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

V předchozím příkladu `ClockHub` třída implementuje `Hub<T>` třídu pro vytvoření rozbočovače silného typu. Ve třídě byl nakonfigurován tak, aby odpovídal na požadavky na koncovém bodu `/hubs/clock` `ClockHub` `Startup`

Další informace o rozbočovačích se silnými typy najdete v tématu [použití Center v SignalR pro ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Tato funkce není omezená na třídu [>\<centra t](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Budou fungovat i všechny třídy, které dědí z [rozbočovače](xref:Microsoft.AspNetCore.SignalR.Hub), například [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub).

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

Rozhraní používané silným typem `ClockHub` je `IClock` rozhraní.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Volání SignalR centra ze služby na pozadí

Při spuštění je `Worker` třída `BackgroundService`povolena pomocí. `AddHostedService`

```csharp
services.AddHostedService<Worker>();
```

Vzhledem SignalR k tomu, že je zároveň `Startup` zapnutá i během fáze, kde je každé centrum připojené k jednotlivému koncovému bodu v kanálu požadavků HTTP ASP.NET Core, je každé `IHubContext<T>` centrum reprezentované na serveru. Pomocí funkcí DI ASP.NET Core, jiné třídy, které jsou vytvořeny pomocí hostující vrstvy, jako `BackgroundService` jsou třídy, třídy kontroleru MVC nebo Razor modely stránek, mohou získat odkazy na centra na straně serveru tím, že přijímají instance `IHubContext<ClockHub, IClock>` během konstrukce.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Jak je `ExecuteAsync` metoda volána iterativním způsobem ve službě na pozadí, je aktuální datum a čas serveru odesíláno připojeným klientům pomocí `ClockHub`.

## <a name="react-to-signalr-events-with-background-services"></a>Reakce na SignalR události se službami na pozadí

Podobně jako jedna stránková aplikace, která používá klienta SignalR JavaScriptu pro nebo <xref:signalr/dotnet-client>desktopovou aplikaci .NET, může pomocí implementace použít, `BackgroundService` a `IHostedService` nebo použít implementaci, nebo taky pro připojení SignalR k centrům a reakci na události.

`ClockHubClient` Třída implementuje `IClock` rozhraní i `IHostedService` rozhraní. Tímto způsobem je možné tuto možnost povolit `Startup` , aby běžela nepřetržitě a reagovala na události centra ze serveru.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Během inicializace `ClockHubClient` vytvoří instanci instance `HubConnection` a a povolí `IClock.ShowTime` metodu jako obslužnou rutinu pro `ShowTime` událost centra.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

V `IHostedService.StartAsync` implementaci se modul `HubConnection` spouští asynchronně.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Během `IHostedService.StopAsync` metody `HubConnection` je vyřazení asynchronně.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Další zdroje

* [Začínáme](xref:tutorials/signalr)
* [Centra](xref:signalr/hubs)
* [Publikování aplikací do Azure](xref:signalr/publish-to-azure-web-app)
* [Rozbočovače silného typu](xref:signalr/hubs#strongly-typed-hubs)
