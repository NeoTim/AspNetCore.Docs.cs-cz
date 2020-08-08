---
title: SignalRHubContext
author: bradygaster
description: Naučte se používat službu ASP.NET Core SignalR HubContext pro posílání oznámení klientům mimo centrum.
monikerRange: '>= aspnetcore-2.1'
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
uid: signalr/hubcontext
ms.openlocfilehash: b9adc54c1928d6ec11f707b2bd5e1e297973f1ae
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021923"
---
# <a name="send-messages-from-outside-a-hub"></a>Posílání zpráv mimo centrum

Od [Mikael Mengistu](https://twitter.com/MikaelM_12)

SignalRRozbočovač je základní abstrakce pro posílání zpráv klientům připojeným k SignalR serveru. Je také možné odesílat zprávy z jiných míst ve vaší aplikaci pomocí `IHubContext` služby. Tento článek vysvětluje, jak získat přístup k, aby bylo možné SignalR `IHubContext` odesílat oznámení klientům mimo centrum.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>Získat instanci IHubContext

V ASP.NET Core SignalR máte přístup k instanci `IHubContext` přes vkládání závislostí. Můžete vložit instanci `IHubContext` do kontroleru, middleware nebo jiné služby di. Použijte instanci k posílání zpráv klientům.

> [!NOTE]
> To se liší od ASP.NET 4. x SignalR , které používaly GlobalHost k poskytnutí přístupu k `IHubContext` . ASP.NET Core má rozhraní pro vkládání závislostí, které odstraňuje nutnost tohoto globálního typu singleton.

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>Vložení instance IHubContext do kontroleru

Můžete vložit instanci `IHubContext` do kontroleru tak, že ji přidáte do konstruktoru:

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

Nyní s přístupem k instanci nástroje `IHubContext` můžete volat metody centra jako v případě, že jste byli v samotném centru.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>Získání instance IHubContext v middlewaru

Přístup k v `IHubContext` rámci kanálu middlewaru vám umožní:

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> Když jsou metody rozbočovače volány z vnějšku `Hub` třídy, není k vyvolání přidružen žádný volající. Proto neexistuje přístup k `ConnectionId` `Caller` `Others` vlastnostem, a.

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a>Získat instanci IHubContext z IHost

Přístup k `IHubContext` webu z webového hostitele je užitečný pro integraci s oblastmi mimo ASP.NET Core, například pomocí rozhraní pro vkládání závislostí od jiných výrobců:

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a>Vložení silně typovaného HubContextu

Pro vložení silně typovaného HubContextu zajistěte, aby váš rozbočovač dědil z `Hub<T>` . `IHubContext<THub, T>`Místo použijte rozhraní `IHubContext<THub>` .

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
