---
title: Podpora websockets v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak začít s WebSockets v ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: a8040003374906fd93e12c9fde44c4a5ccc2cf37
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655783"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="3fc3f-103">Podpora websockets v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fc3f-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="3fc3f-104">Tom [Dykstra](https://github.com/tdykstra) a [Andrew Stanton-Sestra](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="3fc3f-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="3fc3f-105">Tento článek vysvětluje, jak začít s WebSockets v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="3fc3f-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) je protokol, který umožňuje obousměrné trvalé komunikační kanály přes připojení TCP.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="3fc3f-107">Používá se v aplikacích, které využívají rychlou komunikaci v reálném čase, jako je chat, řídicí panel a herní aplikace.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="3fc3f-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3fc3f-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="3fc3f-109">[Jak spustit](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="3fc3f-109">[How to run](#sample-app).</span></span>

## <a name="signalr"></a><span data-ttu-id="3fc3f-110">SignalR</span><span class="sxs-lookup"><span data-stu-id="3fc3f-110">SignalR</span></span>

<span data-ttu-id="3fc3f-111">[ASP.NET Core SignalR](xref:signalr/introduction) je knihovna, která zjednodušuje přidávání webových funkcí v reálném čase do aplikací.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-111">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="3fc3f-112">Používá WebSockets, kdykoli je to možné.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-112">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="3fc3f-113">Pro většinu aplikací doporučujeme SignalR přes raw WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-113">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="3fc3f-114">SignalR poskytuje přenos záložní pro prostředí, kde WebSockets není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-114">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="3fc3f-115">Poskytuje také jednoduchý model aplikace vzdáleného volání procedur.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-115">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="3fc3f-116">A ve většině scénářů SignalR nemá žádné významné nevýhody výkonu ve srovnání s použitím raw WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-116">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3fc3f-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3fc3f-117">Prerequisites</span></span>

* <span data-ttu-id="3fc3f-118">ASP.NET jádro 1.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="3fc3f-118">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="3fc3f-119">Jakýkoli operační operační spoje, který podporuje ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-119">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="3fc3f-120">Windows 7 / Windows Server 2008 nebo novější</span><span class="sxs-lookup"><span data-stu-id="3fc3f-120">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="3fc3f-121">Linux</span><span class="sxs-lookup"><span data-stu-id="3fc3f-121">Linux</span></span>
  * <span data-ttu-id="3fc3f-122">macOS</span><span class="sxs-lookup"><span data-stu-id="3fc3f-122">macOS</span></span>
  
* <span data-ttu-id="3fc3f-123">Pokud aplikace běží ve Windows se službou IIS:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-123">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="3fc3f-124">Windows 8 / Windows Server 2012 nebo novější</span><span class="sxs-lookup"><span data-stu-id="3fc3f-124">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="3fc3f-125">IIS 8 / IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="3fc3f-125">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="3fc3f-126">WebSockets musí být povolena (viz část [podpory služby IIS/IIS Express.).](#iisiis-express-support)</span><span class="sxs-lookup"><span data-stu-id="3fc3f-126">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="3fc3f-127">Pokud aplikace běží na [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="3fc3f-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="3fc3f-128">Windows 8 / Windows Server 2012 nebo novější</span><span class="sxs-lookup"><span data-stu-id="3fc3f-128">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="3fc3f-129">Podporované prohlížeče naleznete https://caniuse.com/#feat=websocketsv tématu .</span><span class="sxs-lookup"><span data-stu-id="3fc3f-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="3fc3f-130">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="3fc3f-130">NuGet package</span></span>

<span data-ttu-id="3fc3f-131">Nainstalujte balíček [Microsoft.AspNetCore.WebSockets.](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/)</span><span class="sxs-lookup"><span data-stu-id="3fc3f-131">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="3fc3f-132">Konfigurace middlewaru</span><span class="sxs-lookup"><span data-stu-id="3fc3f-132">Configure the middleware</span></span>


<span data-ttu-id="3fc3f-133">Přidejte middleware WebSockets `Configure` do `Startup` metody třídy:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-133">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3fc3f-134">Lze nakonfigurovat následující nastavení:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-134">The following settings can be configured:</span></span>

* <span data-ttu-id="3fc3f-135">`KeepAliveInterval`- Jak často posílat "ping" rámce klientovi, aby proxy udržet připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-135">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="3fc3f-136">Výchozí hodnota je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-136">The default is two minutes.</span></span>
* <span data-ttu-id="3fc3f-137">`ReceiveBufferSize`- Velikost vyrovnávací paměti používané pro příjem dat.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-137">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="3fc3f-138">Pokročilí uživatelé může být nutné změnit pro optimalizaci výkonu na základě velikosti dat.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-138">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="3fc3f-139">Výchozí hodnota je 4 KB.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-139">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3fc3f-140">Lze nakonfigurovat následující nastavení:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-140">The following settings can be configured:</span></span>

* <span data-ttu-id="3fc3f-141">`KeepAliveInterval`- Jak často posílat "ping" rámce klientovi, aby proxy udržet připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="3fc3f-142">Výchozí hodnota je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-142">The default is two minutes.</span></span>
* <span data-ttu-id="3fc3f-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>- Velikost vyrovnávací paměti používané pro příjem dat.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="3fc3f-144">Pokročilí uživatelé může být nutné změnit pro optimalizaci výkonu na základě velikosti dat.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-144">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="3fc3f-145">Výchozí hodnota je 4 KB.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-145">The default is 4 KB.</span></span>
* <span data-ttu-id="3fc3f-146">`AllowedOrigins`- Seznam povolených hodnot hlaviček Origin pro požadavky WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-146">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="3fc3f-147">Ve výchozím nastavení jsou povoleny všechny počátky.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-147">By default, all origins are allowed.</span></span> <span data-ttu-id="3fc3f-148">Podrobnosti naleznete níže v části "Omezení původu websocketu".</span><span class="sxs-lookup"><span data-stu-id="3fc3f-148">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="3fc3f-149">Přijmout požadavky websocketu</span><span class="sxs-lookup"><span data-stu-id="3fc3f-149">Accept WebSocket requests</span></span>

<span data-ttu-id="3fc3f-150">Někde později v životním cyklu `Configure` požadavku (později v metodě nebo v metodě akce, například) zkontrolujte, zda se jedná o požadavek WebSocket a přijměte požadavek WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-150">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="3fc3f-151">Následující příklad je z `Configure` později v metodě:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-151">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="3fc3f-152">Požadavek WebSocket může přijít na libovolnou adresu URL, ale `/ws`tento ukázkový kód přijímá pouze požadavky pro .</span><span class="sxs-lookup"><span data-stu-id="3fc3f-152">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="3fc3f-153">Při použití WebSocket, **je nutné** zachovat middleware kanálu spuštěna po dobu trvání připojení.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-153">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="3fc3f-154">Pokud se pokusíte odeslat nebo přijmout zprávu WebSocket po ukončení kanálu middlewaru, může se zobrazit výjimka, jako je následující:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-154">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="3fc3f-155">Pokud používáte službu na pozadí k zápisu dat do WebSocket, ujistěte se, že zachovat middleware kanálu spuštěna.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-155">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="3fc3f-156">Proveďte to <xref:System.Threading.Tasks.TaskCompletionSource%601>pomocí .</span><span class="sxs-lookup"><span data-stu-id="3fc3f-156">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="3fc3f-157">Předejte `TaskCompletionSource` službu na pozadí <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> a mít volání po dokončení s WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-157">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="3fc3f-158">Pak `await` <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> vlastnost během požadavku, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-158">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="3fc3f-159">Uzavřená výjimka WebSocket může také dojít, pokud se vrátíte příliš brzy z metody akce.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-159">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="3fc3f-160">Pokud přijmete soket v metodě akce, počkejte na kód, který používá soket k dokončení před návratem z metody akce.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-160">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="3fc3f-161">Nikdy `Task.Wait()`nepoužívejte , `Task.Result`, nebo podobné blokování volání čekat na soket k dokončení, protože to může způsobit vážné problémy s podprocesem.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-161">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="3fc3f-162">Vždy `await`používejte .</span><span class="sxs-lookup"><span data-stu-id="3fc3f-162">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="3fc3f-163">Odesílání a příjem zpráv</span><span class="sxs-lookup"><span data-stu-id="3fc3f-163">Send and receive messages</span></span>

<span data-ttu-id="3fc3f-164">Metoda `AcceptWebSocketAsync` inovuje připojení TCP na připojení WebSocket a poskytuje objekt [WebSocket.](/dotnet/core/api/system.net.websockets.websocket)</span><span class="sxs-lookup"><span data-stu-id="3fc3f-164">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="3fc3f-165">Pomocí `WebSocket` objektu odesílejte a přijímejte zprávy.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-165">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="3fc3f-166">Kód zobrazený dříve, který přijímá požadavek WebSocket předá `WebSocket` objekt metodě. `Echo`</span><span class="sxs-lookup"><span data-stu-id="3fc3f-166">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="3fc3f-167">Kód obdrží zprávu a okamžitě odešle zpět stejnou zprávu.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-167">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="3fc3f-168">Zprávy jsou odesílány a přijímány ve smyčce, dokud klient neukončí připojení:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-168">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="3fc3f-169">Při přijetí připojení WebSocket před zahájením smyčky middleware kanálu končí.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-169">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="3fc3f-170">Po zavření zásuvky se potrubí uvolní.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-170">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="3fc3f-171">To znamená, že požadavek přestane posunout vpřed v kanálu při přijetí WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-171">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="3fc3f-172">Po dokončení smyčky a soketu je uzavřen, požadavek pokračuje zálohovat potrubí.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-172">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="3fc3f-173">Zpracování odpojení klienta</span><span class="sxs-lookup"><span data-stu-id="3fc3f-173">Handle client disconnects</span></span>

<span data-ttu-id="3fc3f-174">Server není automaticky informován, když se klient odpojí z důvodu ztráty připojení.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-174">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="3fc3f-175">Server obdrží zprávu o odpojení pouze v případě, že ji klient odešle, což nelze provést v případě ztráty připojení k Internetu.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-175">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="3fc3f-176">Pokud chcete provést nějakou akci, když k tomu dojde, nastavte časový čas po nic je přijatod klienta v určitém časovém okně.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-176">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="3fc3f-177">Pokud klient není vždy odesílání zpráv a nechcete časový limit jen proto, že připojení přejde nečinnosti, mají klient použít časovač odeslat ping zprávy každých X sekund.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-177">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="3fc3f-178">Pokud zpráva nedorazila do 2\*x sekund po předchozím, ukončete připojení a nahlaste, že se klient odpojil.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-178">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="3fc3f-179">Počkejte na dvojnásobek očekávaného časového intervalu, aby se doba navíc prozpoždění sítě, která by mohla obsahovat zprávu ping.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-179">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="3fc3f-180">Omezení původu websocketu</span><span class="sxs-lookup"><span data-stu-id="3fc3f-180">WebSocket origin restriction</span></span>

<span data-ttu-id="3fc3f-181">Ochrany poskytované CORS se nevztahují na WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-181">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="3fc3f-182">Prohlížeče **nemají**:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-182">Browsers do **not**:</span></span>

* <span data-ttu-id="3fc3f-183">Proveďte předletové požadavky CORS.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-183">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="3fc3f-184">Respektujte omezení `Access-Control` zadaná v záhlaví při vytváření požadavků WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-184">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="3fc3f-185">Prohlížeče však odeslat `Origin` záhlaví při vydávání požadavků WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-185">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="3fc3f-186">Aplikace by měly být nakonfigurovány tak, aby ověřovaly tyto hlavičky, aby bylo zajištěno, že jsou povoleny pouze websockety pocházející z očekávaného původu.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-186">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="3fc3f-187">Pokud hostujete serverhttps://server.comna " " ahttps://client.comhostujete svého klienta na " ", přidejte "https://client.com" do `AllowedOrigins` seznamu pro WebSockets k ověření.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-187">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="3fc3f-188">Záhlaví `Origin` je řízeno klientem a `Referer` stejně jako záhlaví může být zfalšováno.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-188">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="3fc3f-189">**Nepoužívejte** tyto hlavičky jako mechanismus ověřování.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-189">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="3fc3f-190">Podpora služby IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="3fc3f-190">IIS/IIS Express support</span></span>

<span data-ttu-id="3fc3f-191">Windows Server 2012 nebo novější a Windows 8 nebo novější se službou IIS/IIS Express 8 nebo novějším podporují protokol WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-191">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="3fc3f-192">WebSockets jsou vždy povoleny při použití služby IIS Express.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-192">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="3fc3f-193">Povolení webových soketů ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="3fc3f-193">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="3fc3f-194">Povolení podpory protokolu WebSocket v systému Windows Server 2012 nebo novějším:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-194">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="3fc3f-195">Tyto kroky nejsou vyžadovány při použití služby IIS Express</span><span class="sxs-lookup"><span data-stu-id="3fc3f-195">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="3fc3f-196">Použijte Průvodce **přidáním rolí a funkcí** z nabídky **Spravovat** nebo odkaz ve **Správci serveru**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-196">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="3fc3f-197">Vyberte **možnost Instalace založená na rolích nebo na základě funkcí**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-197">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="3fc3f-198">Vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-198">Select **Next**.</span></span>
1. <span data-ttu-id="3fc3f-199">Vyberte příslušný server (místní server je vybrán ve výchozím nastavení).</span><span class="sxs-lookup"><span data-stu-id="3fc3f-199">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="3fc3f-200">Vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-200">Select **Next**.</span></span>
1. <span data-ttu-id="3fc3f-201">Rozbalte **webový server (IIS)** ve stromu **rolí,** rozbalte **webový server**a potom **rozbalte položku Vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-201">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="3fc3f-202">Vyberte **websocketový protokol**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-202">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="3fc3f-203">Vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-203">Select **Next**.</span></span>
1. <span data-ttu-id="3fc3f-204">Pokud další funkce nejsou potřeba, vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-204">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="3fc3f-205">Vyberte **Install** (Nainstalovat).</span><span class="sxs-lookup"><span data-stu-id="3fc3f-205">Select **Install**.</span></span>
1. <span data-ttu-id="3fc3f-206">Po dokončení instalace vyberte **Zavřít** a ukončete průvodce.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-206">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="3fc3f-207">Povolení podpory protokolu WebSocket v systému Windows 8 nebo novějším:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-207">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="3fc3f-208">Tyto kroky nejsou vyžadovány při použití služby IIS Express</span><span class="sxs-lookup"><span data-stu-id="3fc3f-208">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="3fc3f-209">Přejděte na **Ovládací panely** > **Programy** > **a funkce** > **Zapněte nebo vypněte funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="3fc3f-209">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="3fc3f-210">Otevřete následující uzly: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-210">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="3fc3f-211">Vyberte funkci **WebSocket Protocol.**</span><span class="sxs-lookup"><span data-stu-id="3fc3f-211">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="3fc3f-212">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-212">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="3fc3f-213">Zakázat websocket při použití socket.io na soubor Node.js</span><span class="sxs-lookup"><span data-stu-id="3fc3f-213">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="3fc3f-214">Pokud používáte podporu WebSocket v [souboru socket.io](https://socket.io/) na [souboru Node.js](https://nodejs.org/), zakažte výchozí modul IIS WebSocket pomocí prvku v `webSocket` *souboru web.config* nebo *applicationHost.config*. Pokud tento krok není proveden, modul IIS WebSocket se pokusí zpracovat komunikaci WebSocket spíše než Node.js a aplikace.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-214">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="3fc3f-215">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="3fc3f-215">Sample app</span></span>

<span data-ttu-id="3fc3f-216">[Ukázková aplikace,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) která doprovází tento článek je echo aplikace.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-216">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="3fc3f-217">Má webovou stránku, která umožňuje připojení WebSocket a server znovu odešle všechny zprávy, které obdrží zpět klientovi.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-217">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="3fc3f-218">Spusťte aplikaci z příkazového řádku (není nastavena na spuštění z visual http://localhost:5000studia pomocí iis express) a přejděte na .</span><span class="sxs-lookup"><span data-stu-id="3fc3f-218">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="3fc3f-219">Na webové stránce se zobrazuje stav připojení v levém horním rohu:</span><span class="sxs-lookup"><span data-stu-id="3fc3f-219">The web page shows the connection status in the upper left:</span></span>

![Počáteční stav webové stránky](websockets/_static/start.png)

<span data-ttu-id="3fc3f-221">Vyberte **Připojit,** chcete-li odeslat požadavek WebSocket na zobrazenou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-221">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="3fc3f-222">Zadejte testovací zprávu a vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-222">Enter a test message and select **Send**.</span></span> <span data-ttu-id="3fc3f-223">Až bude hotovo, vyberte **Zavřít soket**.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-223">When done, select **Close Socket**.</span></span> <span data-ttu-id="3fc3f-224">Sekce **Protokol komunikace** hlásí každou akci otevření, odeslání a zavření.</span><span class="sxs-lookup"><span data-stu-id="3fc3f-224">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Počáteční stav webové stránky](websockets/_static/end.png)

