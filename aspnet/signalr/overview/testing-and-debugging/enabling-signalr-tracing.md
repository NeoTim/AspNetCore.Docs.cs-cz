---
uid: signalr/overview/testing-and-debugging/enabling-signalr-tracing
title: Povolení trasování knihovnou SignalR | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento dokument popisuje, jak povolit a nakonfigurovat trasování pro funkci SignalR servery a klienty. Trasování umožňuje zobrazit diagnostické informace o události...
ms.author: riande
ms.date: 08/08/2014
ms.assetid: 30060acb-be3e-4347-996f-3870f0c37829
msc.legacyurl: /signalr/overview/testing-and-debugging/enabling-signalr-tracing
msc.type: authoredcontent
ms.openlocfilehash: 6ab9a5de16a1440d14f7526c0cd417592ba415db
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021323"
---
<a name="enabling-signalr-tracing"></a><span data-ttu-id="afbdc-104">Povolení trasování knihovnou SignalR</span><span class="sxs-lookup"><span data-stu-id="afbdc-104">Enabling SignalR Tracing</span></span>
====================
<span data-ttu-id="afbdc-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="afbdc-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="afbdc-106">Tento dokument popisuje, jak povolit a nakonfigurovat trasování pro funkci SignalR servery a klienty.</span><span class="sxs-lookup"><span data-stu-id="afbdc-106">This document describes how to enable and configure tracing for SignalR servers and clients.</span></span> <span data-ttu-id="afbdc-107">Trasování můžete zobrazit diagnostické informace o událostech v aplikaci funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="afbdc-107">Tracing enables you to view diagnostic information about events in your SignalR application.</span></span>
>
> <span data-ttu-id="afbdc-108">Toto téma bylo napsáno původně podle Patrick Fletcher.</span><span class="sxs-lookup"><span data-stu-id="afbdc-108">This topic was originally written by Patrick Fletcher.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="afbdc-109">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="afbdc-109">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="afbdc-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="afbdc-110">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="afbdc-111">.NET Framework 4.5</span><span class="sxs-lookup"><span data-stu-id="afbdc-111">.NET Framework 4.5</span></span>
> - <span data-ttu-id="afbdc-112">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="afbdc-112">SignalR version 2</span></span>
>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="afbdc-113">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="afbdc-113">Questions and comments</span></span>
>
> <span data-ttu-id="afbdc-114">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="afbdc-114">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="afbdc-115">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="afbdc-115">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="afbdc-116">Když je povoleno trasování, vytvoří aplikaci s knihovnou SignalR položky protokolu událostí.</span><span class="sxs-lookup"><span data-stu-id="afbdc-116">When tracing is enabled, a SignalR application creates log entries for events.</span></span> <span data-ttu-id="afbdc-117">Protokolování událostí z klienta i serveru.</span><span class="sxs-lookup"><span data-stu-id="afbdc-117">You can log events from both the client and the server.</span></span> <span data-ttu-id="afbdc-118">Trasování protokolů připojení k serveru, zprostředkovatele o škálování a událostí Service bus zprávu.</span><span class="sxs-lookup"><span data-stu-id="afbdc-118">Tracing on the server logs connection, scaleout provider, and message bus events.</span></span> <span data-ttu-id="afbdc-119">Trasování událostí připojení klientských protokolů.</span><span class="sxs-lookup"><span data-stu-id="afbdc-119">Tracing on the client logs connection events.</span></span> <span data-ttu-id="afbdc-120">V systému SignalR 2.1 nebo novější protokoly trasování na straně klienta celý obsah zprávy volání rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="afbdc-120">In SignalR 2.1 and later, tracing on the client logs the full content of hub invocation messages.</span></span>

## <a name="contents"></a><span data-ttu-id="afbdc-121">Obsah</span><span class="sxs-lookup"><span data-stu-id="afbdc-121">Contents</span></span>

- [<span data-ttu-id="afbdc-122">Povolení trasování na serveru</span><span class="sxs-lookup"><span data-stu-id="afbdc-122">Enabling tracing on the server</span></span>](#server)

    - [<span data-ttu-id="afbdc-123">Protokolování událostí na serveru k textovým souborům</span><span class="sxs-lookup"><span data-stu-id="afbdc-123">Logging server events to text files</span></span>](#server_text)
    - [<span data-ttu-id="afbdc-124">Protokolování serveru události do protokolu událostí</span><span class="sxs-lookup"><span data-stu-id="afbdc-124">Logging server events to the event log</span></span>](#server_eventlog)
- [<span data-ttu-id="afbdc-125">Povolení trasování klienta .NET (aplikace pro Windows Desktop)</span><span class="sxs-lookup"><span data-stu-id="afbdc-125">Enabling tracing in the .NET client (Windows Desktop apps)</span></span>](#net_client)

    - [<span data-ttu-id="afbdc-126">Protokolování událostí klienta pro stolní počítače do konzoly</span><span class="sxs-lookup"><span data-stu-id="afbdc-126">Logging Desktop client events to the console</span></span>](#desktop_console)
    - [<span data-ttu-id="afbdc-127">Protokolování událostí klienta pro stolní počítače do textového souboru</span><span class="sxs-lookup"><span data-stu-id="afbdc-127">Logging Desktop client events to a text file</span></span>](#desktop_text)
- [<span data-ttu-id="afbdc-128">Povolení trasování v klientech Windows Phone 8</span><span class="sxs-lookup"><span data-stu-id="afbdc-128">Enabling tracing in Windows Phone 8 clients</span></span>](#phone)

    - [<span data-ttu-id="afbdc-129">Protokolování událostí klienta Windows Phone v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="afbdc-129">Logging Windows Phone client events to the UI</span></span>](#phone_ui)
    - [<span data-ttu-id="afbdc-130">Protokolování událostí pro Windows Phone klienta ke konzole ladění</span><span class="sxs-lookup"><span data-stu-id="afbdc-130">Logging Windows Phone client events to the debug console</span></span>](#phone_debug)
- [<span data-ttu-id="afbdc-131">Povolení trasování klienta jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="afbdc-131">Enabling tracing in the JavaScript client</span></span>](#javascript)

<a id="server"></a>
## <a name="enabling-tracing-on-the-server"></a><span data-ttu-id="afbdc-132">Povolení trasování na serveru</span><span class="sxs-lookup"><span data-stu-id="afbdc-132">Enabling tracing on the server</span></span>

<span data-ttu-id="afbdc-133">Povolení trasování serveru v konfiguračním souboru aplikace (App.config nebo Web.config v závislosti na typu projektu.) Určíte, které kategorie událostí, které chcete se přihlásit.</span><span class="sxs-lookup"><span data-stu-id="afbdc-133">You enable tracing on the server within the application's configuration file (either App.config or Web.config depending on the type of project.) You specify which categories of events you want to log.</span></span> <span data-ttu-id="afbdc-134">V konfiguračním souboru, můžete také určit, zda chcete protokolovat události do textového souboru, do protokolu událostí Windows nebo vlastního protokolu pomocí implementace [TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx).</span><span class="sxs-lookup"><span data-stu-id="afbdc-134">In the configuration file, you also specify whether to log the events to a text file, the Windows event log, or a custom log using an implementation of [TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx).</span></span>

<span data-ttu-id="afbdc-135">Kategorie události serveru zahrnují následující druhy zprávy:</span><span class="sxs-lookup"><span data-stu-id="afbdc-135">The server event categories include the following sorts of messages:</span></span>

| <span data-ttu-id="afbdc-136">Zdroj</span><span class="sxs-lookup"><span data-stu-id="afbdc-136">Source</span></span> | <span data-ttu-id="afbdc-137">Zprávy</span><span class="sxs-lookup"><span data-stu-id="afbdc-137">Messages</span></span> |
| --- | --- |
| <span data-ttu-id="afbdc-138">SignalR.SqlMessageBus</span><span class="sxs-lookup"><span data-stu-id="afbdc-138">SignalR.SqlMessageBus</span></span> | <span data-ttu-id="afbdc-139">Instalační program zprostředkovatele sběrnice zpráv SQL horizontálním navýšením kapacity, operace databáze, chyby a události vypršení časového limitu</span><span class="sxs-lookup"><span data-stu-id="afbdc-139">SQL Message Bus scaleout provider setup, database operation, error, and timeout events</span></span> |
| <span data-ttu-id="afbdc-140">SignalR.ServiceBusMessageBus</span><span class="sxs-lookup"><span data-stu-id="afbdc-140">SignalR.ServiceBusMessageBus</span></span> | <span data-ttu-id="afbdc-141">Vytvoření tématu služby Service bus horizontálním navýšením kapacity zprostředkovatele a předplatné, chyb a zasílání zpráv událostí</span><span class="sxs-lookup"><span data-stu-id="afbdc-141">Service bus scaleout provider topic creation and subscription, error, and messaging events</span></span> |
| <span data-ttu-id="afbdc-142">SignalR.RedisMessageBus</span><span class="sxs-lookup"><span data-stu-id="afbdc-142">SignalR.RedisMessageBus</span></span> | <span data-ttu-id="afbdc-143">Události zprostředkovatele připojení, odpojení a chyba horizontálním navýšením kapacity redis</span><span class="sxs-lookup"><span data-stu-id="afbdc-143">Redis scaleout provider connection, disconnection, and error events</span></span> |
| <span data-ttu-id="afbdc-144">SignalR.ScaleoutMessageBus</span><span class="sxs-lookup"><span data-stu-id="afbdc-144">SignalR.ScaleoutMessageBus</span></span> | <span data-ttu-id="afbdc-145">Události zasílání zpráv o škálování</span><span class="sxs-lookup"><span data-stu-id="afbdc-145">Scaleout messaging events</span></span> |
| <span data-ttu-id="afbdc-146">SignalR.Transports.WebSocketTransport</span><span class="sxs-lookup"><span data-stu-id="afbdc-146">SignalR.Transports.WebSocketTransport</span></span> | <span data-ttu-id="afbdc-147">Události připojení, odpojení, zasílání zpráv a chyba přenosu pomocí protokolu WebSocket</span><span class="sxs-lookup"><span data-stu-id="afbdc-147">WebSocket transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="afbdc-148">SignalR.Transports.ServerSentEventsTransport</span><span class="sxs-lookup"><span data-stu-id="afbdc-148">SignalR.Transports.ServerSentEventsTransport</span></span> | <span data-ttu-id="afbdc-149">ServerSentEvents přenosu připojení, odpojení, zasílání zpráv, události a chyby</span><span class="sxs-lookup"><span data-stu-id="afbdc-149">ServerSentEvents transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="afbdc-150">SignalR.Transports.ForeverFrameTransport</span><span class="sxs-lookup"><span data-stu-id="afbdc-150">SignalR.Transports.ForeverFrameTransport</span></span> | <span data-ttu-id="afbdc-151">ForeverFrame přenosové připojení, odpojení, zasílání zpráv a chybové události</span><span class="sxs-lookup"><span data-stu-id="afbdc-151">ForeverFrame transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="afbdc-152">SignalR.Transports.LongPollingTransport</span><span class="sxs-lookup"><span data-stu-id="afbdc-152">SignalR.Transports.LongPollingTransport</span></span> | <span data-ttu-id="afbdc-153">LongPolling přenosové připojení, odpojení, zasílání zpráv a chybové události</span><span class="sxs-lookup"><span data-stu-id="afbdc-153">LongPolling transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="afbdc-154">SignalR.Transports.TransportHeartBeat</span><span class="sxs-lookup"><span data-stu-id="afbdc-154">SignalR.Transports.TransportHeartBeat</span></span> | <span data-ttu-id="afbdc-155">Přenést události připojení, odpojení a keepalive</span><span class="sxs-lookup"><span data-stu-id="afbdc-155">Transport connection, disconnection, and keepalive events</span></span> |
| <span data-ttu-id="afbdc-156">SignalR.ReflectedHubDescriptorProvider</span><span class="sxs-lookup"><span data-stu-id="afbdc-156">SignalR.ReflectedHubDescriptorProvider</span></span> | <span data-ttu-id="afbdc-157">Události v centru zjišťování</span><span class="sxs-lookup"><span data-stu-id="afbdc-157">Hub discovery events</span></span> |

<a id="server_text"></a>
### <a name="logging-server-events-to-text-files"></a><span data-ttu-id="afbdc-158">Protokolování událostí na serveru k textovým souborům</span><span class="sxs-lookup"><span data-stu-id="afbdc-158">Logging server events to text files</span></span>

<span data-ttu-id="afbdc-159">Následující kód ukazuje, jak povolit trasování pro každou kategorii událostí.</span><span class="sxs-lookup"><span data-stu-id="afbdc-159">The following code shows how to enable tracing for each category of event.</span></span> <span data-ttu-id="afbdc-160">Tento příklad konfiguruje aplikaci, do protokolu událostí do textových souborů.</span><span class="sxs-lookup"><span data-stu-id="afbdc-160">This sample configures the application to log events to text files.</span></span>

<span data-ttu-id="afbdc-161">**XML serverový kód pro povolení trasování**</span><span class="sxs-lookup"><span data-stu-id="afbdc-161">**XML server code for enabling tracing**</span></span>

[!code-html[Main](enabling-signalr-tracing/samples/sample1.html)]

<span data-ttu-id="afbdc-162">Ve výše uvedeném kódu `SignalRSwitch` určuje položku [TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx) používá pro událostí odeslaných do zadaného protokolu.</span><span class="sxs-lookup"><span data-stu-id="afbdc-162">In the code above, the `SignalRSwitch` entry specifies the [TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx) used for events sent to the specified log.</span></span> <span data-ttu-id="afbdc-163">V takovém případě je nastavena na `Verbose` což znamená, že všechny ladění a trasování zpráv jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="afbdc-163">In this case, it is set to `Verbose` which means all debugging and tracing messages are logged.</span></span>

<span data-ttu-id="afbdc-164">Následující výstup zobrazuje položky `transports.log.txt` soubor pro aplikaci pomocí konfiguračního souboru výše.</span><span class="sxs-lookup"><span data-stu-id="afbdc-164">The following output shows entries from the `transports.log.txt` file for an application using the above configuration file.</span></span> <span data-ttu-id="afbdc-165">Zobrazuje nové připojení, odebrané připojení a přenos události prezenčního signálu.</span><span class="sxs-lookup"><span data-stu-id="afbdc-165">It shows a new connection, a removed connection, and transport heartbeat events.</span></span>

[!code-console[Main](enabling-signalr-tracing/samples/sample2.cmd)]

<a id="server_eventlog"></a>
### <a name="logging-server-events-to-the-event-log"></a><span data-ttu-id="afbdc-166">Protokolování serveru události do protokolu událostí</span><span class="sxs-lookup"><span data-stu-id="afbdc-166">Logging server events to the event log</span></span>

<span data-ttu-id="afbdc-167">Chcete-li protokolovat události do protokolu událostí, nikoli textový soubor, změňte hodnoty pro položky v `sharedListeners` uzlu.</span><span class="sxs-lookup"><span data-stu-id="afbdc-167">To log events to the event log rather than a text file, change the values for the entries in the `sharedListeners` node.</span></span> <span data-ttu-id="afbdc-168">Následující kód ukazuje, jak protokolovat události serveru do protokolu událostí:</span><span class="sxs-lookup"><span data-stu-id="afbdc-168">The following code shows how to log server events to the event log:</span></span>

<span data-ttu-id="afbdc-169">**Serverový kód XML pro protokolování události do protokolu událostí**</span><span class="sxs-lookup"><span data-stu-id="afbdc-169">**XML server code for logging events to the event log**</span></span>

[!code-xml[Main](enabling-signalr-tracing/samples/sample3.xml)]

<span data-ttu-id="afbdc-170">Události jsou protokolovány v protokolu aplikací a jsou k dispozici prostřednictvím prohlížeče událostí, jak je znázorněno níže:</span><span class="sxs-lookup"><span data-stu-id="afbdc-170">The events are logged in the Application log, and are available through the Event Viewer, as shown below:</span></span>

![Prohlížeč událostí zobrazuje protokoly SignalR](enabling-signalr-tracing/_static/image1.png)

> [!NOTE]
> <span data-ttu-id="afbdc-172">Při použití protokolu událostí, nastavte **TraceLevel** k **chyba** zachovat počet zpráv, které lze spravovat.</span><span class="sxs-lookup"><span data-stu-id="afbdc-172">When using the event log, set the **TraceLevel** to **Error** to keep the number of messages manageable.</span></span>

<a id="net_client"></a>
## <a name="enabling-tracing-in-the-net-client-windows-desktop-apps"></a><span data-ttu-id="afbdc-173">Povolení trasování klienta .NET (aplikace pro Windows Desktop)</span><span class="sxs-lookup"><span data-stu-id="afbdc-173">Enabling tracing in the .NET client (Windows Desktop apps)</span></span>

<span data-ttu-id="afbdc-174">Klient .NET můžete protokolovat události do konzoly, textový soubor, nebo do vlastního protokolu pomocí implementace [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).</span><span class="sxs-lookup"><span data-stu-id="afbdc-174">The .NET client can log events to the console, a text file, or to a custom log using an implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).</span></span>

<span data-ttu-id="afbdc-175">Povolit protokolování klienta rozhraní .NET, nastavíte připojení k `TraceLevel` vlastnost [TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx) hodnotu a `TraceWriter` platnou vlastnost [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) instance.</span><span class="sxs-lookup"><span data-stu-id="afbdc-175">To enable logging in the .NET client, set the connection's `TraceLevel` property to a [TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx) value, and the `TraceWriter` property to a valid [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) instance.</span></span>

<a id="desktop_console"></a>
### <a name="logging-desktop-client-events-to-the-console"></a><span data-ttu-id="afbdc-176">Protokolování událostí klienta pro stolní počítače do konzoly</span><span class="sxs-lookup"><span data-stu-id="afbdc-176">Logging Desktop client events to the console</span></span>

<span data-ttu-id="afbdc-177">Následující kód jazyka C# ukazuje, jak protokolovat události v rozhraní .NET klienta do konzoly:</span><span class="sxs-lookup"><span data-stu-id="afbdc-177">The following C# code shows how to log events in the .NET client to the console:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample4.cs?highlight=2-3)]

<a id="desktop_text"></a>
### <a name="logging-desktop-client-events-to-a-text-file"></a><span data-ttu-id="afbdc-178">Protokolování událostí klienta pro stolní počítače do textového souboru</span><span class="sxs-lookup"><span data-stu-id="afbdc-178">Logging Desktop client events to a text file</span></span>

<span data-ttu-id="afbdc-179">Následující kód jazyka C# ukazuje, jak protokolovat události v rozhraní .NET klienta do textového souboru:</span><span class="sxs-lookup"><span data-stu-id="afbdc-179">The following C# code shows how to log events in the .NET client to a text file:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample5.cs?highlight=4-5)]

<span data-ttu-id="afbdc-180">Následující výstup zobrazuje položky `ClientLog.txt` soubor pro aplikaci pomocí konfiguračního souboru výše.</span><span class="sxs-lookup"><span data-stu-id="afbdc-180">The following output shows entries from the `ClientLog.txt` file for an application using the above configuration file.</span></span> <span data-ttu-id="afbdc-181">Zobrazuje připojení k serveru klienta a Centrum volání metody klienta volat `addMessage`:</span><span class="sxs-lookup"><span data-stu-id="afbdc-181">It shows the client connecting to the server, and the hub invoking a client method called `addMessage`:</span></span>

[!code-console[Main](enabling-signalr-tracing/samples/sample6.cmd)]

<a id="phone"></a>
## <a name="enabling-tracing-in-windows-phone-8-clients"></a><span data-ttu-id="afbdc-182">Povolení trasování v klientech Windows Phone 8</span><span class="sxs-lookup"><span data-stu-id="afbdc-182">Enabling tracing in Windows Phone 8 clients</span></span>

<span data-ttu-id="afbdc-183">Aplikace SignalR pro aplikace Windows Phone pomocí stejného klienta .NET jako aplikace klasické pracovní plochy, ale [Console.Out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx) a zápis do souboru s [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) nejsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="afbdc-183">SignalR applications for Windows Phone apps use the same .NET client as desktop apps, but [Console.Out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx) and writing to a file with [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) are not available.</span></span> <span data-ttu-id="afbdc-184">Místo toho je potřeba vytvořit vlastní implementaci [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) pro trasování.</span><span class="sxs-lookup"><span data-stu-id="afbdc-184">Instead, you need to create a custom implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) for tracing.</span></span>

<a id="phone_ui"></a>
### <a name="logging-windows-phone-client-events-to-the-ui"></a><span data-ttu-id="afbdc-185">Protokolování událostí klienta Windows Phone v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="afbdc-185">Logging Windows Phone client events to the UI</span></span>

<span data-ttu-id="afbdc-186">[SignalR codebase](https://github.com/SignalR/SignalR/archive/master.zip) zahrnuje ukázky Windows Phone, která zapíše výstup trasování do [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) pomocí vlastní [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) volaná implementaci `TextBlockWriter`.</span><span class="sxs-lookup"><span data-stu-id="afbdc-186">The [SignalR codebase](https://github.com/SignalR/SignalR/archive/master.zip) includes a Windows Phone sample that writes trace output to a [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) using a custom [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) implementation called `TextBlockWriter`.</span></span> <span data-ttu-id="afbdc-187">Tato třída najdete v **samples/Microsoft.AspNet.SignalR.Client.WP8.Samples** projektu.</span><span class="sxs-lookup"><span data-stu-id="afbdc-187">This class can be found in the **samples/Microsoft.AspNet.SignalR.Client.WP8.Samples** project.</span></span> <span data-ttu-id="afbdc-188">Při vytváření instance `TextBlockWriter`, předejte aktuální [třída SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx)a [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) kde se vytvoří [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) pro trasovacího výstup:</span><span class="sxs-lookup"><span data-stu-id="afbdc-188">When creating an instance of `TextBlockWriter`, pass in the current [SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx), and a [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) where it will create a [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) to use for trace output:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample7.cs)]

<span data-ttu-id="afbdc-189">Výstup trasování se poté zapíšou do nového [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) vytvořené v [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) jste předali v:</span><span class="sxs-lookup"><span data-stu-id="afbdc-189">The trace output will then be written to a new [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) created in the [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) you passed in:</span></span>

![](enabling-signalr-tracing/_static/image2.png)

<a id="phone_debug"></a>
### <a name="logging-windows-phone-client-events-to-the-debug-console"></a><span data-ttu-id="afbdc-190">Protokolování událostí pro Windows Phone klienta ke konzole ladění</span><span class="sxs-lookup"><span data-stu-id="afbdc-190">Logging Windows Phone client events to the debug console</span></span>

<span data-ttu-id="afbdc-191">Aby odesílal výstup do konzoly ladění místo uživatelského rozhraní, vytvořte implementaci třídy [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) , která zapisuje do okna ladění a přiřadíte ho k připojení k [TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx) vlastnost:</span><span class="sxs-lookup"><span data-stu-id="afbdc-191">To send output to the debug console rather than the UI, create an implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) that writes to the debug window, and assign it to your connection's [TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx) property:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample8.cs)]

<span data-ttu-id="afbdc-192">Informace o trasování, se zapíšou do okna ladění v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="afbdc-192">Trace information will then be written to the debug window in Visual Studio:</span></span>

![](enabling-signalr-tracing/_static/image3.png)

<a id="javascript"></a>
## <a name="enabling-tracing-in-the-javascript-client"></a><span data-ttu-id="afbdc-193">Povolení trasování klienta jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="afbdc-193">Enabling tracing in the JavaScript client</span></span>

<span data-ttu-id="afbdc-194">Chcete-li povolit protokolování na straně klienta na připojení, nastavte `logging` vlastnost u objektu připojení před voláním `start` metoda k navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="afbdc-194">To enable client-side logging on a connection, set the `logging` property on the connection object before you call the `start` method to establish the connection.</span></span>

<span data-ttu-id="afbdc-195">**Kód JavaScript klienta pro povolení trasování do konzoly prohlížeče (pomocí vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="afbdc-195">**Client JavaScript code for enabling tracing to the browser console (with the generated proxy)**</span></span>

[!code-javascript[Main](enabling-signalr-tracing/samples/sample9.js?highlight=1)]

<span data-ttu-id="afbdc-196">**Kód JavaScript klienta pro povolení trasování do konzoly prohlížeče (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="afbdc-196">**Client JavaScript code for enabling tracing to the browser console (without the generated proxy)**</span></span>

[!code-javascript[Main](enabling-signalr-tracing/samples/sample10.js?highlight=2)]

<span data-ttu-id="afbdc-197">Když je povoleno trasování, javascriptový klient protokoluje události do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="afbdc-197">When tracing is enabled, the JavaScript client logs events to the browser console.</span></span> <span data-ttu-id="afbdc-198">Pro přístup do konzoly prohlížeče, naleznete v tématu [monitorování přenosů](../getting-started/introduction-to-signalr.md#MonitoringTransports).</span><span class="sxs-lookup"><span data-stu-id="afbdc-198">To access the browser console, see [Monitoring Transports](../getting-started/introduction-to-signalr.md#MonitoringTransports).</span></span>

<span data-ttu-id="afbdc-199">Následující snímek obrazovky ukazuje SignalR JavaScript klienta s povoleným trasováním.</span><span class="sxs-lookup"><span data-stu-id="afbdc-199">The following screenshot shows a SignalR JavaScript client with tracing enabled.</span></span> <span data-ttu-id="afbdc-200">Připojení a vyvolání události v centru zobrazí v konzole prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="afbdc-200">It shows connection and hub invocation events in the browser console:</span></span>

![Události trasování SignalR v konzole prohlížeče](enabling-signalr-tracing/_static/image4.png)
