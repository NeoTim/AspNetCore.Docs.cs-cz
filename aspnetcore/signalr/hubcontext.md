---
title: SignalR HubContext
author: bradygaster
description: Zjistěte, jak použít službu SignalR HubContext technologie ASP.NET Core pro odesílání oznámení klientům mimo rozbočovač.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2018
uid: signalr/hubcontext
ms.openlocfilehash: 73cf2c9d30ed5e409a75827fdab1f22b20427884
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837933"
---
# <a name="send-messages-from-outside-a-hub"></a>Odeslání zprávy z mimo rozbočovač

Podle [Mikael Mengistu](https://twitter.com/MikaelM_12)

Rozbočovače SignalR je základní abstrakci pro odesílání zpráv do klientů připojených k serveru funkce SignalR. Je také možné odesílat zprávy z jiného místa portálu vaši aplikaci s použitím `IHubContext` služby. Tento článek vysvětluje, jak získat přístup k knihovnou SignalR `IHubContext` k odesílání oznámení klientům mimo rozbočovač.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>Získat instanci IHubContext

V knihovně SignalR technologie ASP.NET Core, můžete přístup k instanci `IHubContext` pomocí vkládání závislostí. Můžete vložit instance `IHubContext` do kontroleru, middleware nebo jiné služby DI. Použijte instanci pro odesílání zpráv do klientů.

> [!NOTE]
> Tím se liší od ASP.NET 4.x SignalR, která používá GlobalHost k poskytnutí přístupu k `IHubContext`. ASP.NET Core má rozhraní injektáž závislostí, které eliminuje potřebu této globální typu singleton.

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>Vložit instance IHubContext v kontroleru

Můžete vložit instance `IHubContext` do kontroleru tak, že ho přidáte do konstruktoru:

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

Nyní, s přístupem k instanci `IHubContext`, jako kdyby byly v centru samotné můžete volat metody rozbočovače.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>Získat instanci IHubContext v middlewaru

Přístup `IHubContext` v rámci kanálu middleware takto:

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<MyHub>>();
    //...
});
```

> [!NOTE]
> Kdy jsou volány metody rozbočovače z mimo `Hub` třídy, neexistuje žádný volající přidružené k vyvolání. Proto neexistuje žádný přístup k `ConnectionId`, `Caller`, a `Others` vlastnosti.

### <a name="inject-a-strongly-typed-hubcontext"></a>Vložit HubContext se silnými typy

Vložit HubContext se silnými typy, ujistěte se centrem dědí z `Hub<T>`. Vložení pomocí `IHubContext<THub, T>` rozhraní spíše než `IHubContext<THub>`.

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Centra](xref:signalr/hubs)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
