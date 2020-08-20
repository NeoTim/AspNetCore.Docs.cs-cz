---
title: SignalR HubContext
author: bradygaster
description: Naučte se používat službu ASP.NET Core SignalR HubContext pro posílání oznámení klientům mimo centrum.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: c6a4926be008feb2c9b708c56597070b96d8bd3f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633016"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="bba9f-103">Posílání zpráv mimo centrum</span><span class="sxs-lookup"><span data-stu-id="bba9f-103">Send messages from outside a hub</span></span>

<span data-ttu-id="bba9f-104">Od [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="bba9f-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="bba9f-105">SignalRRozbočovač je základní abstrakce pro posílání zpráv klientům připojeným k SignalR serveru.</span><span class="sxs-lookup"><span data-stu-id="bba9f-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="bba9f-106">Je také možné odesílat zprávy z jiných míst ve vaší aplikaci pomocí `IHubContext` služby.</span><span class="sxs-lookup"><span data-stu-id="bba9f-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="bba9f-107">Tento článek vysvětluje, jak získat přístup k, aby bylo možné SignalR `IHubContext` odesílat oznámení klientům mimo centrum.</span><span class="sxs-lookup"><span data-stu-id="bba9f-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="bba9f-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="bba9f-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="bba9f-109">Získat instanci IHubContext</span><span class="sxs-lookup"><span data-stu-id="bba9f-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="bba9f-110">V ASP.NET Core SignalR máte přístup k instanci `IHubContext` přes vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="bba9f-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="bba9f-111">Můžete vložit instanci `IHubContext` do kontroleru, middleware nebo jiné služby di.</span><span class="sxs-lookup"><span data-stu-id="bba9f-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="bba9f-112">Použijte instanci k posílání zpráv klientům.</span><span class="sxs-lookup"><span data-stu-id="bba9f-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="bba9f-113">To se liší od ASP.NET 4. x SignalR , které používaly GlobalHost k poskytnutí přístupu k `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="bba9f-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="bba9f-114">ASP.NET Core má rozhraní pro vkládání závislostí, které odstraňuje nutnost tohoto globálního typu singleton.</span><span class="sxs-lookup"><span data-stu-id="bba9f-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="bba9f-115">Vložení instance IHubContext do kontroleru</span><span class="sxs-lookup"><span data-stu-id="bba9f-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="bba9f-116">Můžete vložit instanci `IHubContext` do kontroleru tak, že ji přidáte do konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="bba9f-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="bba9f-117">Nyní s přístupem k instanci nástroje `IHubContext` můžete volat metody centra jako v případě, že jste byli v samotném centru.</span><span class="sxs-lookup"><span data-stu-id="bba9f-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="bba9f-118">Získání instance IHubContext v middlewaru</span><span class="sxs-lookup"><span data-stu-id="bba9f-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="bba9f-119">Přístup k v `IHubContext` rámci kanálu middlewaru vám umožní:</span><span class="sxs-lookup"><span data-stu-id="bba9f-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

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
> <span data-ttu-id="bba9f-120">Když jsou metody rozbočovače volány z vnějšku `Hub` třídy, není k vyvolání přidružen žádný volající.</span><span class="sxs-lookup"><span data-stu-id="bba9f-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="bba9f-121">Proto neexistuje přístup k `ConnectionId` `Caller` `Others` vlastnostem, a.</span><span class="sxs-lookup"><span data-stu-id="bba9f-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="bba9f-122">Získat instanci IHubContext z IHost</span><span class="sxs-lookup"><span data-stu-id="bba9f-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="bba9f-123">Přístup k `IHubContext` webu z webového hostitele je užitečný pro integraci s oblastmi mimo ASP.NET Core, například pomocí rozhraní pro vkládání závislostí od jiných výrobců:</span><span class="sxs-lookup"><span data-stu-id="bba9f-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

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

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="bba9f-124">Vložení silně typovaného HubContextu</span><span class="sxs-lookup"><span data-stu-id="bba9f-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="bba9f-125">Pro vložení silně typovaného HubContextu zajistěte, aby váš rozbočovač dědil z `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="bba9f-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="bba9f-126">`IHubContext<THub, T>`Místo použijte rozhraní `IHubContext<THub>` .</span><span class="sxs-lookup"><span data-stu-id="bba9f-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

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

## <a name="related-resources"></a><span data-ttu-id="bba9f-127">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="bba9f-127">Related resources</span></span>

* [<span data-ttu-id="bba9f-128">Začínáme</span><span class="sxs-lookup"><span data-stu-id="bba9f-128">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="bba9f-129">Rozbočovače</span><span class="sxs-lookup"><span data-stu-id="bba9f-129">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="bba9f-130">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="bba9f-130">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
