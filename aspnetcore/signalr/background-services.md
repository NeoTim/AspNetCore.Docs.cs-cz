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
# <a name="host-aspnet-core-signalr-in-background-services"></a>Funkce SignalR technologie ASP.NET Core hostitele služby na pozadí

Podle [Brady Gaster](https://twitter.com/bradygaster)

Tento článek obsahuje pokyny pro:

* Hostování rozbočovače SignalR pomocí pracovní proces na pozadí hostované pomocí ASP.NET Core.
* Zasílání zpráv pro připojení klientů z v rámci .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="wire-up-signalr-during-startup"></a>Nastavit SignalR při spuštění

Hostování rozbočovače SignalR technologie ASP.NET Core v rámci pracovní proces na pozadí se shoduje s hostující Hubu ve webové aplikaci ASP.NET Core. V `Startup.ConfigureServices` metody, volání `services.AddSignalR` přidá k požadovaným službám vrstvu ASP.NET Core Dependency Injection (DI) pro podporu systému SignalR. V `Startup.Configure`, `UseSignalR` metoda je volána k přenosu do centra koncových bodů v kanálu požadavku ASP.NET Core.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

V předchozím příkladu `ClockHub` implementuje třída `Hub<T>` třídy za účelem vytvoření rozbočovač silného typu. `ClockHub` Nakonfigurovanou `Startup` třídy reagovat na požadavky na koncovém bodu `/hubs/clock`.

Další informace o silného typu rozbočovače, naleznete v tématu [použití rozbočovače signalr pro ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Tato funkce se neomezuje na [centra\<T >](xref:Microsoft.AspNetCore.SignalR.Hub`1) třídy. Všechny třídy, která dědí z [centra](xref:Microsoft.AspNetCore.SignalR.Hub), jako například [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), budou fungovat i.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

Rozhraní používá silného typu `ClockHub` je `IClock` rozhraní.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Volání rozbočovače SignalR ze služby na pozadí

Při spuštění `Worker` třídy, `BackgroundService`, je připraveno, pomocí `AddHostedService`.

```csharp
services.AddHostedService<Worker>();
```

Protože SignalR je také připraveno, během `Startup` fáze, ve kterém každé centrum je připojen k jednotlivé koncový bod v ASP.NET Core kanál požadavků HTTP, je reprezentována každé centrum `IHubContext<T>` na serveru. Pomocí ASP.NET Core nabízí DI, dalším třídám, mohl vytvořit jeho instanci hostující vrstvy, jako je třeba `BackgroundService` tříd, kontroler MVC a modely stránky Razor, lze získat odkazy na rozbočovače na straně serveru tak, že přijímá instance `IHubContext<ClockHub, IClock>` během konstrukce.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Jako `ExecuteAsync` metoda je volána zavádět postupně ve službě na pozadí, aktuální datum a čas na serveru se odesílají připojeným klientům pomocí `ClockHub`.

## <a name="react-to-signalr-events-with-background-services"></a>Reagujte na události SignalR s služby na pozadí

Stejně jako jednotlivé stránky aplikace pomocí jazyka JavaScript klienta pro funkci SignalR nebo desktopové aplikace .NET, můžete provést pomocí <xref:signalr/dotnet-client>, `BackgroundService` nebo `IHostedService` implementace lze také použít k připojení k rozbočovačům SignalR a reagovat na události.

`ClockHubClient` Třída implementuje oba `IClock` rozhraní a `IHostedService` rozhraní. Tímto způsobem ji můžou být připraveno, během `Startup` spouštět nepřetržitě a reagovat na události v centru ze serveru. 

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Během inicializace `ClockHubClient` vytvoří instanci `HubConnection` a sváže `IClock.ShowTime` metody jako obslužnou rutinu pro centra `ShowTime` událostí.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

V `IHostedService.StartAsync` implementace, `HubConnection` se spustí asynchronně.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Během `IHostedService.StopAsync` metody `HubConnection` vyřazen asynchronně.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Další zdroje

* [Začínáme](xref:tutorials/signalr)
* [Centra](xref:signalr/hubs)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
* [Silného typu rozbočovače](xref:signalr/hubs#strongly-typed-hubs)
