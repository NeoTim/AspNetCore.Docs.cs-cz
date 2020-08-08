---
title: Úvod do ASP.NET CoreSignalR
author: bradygaster
description: Přečtěte si, jak SignalR knihovna ASP.NET Core zjednodušuje přidávání funkcí v reálném čase do aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
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
uid: signalr/introduction
ms.openlocfilehash: 6f3cec83c9af5ec6e820db4a15061eddac613f36
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022170"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a><span data-ttu-id="966be-103">Úvod do ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="966be-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-no-locsignalr"></a><span data-ttu-id="966be-104">Co je to SignalR ?</span><span class="sxs-lookup"><span data-stu-id="966be-104">What is SignalR?</span></span>

<span data-ttu-id="966be-105">ASP.NET Core SignalR je open source knihovna, která zjednodušuje přidávání webových funkcí v reálném čase do aplikací.</span><span class="sxs-lookup"><span data-stu-id="966be-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="966be-106">Webová funkce v reálném čase umožňuje, aby kód na straně serveru nabízel okamžitý obsah klientům.</span><span class="sxs-lookup"><span data-stu-id="966be-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="966be-107">Dobré kandidáty pro SignalR :</span><span class="sxs-lookup"><span data-stu-id="966be-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="966be-108">Aplikace, které vyžadují aktualizace ze serveru s vysokou frekvencí.</span><span class="sxs-lookup"><span data-stu-id="966be-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="966be-109">Příkladem jsou hry, sociální sítě, hlasování, aukce, mapy a aplikace využívající GPS.</span><span class="sxs-lookup"><span data-stu-id="966be-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="966be-110">Řídicí panely a aplikace pro monitorování.</span><span class="sxs-lookup"><span data-stu-id="966be-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="966be-111">Mezi příklady patří řídicí panely společností, okamžité a aktuální informace o prodeji nebo upozornění pro cestující.</span><span class="sxs-lookup"><span data-stu-id="966be-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="966be-112">Aplikace podporující spolupráci.</span><span class="sxs-lookup"><span data-stu-id="966be-112">Collaborative apps.</span></span> <span data-ttu-id="966be-113">Příklady aplikací podporujících spolupráci jsou aplikace tabulí a software pro týmové schůzky.</span><span class="sxs-lookup"><span data-stu-id="966be-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="966be-114">Aplikace, které vyžadují oznámení.</span><span class="sxs-lookup"><span data-stu-id="966be-114">Apps that require notifications.</span></span> <span data-ttu-id="966be-115">Oznámení využívají sociální sítě, e-mailové aplikace, chaty, hry, upozornění pro cestující a řada dalších aplikací.</span><span class="sxs-lookup"><span data-stu-id="966be-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="966be-116">SignalRposkytuje rozhraní API pro vytváření [vzdálených volání procedur (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)mezi servery.</span><span class="sxs-lookup"><span data-stu-id="966be-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="966be-117">Volání RPC volá funkce JavaScriptu na klientech z kódu .NET Core na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="966be-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="966be-118">Zde jsou některé funkce SignalR pro ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="966be-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="966be-119">Zpracovává správu připojení automaticky.</span><span class="sxs-lookup"><span data-stu-id="966be-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="966be-120">Odesílá současně zprávy všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="966be-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="966be-121">Například chatovací místnost.</span><span class="sxs-lookup"><span data-stu-id="966be-121">For example, a chat room.</span></span>
* <span data-ttu-id="966be-122">Odesílá zprávy konkrétním klientům nebo skupinám klientů.</span><span class="sxs-lookup"><span data-stu-id="966be-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="966be-123">Škálujte na zvýšení provozu.</span><span class="sxs-lookup"><span data-stu-id="966be-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="966be-124">Zdroj je hostovaný v [ SignalR úložišti na GitHubu](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span><span class="sxs-lookup"><span data-stu-id="966be-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="966be-125">Přenosy</span><span class="sxs-lookup"><span data-stu-id="966be-125">Transports</span></span>

<span data-ttu-id="966be-126">SignalRpodporuje následující techniky pro zpracování komunikace v reálném čase (v pořadí podle řádného použití):</span><span class="sxs-lookup"><span data-stu-id="966be-126">SignalR supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="966be-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="966be-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="966be-128">Události odeslané serverem</span><span class="sxs-lookup"><span data-stu-id="966be-128">Server-Sent Events</span></span>
* <span data-ttu-id="966be-129">Dlouhé cyklické dotazování</span><span class="sxs-lookup"><span data-stu-id="966be-129">Long Polling</span></span>

<span data-ttu-id="966be-130">SignalRautomaticky zvolí nejlepší přenosovou metodu, která je v rámci schopností serveru a klienta.</span><span class="sxs-lookup"><span data-stu-id="966be-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="966be-131">Centra</span><span class="sxs-lookup"><span data-stu-id="966be-131">Hubs</span></span>

<span data-ttu-id="966be-132">SignalRpoužívá *centra* ke komunikaci mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="966be-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="966be-133">Centrum je kanál vysoké úrovně, který umožňuje klientovi a serveru volat metody na sebe navzájem.</span><span class="sxs-lookup"><span data-stu-id="966be-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="966be-134">SignalRzpracovává odesílání mezi hranicemi počítačů automaticky a umožňuje klientům volat metody na serveru a naopak.</span><span class="sxs-lookup"><span data-stu-id="966be-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="966be-135">Můžete předat parametry silného typu metodám, které umožňují vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="966be-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="966be-136">SignalRposkytuje dva integrované protokoly rozbočovače: textový protokol založený na formátu JSON a binární protokol založený na [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="966be-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="966be-137">MessagePack obvykle vytváří menší zprávy ve srovnání se JSON.</span><span class="sxs-lookup"><span data-stu-id="966be-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="966be-138">Aby bylo možné poskytovat podporu protokolu MessagePack, starší prohlížeče musí podporovat [XHR úrovně 2](https://caniuse.com/#feat=xhr2) .</span><span class="sxs-lookup"><span data-stu-id="966be-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="966be-139">Centra volají kód na straně klienta odesláním zpráv, které obsahují název a parametry metody na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="966be-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="966be-140">Objekty odeslané jako parametry metody jsou deserializovány pomocí nakonfigurovaného protokolu.</span><span class="sxs-lookup"><span data-stu-id="966be-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="966be-141">Klient se pokusí porovnat název s metodou v kódu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="966be-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="966be-142">Když klient najde shodu, zavolá metodu a předá jí deserializovaná data parametrů.</span><span class="sxs-lookup"><span data-stu-id="966be-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="966be-143">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="966be-143">Additional resources</span></span>

* [<span data-ttu-id="966be-144">Začínáme s nástrojem SignalR pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="966be-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="966be-145">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="966be-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="966be-146">Centra</span><span class="sxs-lookup"><span data-stu-id="966be-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="966be-147">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="966be-147">JavaScript client</span></span>](xref:signalr/javascript-client)
