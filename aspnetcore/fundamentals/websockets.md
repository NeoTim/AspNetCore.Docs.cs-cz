---
title: Podpora WebSockets v ASP.NET Core
author: rick-anderson
description: Naučte se, jak začít s objekty WebSockets v ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: fundamentals/websockets
ms.openlocfilehash: 5d4d9b02bd45e6650aa56448a3663cad06b3b45e
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975445"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="7ce8a-103">Podpora WebSockets v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ce8a-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="7ce8a-104">Tím, že [Dykstra](https://github.com/tdykstra) a [Andrew Stanton – sestry](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="7ce8a-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="7ce8a-105">Tento článek vysvětluje, jak začít s objekty WebSockets v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="7ce8a-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) je protokol, který umožňuje obousměrné trvalé komunikační kanály přes připojení TCP.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="7ce8a-107">Používá se v aplikacích, které využívají rychlou komunikaci v reálném čase, jako jsou konverzace, řídicí panely a herní aplikace.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="7ce8a-108">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7ce8a-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="7ce8a-109">[Jak spustit](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="7ce8a-109">[How to run](#sample-app).</span></span>

## <a name="signalr"></a><span data-ttu-id="7ce8a-110">SignalR</span><span class="sxs-lookup"><span data-stu-id="7ce8a-110">SignalR</span></span>

<span data-ttu-id="7ce8a-111">[ASP.NET Core Signal](xref:signalr/introduction) je knihovna, která zjednodušuje přidávání webových funkcí v reálném čase do aplikací.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-111">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="7ce8a-112">Pokud je to možné, používá objekty WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-112">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="7ce8a-113">Pro většinu aplikací doporučujeme signalizaci pro nezpracované objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-113">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="7ce8a-114">Signál poskytuje záložní přenos pro prostředí, kde nejsou k dispozici objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-114">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="7ce8a-115">Poskytuje také jednoduchý model aplikace pro vzdálené volání procedur.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-115">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="7ce8a-116">Ve většině scénářů nemá Signal v porovnání s nezpracovanými objekty WebSocket žádný významný nevýhodný výkon.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-116">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7ce8a-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="7ce8a-117">Prerequisites</span></span>

* <span data-ttu-id="7ce8a-118">ASP.NET Core 1,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="7ce8a-118">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="7ce8a-119">Libovolný operační systém, který podporuje ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-119">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="7ce8a-120">Windows 7/Windows Server 2008 nebo novější</span><span class="sxs-lookup"><span data-stu-id="7ce8a-120">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="7ce8a-121">Linux</span><span class="sxs-lookup"><span data-stu-id="7ce8a-121">Linux</span></span>
  * <span data-ttu-id="7ce8a-122">macOS</span><span class="sxs-lookup"><span data-stu-id="7ce8a-122">macOS</span></span>
  
* <span data-ttu-id="7ce8a-123">Pokud aplikace běží ve Windows se službou IIS:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-123">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="7ce8a-124">Windows 8/Windows Server 2012 nebo novější</span><span class="sxs-lookup"><span data-stu-id="7ce8a-124">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="7ce8a-125">IIS 8 / IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="7ce8a-125">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="7ce8a-126">Je nutné povolit objekty WebSockets (viz část [Podpora služby IIS/IIS Express](#iisiis-express-support) .).</span><span class="sxs-lookup"><span data-stu-id="7ce8a-126">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="7ce8a-127">Pokud aplikace běží na [http. sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="7ce8a-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="7ce8a-128">Windows 8/Windows Server 2012 nebo novější</span><span class="sxs-lookup"><span data-stu-id="7ce8a-128">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="7ce8a-129">Podporované prohlížeče najdete v tématu https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="7ce8a-130">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="7ce8a-130">NuGet package</span></span>

<span data-ttu-id="7ce8a-131">Nainstalujte balíček [Microsoft. AspNetCore.](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-131">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="7ce8a-132">Konfigurace middlewaru</span><span class="sxs-lookup"><span data-stu-id="7ce8a-132">Configure the middleware</span></span>


<span data-ttu-id="7ce8a-133">Do `Configure` metody`Startup` třídy přidejte middleware WebSockets:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-133">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7ce8a-134">Je možné nakonfigurovat následující nastavení:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-134">The following settings can be configured:</span></span>

* <span data-ttu-id="7ce8a-135">`KeepAliveInterval`– Jak často se mají do klienta odesílat snímky příkazu "testovat z příkazového testu", aby proxy servery udržovaly otevřené připojení.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-135">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="7ce8a-136">Výchozí hodnota je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-136">The default is two minutes.</span></span>
* <span data-ttu-id="7ce8a-137">`ReceiveBufferSize`– Velikost vyrovnávací paměti použité k příjmu dat.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-137">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="7ce8a-138">Pokročilí uživatelé tuto změnu můžou potřebovat pro vyladění výkonu na základě velikosti dat.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-138">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="7ce8a-139">Výchozí hodnota je 4 KB.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-139">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7ce8a-140">Je možné nakonfigurovat následující nastavení:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-140">The following settings can be configured:</span></span>

* <span data-ttu-id="7ce8a-141">`KeepAliveInterval`– Jak často se mají do klienta odesílat snímky příkazu "testovat z příkazového testu", aby proxy servery udržovaly otevřené připojení.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="7ce8a-142">Výchozí hodnota je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-142">The default is two minutes.</span></span>
* <span data-ttu-id="7ce8a-143">`ReceiveBufferSize`– Velikost vyrovnávací paměti použité k příjmu dat.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-143">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="7ce8a-144">Pokročilí uživatelé tuto změnu můžou potřebovat pro vyladění výkonu na základě velikosti dat.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-144">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="7ce8a-145">Výchozí hodnota je 4 KB.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-145">The default is 4 KB.</span></span>
* <span data-ttu-id="7ce8a-146">`AllowedOrigins`– Seznam povolených hodnot záhlaví zdroje pro požadavky protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-146">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="7ce8a-147">Ve výchozím nastavení jsou povoleny všechny zdroje.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-147">By default, all origins are allowed.</span></span> <span data-ttu-id="7ce8a-148">Podrobnosti najdete níže v části omezení původu pro WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-148">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="7ce8a-149">Přijmout požadavky protokolu WebSocket</span><span class="sxs-lookup"><span data-stu-id="7ce8a-149">Accept WebSocket requests</span></span>

<span data-ttu-id="7ce8a-150">Někam později v životním cyklu požadavku (například později v `Configure` metodě nebo v metodě akce) ověřte, zda se jedná o požadavek protokolu WebSocket a přijmout požadavek protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-150">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="7ce8a-151">Následující příklad je z níže v `Configure` metodě:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-151">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="7ce8a-152">Požadavek protokolu WebSocket by mohl být na jakékoli adrese URL, ale tento ukázkový kód přijímá pouze požadavky `/ws`na.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-152">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="7ce8a-153">Při použití objektu WebSocket je **nutné** , aby byl kanál middleware spuštěn po dobu trvání připojení.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-153">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="7ce8a-154">Pokud se pokusíte odeslat nebo přijmout zprávu protokolu WebSocket po ukončení kanálu middleware, může se zobrazit výjimka podobná následující:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-154">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="7ce8a-155">Pokud k zápisu dat do objektu WebSocket používáte službu na pozadí, ujistěte se, že je kanál middleware spuštěný.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-155">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="7ce8a-156">Použijte k <xref:System.Threading.Tasks.TaskCompletionSource%601>tomu.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-156">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="7ce8a-157">Předejte službě <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> `TaskCompletionSource` na pozadí a zavolejte ji, až skončíte s WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-157">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="7ce8a-158">Pak `await`vlastnostběhemžádosti ,jakjeznázorněnovnásledujícímpříkladu:<xref:System.Threading.Tasks.TaskCompletionSource%601.Task></span><span class="sxs-lookup"><span data-stu-id="7ce8a-158">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="7ce8a-159">Uzavřená výjimka WebSocket se může vyskytnout také v případě, že se vrátí příliš brzy od metody Action.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-159">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="7ce8a-160">Pokud v metodě akce přijmete soket, počkejte, než se před vrácením z metody Action dokončí kód, který používá soket.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-160">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="7ce8a-161">Nikdy `Task.Wait()`Nepoužívejte `Task.Result`, nebo podobná blokující volání pro čekání na dokončení soketu, což může způsobit vážné problémy s vlákny.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-161">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="7ce8a-162">Vždy použít `await`.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-162">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="7ce8a-163">Odesílání a příjem zpráv</span><span class="sxs-lookup"><span data-stu-id="7ce8a-163">Send and receive messages</span></span>

<span data-ttu-id="7ce8a-164">Metoda upgraduje připojení TCP na připojení protokolem WebSocket a poskytuje objekt [WebSocket.](/dotnet/core/api/system.net.websockets.websocket) `AcceptWebSocketAsync`</span><span class="sxs-lookup"><span data-stu-id="7ce8a-164">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="7ce8a-165">`WebSocket` Pomocí objektu můžete odesílat a přijímat zprávy.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-165">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="7ce8a-166">Kód uvedený výše, který přijímá požadavek protokolu WebSocket, předá `WebSocket` objekt `Echo` metodě.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-166">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="7ce8a-167">Kód obdrží zprávu a ihned pošle zpět stejnou zprávu.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-167">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="7ce8a-168">Zprávy jsou odesílány a přijímány ve smyčce, dokud klient neukončí připojení:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-168">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="7ce8a-169">Když přijmete připojení protokolu WebSocket před zahájením smyčky, kanál middleware skončí.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-169">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="7ce8a-170">Po zavření soketu se kanál odvíjí.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-170">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="7ce8a-171">To znamená, že požadavek se při přijetí protokolu WebSocket zastaví v kanálu.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-171">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="7ce8a-172">Když je smyčka dokončená a soket je uzavřený, požadavek pokračuje v zálohování kanálu.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-172">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="7ce8a-173">Popisovač odpojení klienta</span><span class="sxs-lookup"><span data-stu-id="7ce8a-173">Handle client disconnects</span></span>

<span data-ttu-id="7ce8a-174">Pokud se klient odpojí z důvodu ztráty připojení, server se automaticky neinformuje.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-174">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="7ce8a-175">Server obdrží zprávu odpojení pouze v případě, že ji klient pošle, a pokud dojde ke ztrátě připojení k Internetu.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-175">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="7ce8a-176">Pokud chcete provést nějakou akci, když k tomu dojde, nastavte časový limit po nedoručení od klienta do určitého časového období.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-176">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="7ce8a-177">Pokud klient vždy neposílá zprávy a nechcete, aby vypršel časový limit, protože připojení se nečinný, musí klient použít časovač k odeslání zprávy s příkazem if každých X sekund.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-177">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="7ce8a-178">Pokud se na serveru nepřišla zpráva do 2\*X sekund po předchozím konci, ukončí se připojení a oznámí, že se klient odpojil.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-178">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="7ce8a-179">Počkejte na dvojnásobek očekávaného časového intervalu pro prodlevu při zpoždění sítě, která by mohla obsahovat zprávu s upozorněním na test.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-179">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="7ce8a-180">Omezení původu protokolu WebSocket</span><span class="sxs-lookup"><span data-stu-id="7ce8a-180">WebSocket origin restriction</span></span>

<span data-ttu-id="7ce8a-181">Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-181">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="7ce8a-182">Prohlížeče neobsahují:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-182">Browsers do **not**:</span></span>

* <span data-ttu-id="7ce8a-183">Provádění požadavků CORS v řádu CORS</span><span class="sxs-lookup"><span data-stu-id="7ce8a-183">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="7ce8a-184">Respektujte omezení zadaná v `Access-Control` hlavičkách při vytváření požadavků protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-184">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="7ce8a-185">Prohlížeče ale při vydávání požadavků protokolu `Origin` WebSocket odesílají hlavičku.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-185">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="7ce8a-186">Aplikace by měly být nakonfigurovány pro ověření těchto hlaviček, aby bylo zajištěno, že budou povoleny pouze objekty WebSockets přicházející z očekávaných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-186">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="7ce8a-187">Pokud váš server https://server.com hostuje "" a hostující klienta https://client.com v "", `AllowedOrigins` přidejte "https://client.com" do seznamu pro objekty WebSockets, které chcete ověřit.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-187">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="7ce8a-188">Záhlaví řídí klient a, `Referer` podobně jako záhlaví, může být falešným. `Origin`</span><span class="sxs-lookup"><span data-stu-id="7ce8a-188">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="7ce8a-189">Nepoužívejte tyto hlavičky jako ověřovací mechanismus.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-189">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="7ce8a-190">Podpora služby IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="7ce8a-190">IIS/IIS Express support</span></span>

<span data-ttu-id="7ce8a-191">Windows Server 2012 nebo novější a Windows 8 nebo novější se službou IIS/IIS Express 8 nebo novějším má podporu protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-191">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="7ce8a-192">Při použití IIS Express jsou objekty WebSocket vždy povolené.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-192">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="7ce8a-193">Povolování WebSockets ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="7ce8a-193">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="7ce8a-194">Povolení podpory protokolu WebSocket v systému Windows Server 2012 nebo novějším:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-194">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="7ce8a-195">Při použití IIS Express se tyto kroky nevyžadují.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-195">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="7ce8a-196">Použití **přidat role a funkce** z průvodce **spravovat** nabídky nebo na odkaz v **správce serveru**.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-196">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="7ce8a-197">Vyberte **instalaci na základě rolí nebo na základě funkcí**.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-197">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="7ce8a-198">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-198">Select **Next**.</span></span>
1. <span data-ttu-id="7ce8a-199">Vyberte příslušný server (ve výchozím nastavení je místní server vybraný).</span><span class="sxs-lookup"><span data-stu-id="7ce8a-199">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="7ce8a-200">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-200">Select **Next**.</span></span>
1. <span data-ttu-id="7ce8a-201">Rozbalte **webový server (IIS)** ve stromu **role** , rozbalte možnost **webový server**a poté rozbalte položku **vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-201">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="7ce8a-202">Vyberte **protokol WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-202">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="7ce8a-203">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-203">Select **Next**.</span></span>
1. <span data-ttu-id="7ce8a-204">Pokud nepotřebujete další funkce, vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-204">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="7ce8a-205">Vyberte **Install** (Nainstalovat).</span><span class="sxs-lookup"><span data-stu-id="7ce8a-205">Select **Install**.</span></span>
1. <span data-ttu-id="7ce8a-206">Až se instalace dokončí, kliknutím na **Zavřít** ukončete průvodce.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-206">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="7ce8a-207">Povolení podpory protokolu WebSocket v systému Windows 8 nebo novějším:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-207">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="7ce8a-208">Při použití IIS Express se tyto kroky nevyžadují.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-208">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="7ce8a-209">Přejděte do **ovládací panely** > **programy** > **programy a funkce** > **zapnout Windows funkce na nebo vypnout** (levé straně obrazovky).</span><span class="sxs-lookup"><span data-stu-id="7ce8a-209">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="7ce8a-210">Otevřete následující uzly: **Internetová informační služba** > funkce pro vývoj aplikací ve webové službě. > </span><span class="sxs-lookup"><span data-stu-id="7ce8a-210">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="7ce8a-211">Vyberte **protokol WebSocket** funkce.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-211">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7ce8a-212">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-212">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="7ce8a-213">Zakázat WebSocket při použití socket.io v Node. js</span><span class="sxs-lookup"><span data-stu-id="7ce8a-213">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="7ce8a-214">Pokud používáte podporu WebSocket v [Socket.IO](https://socket.io/) v [Node. js](https://nodejs.org/), zakažte `webSocket` výchozí modul protokolu WebSocket služby IIS pomocí elementu v souboru *Web. config* nebo *ApplicationHost. config*. Pokud tento krok neprovedete, modul WebSocket služby IIS se pokusí zpracovat komunikaci protokolu WebSocket, nikoli Node. js a aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-214">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="7ce8a-215">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="7ce8a-215">Sample app</span></span>

<span data-ttu-id="7ce8a-216">[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) , která doprovází tento článek, je aplikace s odezvou.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-216">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="7ce8a-217">Má webovou stránku, která zpřístupňuje připojení pomocí protokolu WebSocket, a server znovu odešle všechny zprávy, které obdrží zpátky klientovi.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-217">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="7ce8a-218">Spusťte aplikaci z příkazového řádku (není nastavená tak, aby se spouštěla ze sady Visual Studio s IIS Express), http://localhost:5000 a přejděte na.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-218">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="7ce8a-219">Na webové stránce se zobrazuje stav připojení v levém horním rohu:</span><span class="sxs-lookup"><span data-stu-id="7ce8a-219">The web page shows the connection status in the upper left:</span></span>

![Počáteční stav webové stránky](websockets/_static/start.png)

<span data-ttu-id="7ce8a-221">Vyberte **připojit** a odešlete požadavek protokolu WebSocket na ZOBRAZENOU adresu URL.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-221">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="7ce8a-222">Zadejte zkušební zprávu a vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-222">Enter a test message and select **Send**.</span></span> <span data-ttu-id="7ce8a-223">Po dokončení vyberte **zavřít soket**.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-223">When done, select **Close Socket**.</span></span> <span data-ttu-id="7ce8a-224">Část **protokol komunikace** oznamuje každou akci otevřít, Odeslat a zavřít, když k ní dojde.</span><span class="sxs-lookup"><span data-stu-id="7ce8a-224">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Počáteční stav webové stránky](websockets/_static/end.png)

