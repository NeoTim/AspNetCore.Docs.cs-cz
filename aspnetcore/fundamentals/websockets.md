---
title: Podpora WebSockets v ASP.NET Core
author: rick-anderson
description: Naučte se, jak začít s objekty WebSockets v ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
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
uid: fundamentals/websockets
ms.openlocfilehash: 685e694a3d974a8a51255bdbb83d33459137a3d9
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629012"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="95ea5-103">Podpora WebSockets v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95ea5-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="95ea5-104">Tím, že [Dykstra](https://github.com/tdykstra) a [Andrew Stanton – sestry](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="95ea5-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="95ea5-105">Tento článek vysvětluje, jak začít s objekty WebSockets v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="95ea5-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="95ea5-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) je protokol, který umožňuje obousměrné trvalé komunikační kanály přes připojení TCP.</span><span class="sxs-lookup"><span data-stu-id="95ea5-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="95ea5-107">Používá se v aplikacích, které využívají rychlou komunikaci v reálném čase, jako jsou konverzace, řídicí panely a herní aplikace.</span><span class="sxs-lookup"><span data-stu-id="95ea5-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="95ea5-108">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="95ea5-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="95ea5-109">[Jak spustit](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="95ea5-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="95ea5-110">[ASP.NET Core SignalR ](xref:signalr/introduction) je knihovna, která zjednodušuje přidávání webových funkcí v reálném čase do aplikací.</span><span class="sxs-lookup"><span data-stu-id="95ea5-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="95ea5-111">Pokud je to možné, používá objekty WebSocket.</span><span class="sxs-lookup"><span data-stu-id="95ea5-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="95ea5-112">Pro většinu aplikací doporučujeme používat SignalR nezpracované objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="95ea5-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="95ea5-113">SignalR poskytuje záložní přenos pro prostředí, kde nejsou k dispozici objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="95ea5-113">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="95ea5-114">Poskytuje také jednoduchý model aplikace pro vzdálené volání procedur.</span><span class="sxs-lookup"><span data-stu-id="95ea5-114">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="95ea5-115">Ve většině scénářů SignalR nemá v porovnání s nezpracovanými objekty WebSocket žádný významný nevýhodný výkon.</span><span class="sxs-lookup"><span data-stu-id="95ea5-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="95ea5-116">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="95ea5-116">Prerequisites</span></span>

* <span data-ttu-id="95ea5-117">ASP.NET Core 1,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="95ea5-117">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="95ea5-118">Libovolný operační systém, který podporuje ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="95ea5-118">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="95ea5-119">Windows 7/Windows Server 2008 nebo novější</span><span class="sxs-lookup"><span data-stu-id="95ea5-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="95ea5-120">Linux</span><span class="sxs-lookup"><span data-stu-id="95ea5-120">Linux</span></span>
  * <span data-ttu-id="95ea5-121">macOS</span><span class="sxs-lookup"><span data-stu-id="95ea5-121">macOS</span></span>
  
* <span data-ttu-id="95ea5-122">Pokud aplikace běží ve Windows se službou IIS:</span><span class="sxs-lookup"><span data-stu-id="95ea5-122">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="95ea5-123">Windows 8/Windows Server 2012 nebo novější</span><span class="sxs-lookup"><span data-stu-id="95ea5-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="95ea5-124">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="95ea5-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="95ea5-125">Je nutné povolit objekty WebSockets (viz část [Podpora služby IIS/IIS Express](#iisiis-express-support) .).</span><span class="sxs-lookup"><span data-stu-id="95ea5-125">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="95ea5-126">Pokud aplikace běží na [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="95ea5-126">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="95ea5-127">Windows 8/Windows Server 2012 nebo novější</span><span class="sxs-lookup"><span data-stu-id="95ea5-127">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="95ea5-128">Podporované prohlížeče najdete v tématu https://caniuse.com/#feat=websockets .</span><span class="sxs-lookup"><span data-stu-id="95ea5-128">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="95ea5-129">Balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="95ea5-129">NuGet package</span></span>

<span data-ttu-id="95ea5-130">Nainstalujte balíček [Microsoft. AspNetCore. WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) .</span><span class="sxs-lookup"><span data-stu-id="95ea5-130">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="95ea5-131">Konfigurace middlewaru</span><span class="sxs-lookup"><span data-stu-id="95ea5-131">Configure the middleware</span></span>


<span data-ttu-id="95ea5-132">Do metody třídy přidejte middleware WebSockets `Configure` `Startup` :</span><span class="sxs-lookup"><span data-stu-id="95ea5-132">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="95ea5-133">Je možné nakonfigurovat následující nastavení:</span><span class="sxs-lookup"><span data-stu-id="95ea5-133">The following settings can be configured:</span></span>

* <span data-ttu-id="95ea5-134">`KeepAliveInterval` – Jak často se mají do klienta odesílat snímky příkazu "testovat z příkazového testu", aby proxy servery udržovaly otevřené připojení.</span><span class="sxs-lookup"><span data-stu-id="95ea5-134">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="95ea5-135">Výchozí hodnota je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="95ea5-135">The default is two minutes.</span></span>
* <span data-ttu-id="95ea5-136">`ReceiveBufferSize` – Velikost vyrovnávací paměti použité k příjmu dat.</span><span class="sxs-lookup"><span data-stu-id="95ea5-136">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="95ea5-137">Pokročilí uživatelé tuto změnu můžou potřebovat pro vyladění výkonu na základě velikosti dat.</span><span class="sxs-lookup"><span data-stu-id="95ea5-137">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="95ea5-138">Výchozí hodnota je 4 KB.</span><span class="sxs-lookup"><span data-stu-id="95ea5-138">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="95ea5-139">Je možné nakonfigurovat následující nastavení:</span><span class="sxs-lookup"><span data-stu-id="95ea5-139">The following settings can be configured:</span></span>

* <span data-ttu-id="95ea5-140">`KeepAliveInterval` – Jak často se mají do klienta odesílat snímky příkazu "testovat z příkazového testu", aby proxy servery udržovaly otevřené připojení.</span><span class="sxs-lookup"><span data-stu-id="95ea5-140">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="95ea5-141">Výchozí hodnota je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="95ea5-141">The default is two minutes.</span></span>
* <span data-ttu-id="95ea5-142"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> – Velikost vyrovnávací paměti použité k příjmu dat.</span><span class="sxs-lookup"><span data-stu-id="95ea5-142"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="95ea5-143">Pokročilí uživatelé tuto změnu můžou potřebovat pro vyladění výkonu na základě velikosti dat.</span><span class="sxs-lookup"><span data-stu-id="95ea5-143">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="95ea5-144">Výchozí hodnota je 4 KB.</span><span class="sxs-lookup"><span data-stu-id="95ea5-144">The default is 4 KB.</span></span>
* <span data-ttu-id="95ea5-145">`AllowedOrigins` – Seznam povolených hodnot záhlaví zdroje pro požadavky protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="95ea5-145">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="95ea5-146">Ve výchozím nastavení jsou povoleny všechny zdroje.</span><span class="sxs-lookup"><span data-stu-id="95ea5-146">By default, all origins are allowed.</span></span> <span data-ttu-id="95ea5-147">Podrobnosti najdete níže v části omezení původu pro WebSocket.</span><span class="sxs-lookup"><span data-stu-id="95ea5-147">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="95ea5-148">Přijmout požadavky protokolu WebSocket</span><span class="sxs-lookup"><span data-stu-id="95ea5-148">Accept WebSocket requests</span></span>

<span data-ttu-id="95ea5-149">Někam později v životním cyklu požadavku (například později v `Configure` metodě nebo v metodě akce) ověřte, zda se jedná o požadavek protokolu WebSocket a přijmout požadavek protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="95ea5-149">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="95ea5-150">Následující příklad je z níže v `Configure` metodě:</span><span class="sxs-lookup"><span data-stu-id="95ea5-150">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="95ea5-151">Požadavek protokolu WebSocket by mohl být na jakékoli adrese URL, ale tento ukázkový kód přijímá pouze požadavky na `/ws` .</span><span class="sxs-lookup"><span data-stu-id="95ea5-151">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="95ea5-152">Při použití objektu WebSocket je **nutné** , aby byl kanál middleware spuštěn po dobu trvání připojení.</span><span class="sxs-lookup"><span data-stu-id="95ea5-152">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="95ea5-153">Pokud se pokusíte odeslat nebo přijmout zprávu protokolu WebSocket po ukončení kanálu middleware, může se zobrazit výjimka podobná následující:</span><span class="sxs-lookup"><span data-stu-id="95ea5-153">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="95ea5-154">Pokud k zápisu dat do objektu WebSocket používáte službu na pozadí, ujistěte se, že je kanál middleware spuštěný.</span><span class="sxs-lookup"><span data-stu-id="95ea5-154">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="95ea5-155">Použijte k tomu <xref:System.Threading.Tasks.TaskCompletionSource%601> .</span><span class="sxs-lookup"><span data-stu-id="95ea5-155">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="95ea5-156">Předejte `TaskCompletionSource` službě na pozadí a zavolejte ji, <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> až skončíte s WebSocket.</span><span class="sxs-lookup"><span data-stu-id="95ea5-156">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="95ea5-157">Pak `await` <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> vlastnost během žádosti, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="95ea5-157">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="95ea5-158">Uzavřená výjimka WebSocket se může vyskytnout také v případě, že se vrátí příliš brzy od metody Action.</span><span class="sxs-lookup"><span data-stu-id="95ea5-158">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="95ea5-159">Pokud v metodě akce přijmete soket, počkejte, než se před vrácením z metody Action dokončí kód, který používá soket.</span><span class="sxs-lookup"><span data-stu-id="95ea5-159">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="95ea5-160">Nikdy nepoužívejte `Task.Wait()` , `Task.Result` nebo podobná blokující volání pro čekání na dokončení soketu, což může způsobit vážné problémy s vlákny.</span><span class="sxs-lookup"><span data-stu-id="95ea5-160">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="95ea5-161">Vždy použít `await` .</span><span class="sxs-lookup"><span data-stu-id="95ea5-161">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="95ea5-162">Odesílání a příjem zpráv</span><span class="sxs-lookup"><span data-stu-id="95ea5-162">Send and receive messages</span></span>

<span data-ttu-id="95ea5-163">`AcceptWebSocketAsync`Metoda upgraduje připojení TCP na připojení protokolem WebSocket a poskytuje objekt [WebSocket](/dotnet/core/api/system.net.websockets.websocket) .</span><span class="sxs-lookup"><span data-stu-id="95ea5-163">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="95ea5-164">Pomocí `WebSocket` objektu můžete odesílat a přijímat zprávy.</span><span class="sxs-lookup"><span data-stu-id="95ea5-164">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="95ea5-165">Kód uvedený výše, který přijímá požadavek protokolu WebSocket, předá `WebSocket` objekt `Echo` metodě.</span><span class="sxs-lookup"><span data-stu-id="95ea5-165">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="95ea5-166">Kód obdrží zprávu a ihned pošle zpět stejnou zprávu.</span><span class="sxs-lookup"><span data-stu-id="95ea5-166">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="95ea5-167">Zprávy jsou odesílány a přijímány ve smyčce, dokud klient neukončí připojení:</span><span class="sxs-lookup"><span data-stu-id="95ea5-167">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="95ea5-168">Když přijmete připojení protokolu WebSocket před zahájením smyčky, kanál middleware skončí.</span><span class="sxs-lookup"><span data-stu-id="95ea5-168">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="95ea5-169">Po zavření soketu se kanál odvíjí.</span><span class="sxs-lookup"><span data-stu-id="95ea5-169">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="95ea5-170">To znamená, že požadavek se při přijetí protokolu WebSocket zastaví v kanálu.</span><span class="sxs-lookup"><span data-stu-id="95ea5-170">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="95ea5-171">Když je smyčka dokončená a soket je uzavřený, požadavek pokračuje v zálohování kanálu.</span><span class="sxs-lookup"><span data-stu-id="95ea5-171">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="95ea5-172">Popisovač odpojení klienta</span><span class="sxs-lookup"><span data-stu-id="95ea5-172">Handle client disconnects</span></span>

<span data-ttu-id="95ea5-173">Pokud se klient odpojí z důvodu ztráty připojení, server se automaticky neinformuje.</span><span class="sxs-lookup"><span data-stu-id="95ea5-173">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="95ea5-174">Server obdrží zprávu odpojení pouze v případě, že ji klient pošle, a pokud dojde ke ztrátě připojení k Internetu.</span><span class="sxs-lookup"><span data-stu-id="95ea5-174">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="95ea5-175">Pokud chcete provést nějakou akci, když k tomu dojde, nastavte časový limit po nedoručení od klienta do určitého časového období.</span><span class="sxs-lookup"><span data-stu-id="95ea5-175">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="95ea5-176">Pokud klient vždy neposílá zprávy a nechcete, aby vypršel časový limit, protože připojení se nečinný, musí klient použít časovač k odeslání zprávy s příkazem if každých X sekund.</span><span class="sxs-lookup"><span data-stu-id="95ea5-176">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="95ea5-177">Pokud se na serveru nepřišla zpráva do 2 \* X sekund po předchozím konci, ukončí se připojení a oznámí, že se klient odpojil.</span><span class="sxs-lookup"><span data-stu-id="95ea5-177">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="95ea5-178">Počkejte na dvojnásobek očekávaného časového intervalu pro prodlevu při zpoždění sítě, která by mohla obsahovat zprávu s upozorněním na test.</span><span class="sxs-lookup"><span data-stu-id="95ea5-178">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="95ea5-179">Omezení původu protokolu WebSocket</span><span class="sxs-lookup"><span data-stu-id="95ea5-179">WebSocket origin restriction</span></span>

<span data-ttu-id="95ea5-180">Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="95ea5-180">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="95ea5-181">Prohlížeče **neobsahují:**</span><span class="sxs-lookup"><span data-stu-id="95ea5-181">Browsers do **not**:</span></span>

* <span data-ttu-id="95ea5-182">Provádění požadavků CORS v řádu CORS</span><span class="sxs-lookup"><span data-stu-id="95ea5-182">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="95ea5-183">Respektujte omezení zadaná v `Access-Control` hlavičkách při vytváření požadavků protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="95ea5-183">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="95ea5-184">Prohlížeče ale `Origin` při vydávání požadavků protokolu WebSocket odesílají hlavičku.</span><span class="sxs-lookup"><span data-stu-id="95ea5-184">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="95ea5-185">Aplikace by měly být nakonfigurovány pro ověření těchto hlaviček, aby bylo zajištěno, že budou povoleny pouze objekty WebSockets přicházející z očekávaných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="95ea5-185">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="95ea5-186">Pokud váš Server hostuje " https://server.com " a hostující klienta v " https://client.com ", přidejte " https://client.com " do `AllowedOrigins` seznamu pro objekty WebSockets, které chcete ověřit.</span><span class="sxs-lookup"><span data-stu-id="95ea5-186">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="95ea5-187">`Origin`Záhlaví řídí klient a, podobně jako `Referer` záhlaví, může být falešným.</span><span class="sxs-lookup"><span data-stu-id="95ea5-187">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="95ea5-188">Nepoužívejte **tyto** hlavičky jako ověřovací mechanismus.</span><span class="sxs-lookup"><span data-stu-id="95ea5-188">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="95ea5-189">Podpora služby IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="95ea5-189">IIS/IIS Express support</span></span>

<span data-ttu-id="95ea5-190">Windows Server 2012 nebo novější a Windows 8 nebo novější se službou IIS/IIS Express 8 nebo novějším má podporu protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="95ea5-190">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="95ea5-191">Při použití IIS Express jsou objekty WebSocket vždy povolené.</span><span class="sxs-lookup"><span data-stu-id="95ea5-191">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="95ea5-192">Povolování WebSockets ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="95ea5-192">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="95ea5-193">Povolení podpory protokolu WebSocket v systému Windows Server 2012 nebo novějším:</span><span class="sxs-lookup"><span data-stu-id="95ea5-193">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="95ea5-194">Při použití IIS Express se tyto kroky nevyžadují.</span><span class="sxs-lookup"><span data-stu-id="95ea5-194">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="95ea5-195">Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-195">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="95ea5-196">Vyberte **instalaci na základě rolí nebo na základě funkcí**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-196">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="95ea5-197">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-197">Select **Next**.</span></span>
1. <span data-ttu-id="95ea5-198">Vyberte příslušný server (ve výchozím nastavení je místní server vybraný).</span><span class="sxs-lookup"><span data-stu-id="95ea5-198">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="95ea5-199">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-199">Select **Next**.</span></span>
1. <span data-ttu-id="95ea5-200">Rozbalte **webový server (IIS)** ve stromu **role** , rozbalte možnost **webový server**a poté rozbalte položku **vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-200">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="95ea5-201">Vyberte **protokol WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-201">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="95ea5-202">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-202">Select **Next**.</span></span>
1. <span data-ttu-id="95ea5-203">Pokud nepotřebujete další funkce, vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-203">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="95ea5-204">Vyberte **Install** (Nainstalovat).</span><span class="sxs-lookup"><span data-stu-id="95ea5-204">Select **Install**.</span></span>
1. <span data-ttu-id="95ea5-205">Až se instalace dokončí, kliknutím na **Zavřít** ukončete průvodce.</span><span class="sxs-lookup"><span data-stu-id="95ea5-205">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="95ea5-206">Povolení podpory protokolu WebSocket v systému Windows 8 nebo novějším:</span><span class="sxs-lookup"><span data-stu-id="95ea5-206">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="95ea5-207">Při použití IIS Express se tyto kroky nevyžadují.</span><span class="sxs-lookup"><span data-stu-id="95ea5-207">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="95ea5-208">Přejděte na **Ovládací panely**  >  **programy**programy  >  **a funkce**  >  **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="95ea5-208">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="95ea5-209">Otevřete následující uzly: **Internetová informační služba**  >  funkce pro vývoj aplikací v**rámci webové služby**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-209">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="95ea5-210">Vyberte funkci **protokolu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="95ea5-210">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="95ea5-211">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-211">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="95ea5-212">Zakázat WebSocket při použití socket.io v Node.js</span><span class="sxs-lookup"><span data-stu-id="95ea5-212">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="95ea5-213">Pokud používáte podporu protokolu WebSocket v [Socket.IO](https://socket.io/) na [Node.js](https://nodejs.org/), zakažte výchozí modul protokolu WebSocket služby IIS pomocí `webSocket` elementu v *web.config* nebo *applicationHost.config*. Pokud tento krok neprovedete, modul WebSocket služby IIS se pokusí zpracovat komunikaci protokolu WebSocket místo Node.js a aplikace.</span><span class="sxs-lookup"><span data-stu-id="95ea5-213">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="95ea5-214">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="95ea5-214">Sample app</span></span>

<span data-ttu-id="95ea5-215">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) , která doprovází tento článek, je aplikace s odezvou.</span><span class="sxs-lookup"><span data-stu-id="95ea5-215">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="95ea5-216">Má webovou stránku, která zpřístupňuje připojení pomocí protokolu WebSocket, a server znovu odešle všechny zprávy, které obdrží zpátky klientovi.</span><span class="sxs-lookup"><span data-stu-id="95ea5-216">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="95ea5-217">Spusťte aplikaci z příkazového řádku (není nastavená tak, aby se spouštěla ze sady Visual Studio s IIS Express), a přejděte na http://localhost:5000 .</span><span class="sxs-lookup"><span data-stu-id="95ea5-217">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="95ea5-218">Na webové stránce se zobrazuje stav připojení v levém horním rohu:</span><span class="sxs-lookup"><span data-stu-id="95ea5-218">The web page shows the connection status in the upper left:</span></span>

![Počáteční stav webové stránky](websockets/_static/start.png)

<span data-ttu-id="95ea5-220">Vyberte **připojit** a odešlete požadavek protokolu WebSocket na ZOBRAZENOU adresu URL.</span><span class="sxs-lookup"><span data-stu-id="95ea5-220">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="95ea5-221">Zadejte zkušební zprávu a vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-221">Enter a test message and select **Send**.</span></span> <span data-ttu-id="95ea5-222">Po dokončení vyberte **zavřít soket**.</span><span class="sxs-lookup"><span data-stu-id="95ea5-222">When done, select **Close Socket**.</span></span> <span data-ttu-id="95ea5-223">Část **protokol komunikace** oznamuje každou akci otevřít, Odeslat a zavřít, když k ní dojde.</span><span class="sxs-lookup"><span data-stu-id="95ea5-223">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Počáteční stav webové stránky](websockets/_static/end.png)

