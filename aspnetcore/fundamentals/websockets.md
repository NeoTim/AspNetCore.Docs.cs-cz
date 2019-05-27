---
title: Webové sockety v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak začít pracovat s objekty Websocket v ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/10/2019
uid: fundamentals/websockets
ms.openlocfilehash: bba9cf051deaf57efdd82ca2fb1318fce79bd6cc
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223221"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="2c3bd-103">Webové sockety v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c3bd-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="2c3bd-104">Podle [Petr Dykstra](https://github.com/tdykstra) a [Andrew Stanton sestry](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="2c3bd-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="2c3bd-105">Tento článek vysvětluje, jak začít pracovat s objekty Websocket v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="2c3bd-106">[Protokol WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) je protokol, který umožňuje obousměrný trvalé komunikačních kanálů přes připojení TCP.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="2c3bd-107">Používá se v aplikacích, které využívají samosprávné komunikace rychlé, v reálném čase, jako jsou konverzace, řídicí panel a herních aplikací.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="2c3bd-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2c3bd-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="2c3bd-109">Zobrazit [další kroky](#next-steps) části Další informace.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-109">See the [Next steps](#next-steps) section for more information.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2c3bd-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="2c3bd-110">Prerequisites</span></span>

* <span data-ttu-id="2c3bd-111">ASP.NET Core 1.1 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="2c3bd-111">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="2c3bd-112">Libovolný operační systém, který podporuje ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-112">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="2c3bd-113">Windows 7 / Windows Server 2008 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2c3bd-113">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="2c3bd-114">Linux</span><span class="sxs-lookup"><span data-stu-id="2c3bd-114">Linux</span></span>
  * <span data-ttu-id="2c3bd-115">macOS</span><span class="sxs-lookup"><span data-stu-id="2c3bd-115">macOS</span></span>
  
* <span data-ttu-id="2c3bd-116">Pokud aplikace běží na Windows se službou IIS:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-116">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="2c3bd-117">Windows 8 nebo Windows Server 2012 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2c3bd-117">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="2c3bd-118">IIS 8 / IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="2c3bd-118">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="2c3bd-119">Musí být povolené protokoly Websocket (najdete v článku [podpora služby IIS/IIS Express](#iisiis-express-support) části.).</span><span class="sxs-lookup"><span data-stu-id="2c3bd-119">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="2c3bd-120">Pokud aplikace běží na [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="2c3bd-120">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="2c3bd-121">Windows 8 nebo Windows Server 2012 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2c3bd-121">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="2c3bd-122">Podporované prohlížeče, naleznete v tématu https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-122">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="when-to-use-websockets"></a><span data-ttu-id="2c3bd-123">Kdy použít objekty Websocket</span><span class="sxs-lookup"><span data-stu-id="2c3bd-123">When to use WebSockets</span></span>

<span data-ttu-id="2c3bd-124">Použijte pracovat přímo s připojení soketu Websocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-124">Use WebSockets to work directly with a socket connection.</span></span> <span data-ttu-id="2c3bd-125">Například můžete použijte objekty Websocket pro zajištění nejlepšího možného výkonu s hry v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-125">For example, use WebSockets for the best possible performance with a real-time game.</span></span>

<span data-ttu-id="2c3bd-126">[Funkce SignalR technologie ASP.NET Core](xref:signalr/introduction) je knihovna, která zjednodušuje přidávání funkce webu v reálném čase do aplikací.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-126">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="2c3bd-127">Používá objekty Websocket, kdykoli je to možné.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-127">It uses WebSockets whenever possible.</span></span>

## <a name="how-to-use-websockets"></a><span data-ttu-id="2c3bd-128">Jak používat objekty Websocket</span><span class="sxs-lookup"><span data-stu-id="2c3bd-128">How to use WebSockets</span></span>

* <span data-ttu-id="2c3bd-129">Nainstalujte [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-129">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>
* <span data-ttu-id="2c3bd-130">Nakonfigurujte middleware.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-130">Configure the middleware.</span></span>
* <span data-ttu-id="2c3bd-131">Přijímal požadavky protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-131">Accept WebSocket requests.</span></span>
* <span data-ttu-id="2c3bd-132">Odesílání a příjem zpráv.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-132">Send and receive messages.</span></span>

### <a name="configure-the-middleware"></a><span data-ttu-id="2c3bd-133">Nakonfigurujte middleware</span><span class="sxs-lookup"><span data-stu-id="2c3bd-133">Configure the middleware</span></span>

<span data-ttu-id="2c3bd-134">Přidat objekty Websocket middlewaru v `Configure` metodu `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-134">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2c3bd-135">Můžete nakonfigurovat následující nastavení:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-135">The following settings can be configured:</span></span>

* <span data-ttu-id="2c3bd-136">`KeepAliveInterval` – Jak často k odesílání rámců "příkaz ping" ke klientovi, aby proxy servery udržení připojení otevřeného.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-136">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="2c3bd-137">Výchozí hodnota je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-137">The default is two minutes.</span></span>
* <span data-ttu-id="2c3bd-138">`ReceiveBufferSize` – Velikost vyrovnávací paměti používané přijímat data.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-138">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="2c3bd-139">Pokročilí uživatelé může být nutné změnit to pro optimalizace na základě velikosti dat výkonu.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-139">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="2c3bd-140">Výchozí hodnota je 4 KB.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-140">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2c3bd-141">Můžete nakonfigurovat následující nastavení:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-141">The following settings can be configured:</span></span>

* <span data-ttu-id="2c3bd-142">`KeepAliveInterval` – Jak často k odesílání rámců "příkaz ping" ke klientovi, aby proxy servery udržení připojení otevřeného.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-142">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="2c3bd-143">Výchozí hodnota je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-143">The default is two minutes.</span></span>
* <span data-ttu-id="2c3bd-144">`ReceiveBufferSize` – Velikost vyrovnávací paměti používané přijímat data.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-144">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="2c3bd-145">Pokročilí uživatelé může být nutné změnit to pro optimalizace na základě velikosti dat výkonu.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-145">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="2c3bd-146">Výchozí hodnota je 4 KB.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-146">The default is 4 KB.</span></span>
* <span data-ttu-id="2c3bd-147">`AllowedOrigins` – Seznam povolených hodnot hlavičky Origin pro požadavky protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-147">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="2c3bd-148">Ve výchozím nastavení jsou povoleny všechny původy.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-148">By default, all origins are allowed.</span></span> <span data-ttu-id="2c3bd-149">Podrobnosti najdete v části "Omezení objektu websocket na straně zdroje" níže.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-149">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

### <a name="accept-websocket-requests"></a><span data-ttu-id="2c3bd-150">Přijímal požadavky protokolu WebSocket</span><span class="sxs-lookup"><span data-stu-id="2c3bd-150">Accept WebSocket requests</span></span>

<span data-ttu-id="2c3bd-151">Někde dále v cyklu požadavku (dále v `Configure` metoda nebo akce MVC, například) zkontrolujte, jestli je požadavek protokolu WebSocket a přijmout požadavek protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-151">Somewhere later in the request life cycle (later in the `Configure` method or in an MVC action, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="2c3bd-152">Následující příklad je z později v `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-152">The following example is from later in the `Configure` method:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

<span data-ttu-id="2c3bd-153">Požadavek protokolu WebSocket může dojít na libovolnou adresu URL, ale tento vzorový kód přijímá jenom žádosti o `/ws`.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-153">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="2c3bd-154">Při použití protokolu WebSocket, je **musí** zachovat middleware kanálu spuštěna po dobu trvání připojení.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-154">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="2c3bd-155">Pokud se pokusíte odeslat nebo přijmout zprávu pomocí protokolu WebSocket po ukončení kanálu middleware, může získat výjimku vypadat asi takto:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-155">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="2c3bd-156">Pokud používáte službu na pozadí k zápisu dat do objekt WebSocket, zkontrolujte, zda že zachovat middleware profilace spuštěna.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-156">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="2c3bd-157">To provést pomocí <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-157">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="2c3bd-158">Předání `TaskCompletionSource` na pozadí služby a jeho volání <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> po dokončení se objekt WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-158">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="2c3bd-159">Potom `await` <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> vlastnost při žádosti.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-159">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request.</span></span>

### <a name="send-and-receive-messages"></a><span data-ttu-id="2c3bd-160">Odesílání a příjem zpráv</span><span class="sxs-lookup"><span data-stu-id="2c3bd-160">Send and receive messages</span></span>

<span data-ttu-id="2c3bd-161">`AcceptWebSocketAsync` Metoda upgraduje na připojení soketu websocket bylo připojení TCP a poskytuje [protokolu WebSocket](/dotnet/core/api/system.net.websockets.websocket) objektu.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-161">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="2c3bd-162">Použití `WebSocket` objektu pro odesílání a příjem zpráv.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-162">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="2c3bd-163">Předá kódu uvedeného výše, který přijme žádost protokolu WebSocket `WebSocket` objektu `Echo` metody.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-163">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="2c3bd-164">Kód přijme zprávu a okamžitě odešle zpět stejné zprávy.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-164">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="2c3bd-165">Zprávy jsou odeslané a přijaté ve smyčce, dokud klient ukončí připojení:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-165">Messages are sent and received in a loop until the client closes the connection:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

<span data-ttu-id="2c3bd-166">Při přijímání připojení soketu websocket bylo před zahájením smyčky, middleware kanálu se ukončí.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-166">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="2c3bd-167">Po zavření soketu, unwinds kanálu.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-167">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="2c3bd-168">To znamená požadavek se zastaví v budoucnu v kanálu při přijetí objekt WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-168">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="2c3bd-169">Když je smyčka dokončena, je uzavřený soket žádost pokračuje zálohování kanálu.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-169">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="handle-client-disconnects"></a><span data-ttu-id="2c3bd-170">Odpojení popisovač klienta</span><span class="sxs-lookup"><span data-stu-id="2c3bd-170">Handle client disconnects</span></span>

<span data-ttu-id="2c3bd-171">Server není informována automaticky, když se klient odpojí z důvodu ztráty připojení.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-171">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="2c3bd-172">Server přijme zprávu o odpojení, pouze pokud klient odešle, nejde provést, pokud dojde ke ztrátě připojení k Internetu.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-172">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="2c3bd-173">Pokud chcete provést určitou akci, pokud k tomu dojde, nastavte vypršení časového limitu po nic je přijatou od klienta v rámci určité časové okno.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-173">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="2c3bd-174">Pokud klient není vždy zasílání zpráv a nechcete konec časového limitu připojení pouze z důvodu nepovede nečinné připojení, mají klienta pomocí časovače odešlete zprávu ping každých X sekund.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-174">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="2c3bd-175">Na serveru, pokud nebyla zpráva dorazila do 2\*X sekund po předchozí, ukončit připojení a hlásí, že klient byl odpojen.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-175">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="2c3bd-176">Počkejte, až dvakrát předpokládaného časového intervalu ponechte další čas zpoždění v síti, které mohou zdržet na zprávu ping.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-176">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

### <a name="websocket-origin-restriction"></a><span data-ttu-id="2c3bd-177">Omezení objektu websocket na straně zdroje</span><span class="sxs-lookup"><span data-stu-id="2c3bd-177">WebSocket origin restriction</span></span>

<span data-ttu-id="2c3bd-178">Poskytovanou CORS se nevztahují na objekty Websocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-178">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="2c3bd-179">Prohlížeče **není**:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-179">Browsers do **not**:</span></span>

* <span data-ttu-id="2c3bd-180">Provedení přípravné požadavků CORS.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-180">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="2c3bd-181">Respektujeme zadaná v omezení `Access-Control` záhlaví při provádění požadavků protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-181">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="2c3bd-182">Ale prohlížeče odesílají `Origin` záhlaví při vydávání žádostí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-182">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="2c3bd-183">Aplikace musí být nakonfigurovaný k ověření tyto hlavičky k zajištění, že jsou povoleny pouze objekty Websocket očekávané původ, odkud pocházejí.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-183">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="2c3bd-184">Pokud váš server hostuješ na "https://server.com"a hostování vašeho klienta na"https://client.com", přidejte "https://client.com" k `AllowedOrigins` seznamu pro objekty Websocket ověření.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-184">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="2c3bd-185">`Origin` Záhlaví je řízena klientem a stejně jako `Referer` záhlaví, můžete zfalšovaná.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-185">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="2c3bd-186">Proveďte **není** používají tyto hlavičky jako mechanismus ověřování.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-186">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="2c3bd-187">Podpora služby IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="2c3bd-187">IIS/IIS Express support</span></span>

<span data-ttu-id="2c3bd-188">Windows Server 2012 nebo novější a Windows 8 nebo novější s služby IIS/IIS Express 8 nebo novější obsahuje podporu protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-188">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="2c3bd-189">Při použití IIS Expressu, jsou vždy povoleny protokoly Websocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-189">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="2c3bd-190">Povolení ve službě IIS WebSockets</span><span class="sxs-lookup"><span data-stu-id="2c3bd-190">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="2c3bd-191">Pokud chcete povolit podporu protokolu WebSocket ve Windows serveru 2012 nebo novější:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-191">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="2c3bd-192">Tyto kroky se nevyžadují, při použití IIS Expressu</span><span class="sxs-lookup"><span data-stu-id="2c3bd-192">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="2c3bd-193">Použití **přidat role a funkce** z průvodce **spravovat** nabídky nebo na odkaz v **správce serveru**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-193">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="2c3bd-194">Vyberte **instalace na základě rolí nebo na základě funkcí**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-194">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="2c3bd-195">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-195">Select **Next**.</span></span>
1. <span data-ttu-id="2c3bd-196">Vyberte příslušný server (ve výchozím nastavení je vybraný místní server).</span><span class="sxs-lookup"><span data-stu-id="2c3bd-196">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="2c3bd-197">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-197">Select **Next**.</span></span>
1. <span data-ttu-id="2c3bd-198">Rozbalte **webového serveru (IIS)** v **role** stromu, rozbalte **Webový Server**a potom rozbalte **vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-198">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="2c3bd-199">Vyberte **protokol WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-199">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="2c3bd-200">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-200">Select **Next**.</span></span>
1. <span data-ttu-id="2c3bd-201">Pokud nejsou potřebné další funkce, vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-201">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="2c3bd-202">Vyberte **Install** (Nainstalovat).</span><span class="sxs-lookup"><span data-stu-id="2c3bd-202">Select **Install**.</span></span>
1. <span data-ttu-id="2c3bd-203">Po dokončení instalace, vybrat **Zavřít** ukončíte průvodce.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-203">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="2c3bd-204">Pokud chcete povolit podporu protokolu WebSocket v systému Windows 8 nebo novější:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-204">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="2c3bd-205">Tyto kroky se nevyžadují, při použití IIS Expressu</span><span class="sxs-lookup"><span data-stu-id="2c3bd-205">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="2c3bd-206">Přejděte do **ovládací panely** > **programy** > **programy a funkce** > **zapnout Windows funkce na nebo vypnout** (levé straně obrazovky).</span><span class="sxs-lookup"><span data-stu-id="2c3bd-206">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="2c3bd-207">Otevřete následující uzly: **Internetová informační služba** > **webové služby** > **funkce pro vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-207">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="2c3bd-208">Vyberte **protokol WebSocket** funkce.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-208">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="2c3bd-209">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-209">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="2c3bd-210">Zakázat protokol WebSocket, když v Node.js pomocí socket.io</span><span class="sxs-lookup"><span data-stu-id="2c3bd-210">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="2c3bd-211">Pokud používáte podpora protokolu WebSocket v [socket.io](https://socket.io/) na [Node.js](https://nodejs.org/), zakázat pomocí modul WebSocket služby IIS výchozí `webSocket` element v *web.config* nebo *applicationHost.config*. Pokud není tento krok provést, modul WebSocket služby IIS se pokusí se zpracovat komunikaci pomocí protokolu WebSocket spíše než Node.js a aplikace.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-211">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="next-steps"></a><span data-ttu-id="2c3bd-212">Další kroky</span><span class="sxs-lookup"><span data-stu-id="2c3bd-212">Next steps</span></span>

<span data-ttu-id="2c3bd-213">[Ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) , který doprovází tento článek je odezvu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-213">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="2c3bd-214">Obsahuje webovou stránku, která umožňuje připojení pomocí protokolu WebSocket, a server odešle všechny zprávy, které obdrží zpět do klienta.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-214">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="2c3bd-215">Spustit z příkazového řádku (ne zřídila pro spuštění ze sady Visual Studio službou IIS Express) a přejděte do http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-215">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="2c3bd-216">Webové stránky zobrazí v levém horním rohu stav připojení:</span><span class="sxs-lookup"><span data-stu-id="2c3bd-216">The web page shows the connection status in the upper left:</span></span>

![Počáteční stav webové stránky](websockets/_static/start.png)

<span data-ttu-id="2c3bd-218">Vyberte **připojit** na adresu URL odeslat požadavek protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-218">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="2c3bd-219">Zadejte testovací zprávu a vybrat **odeslat**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-219">Enter a test message and select **Send**.</span></span> <span data-ttu-id="2c3bd-220">Až budete hotovi, vyberte **zavřít soketu**.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-220">When done, select **Close Socket**.</span></span> <span data-ttu-id="2c3bd-221">**Komunikace protokolu** části sestavy každý otevřený, odesílání a akce při zavření jako to se stane.</span><span class="sxs-lookup"><span data-stu-id="2c3bd-221">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Počáteční stav webové stránky](websockets/_static/end.png)
