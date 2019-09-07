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
# <a name="host-aspnet-core-signalr-in-background-services"></a>Signál hostitelského ASP.NET Core ve službách na pozadí

Od [Brady gastera](https://twitter.com/bradygaster)

Tento článek poskytuje pokyny pro:

* Hostování Center signálů pomocí pracovního procesu na pozadí hostovaného s ASP.NET Core.
* Posílání zpráv pro připojené klienty z rozhraní .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(stažení)](xref:index#how-to-download-a-sample)

## <a name="wire-up-signalr-during-startup"></a>Síťový signál při spuštění

::: moniker range=">= aspnetcore-3.0"

Hostování Center ASP.NET Corech signálů v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci. V metodě volání `services.AddSignalR` přidá k podpoře signálu požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di). `Startup.ConfigureServices` V `Startup.Configure`je metodavolánavezpětnémvolánípropřenoskoncovýchbodůrozbočovačevkanálužádostiASP.NETCore.`MapHub` `UseEndpoints`

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

Hostování Center ASP.NET Corech signálů v kontextu pracovního procesu na pozadí je stejné jako hostování rozbočovače v ASP.NET Core webové aplikaci. V metodě volání `services.AddSignalR` přidá k podpoře signálu požadované služby pro vrstvu ASP.NET Core pro vkládání závislostí (di). `Startup.ConfigureServices` V `Startup.Configure`nástrojije metodavolánapropřenoskoncovýchbodůrozbočovačevkanálužádostiASP.NETCore.`UseSignalR`

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

V předchozím příkladu `ClockHub` třída `Hub<T>` implementuje třídu pro vytvoření rozbočovače silného typu. Ve třídě byl nakonfigurován tak, aby odpovídal na požadavky na koncovém bodu `/hubs/clock`. `ClockHub` `Startup`

Další informace o rozbočovačích se silnými typy najdete v tématu [použití Center v nástroji Signal pro ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Tato funkce není omezená na třídu [>\<centra t](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Budou fungovat i všechny třídy, které dědí z [rozbočovače](xref:Microsoft.AspNetCore.SignalR.Hub), například [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub).

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

Rozhraní používané silným typem `ClockHub` `IClock` je rozhraní.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Volání centra signalizace ze služby na pozadí

Během spouštění `Worker` je třída `BackgroundService`, a, připojená pomocí `AddHostedService`.

```csharp
services.AddHostedService<Worker>();
```

Vzhledem k tomu, že je signál v průběhu `Startup` fáze také zapojený, kdy je každé centrum připojené k jednotlivému koncovému bodu v kanálu požadavků HTTP ASP.NET Core, je každé centrum reprezentované `IHubContext<T>` na serveru. Pomocí funkcí ASP.NET Core di jsou jiné třídy, jejichž instance je vytvořena hostující vrstvou, `BackgroundService` jako jsou třídy, třídy kontroleru MVC nebo modely stránek Razor, schopny získat odkazy na centra na straně serveru tím, `IHubContext<ClockHub, IClock>` že přijímají instance během konstrukce.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Jak je `ClockHub`metoda volána iterativním způsobem ve službě na pozadí, je aktuální datum a čas serveru odesíláno připojeným klientům pomocí. `ExecuteAsync`

## <a name="react-to-signalr-events-with-background-services"></a>Reakce na události signalizace pomocí služeb na pozadí

Podobně jako jedna stránková aplikace, která používá klienta JavaScriptu pro Signal nebo desktopovou aplikaci .NET <xref:signalr/dotnet-client> `BackgroundService` , se dá použít k připojení k centrům signálů a k reagování na události pomocí implementace, a nebo `IHostedService` .

Třída implementuje `IClock` rozhraní i`IHostedService` rozhraní. `ClockHubClient` Tímto způsobem je možné během `Startup` nepřetržitého chodu a reakce na události centra ze serveru pracovat.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Během inicializace `ClockHubClient` vytvoří instance `HubConnection` a vodiče `IClock.ShowTime` `ShowTime` metodu jako obslužnou rutinu pro událost centra.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

V implementaci se modul `HubConnection` spouští asynchronně. `IHostedService.StartAsync`

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

`IHostedService.StopAsync` Během metody `HubConnection` je vyřazení asynchronně.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Další zdroje

* [Začínáme](xref:tutorials/signalr)
* [Centra](xref:signalr/hubs)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
* [Rozbočovače silného typu](xref:signalr/hubs#strongly-typed-hubs)
