---
title: Úvod do ASP.NET Core SignalR
author: bradygaster
description: Zjistěte, jak funkce SignalR technologie ASP.NET Core library usnadňuje přidávání funkcí v reálném čase do aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/25/2018
uid: signalr/introduction
ms.openlocfilehash: 673efafce60dfa46cb99f9537fda2bca42bf9822
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836360"
---
# <a name="introduction-to-aspnet-core-signalr"></a><span data-ttu-id="96db5-103">Úvod do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="96db5-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-signalr"></a><span data-ttu-id="96db5-104">Co je SignalR?</span><span class="sxs-lookup"><span data-stu-id="96db5-104">What is SignalR?</span></span>

<span data-ttu-id="96db5-105">Funkce SignalR technologie ASP.NET Core je open source knihovna, která zjednodušuje přidávání funkce webu v reálném čase do aplikací.</span><span class="sxs-lookup"><span data-stu-id="96db5-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="96db5-106">Funkce webu v reálném čase umožňuje okamžitě kódu na straně serveru předávaný obsah do klientů.</span><span class="sxs-lookup"><span data-stu-id="96db5-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="96db5-107">Vhodnými kandidáty pro funkci SignalR:</span><span class="sxs-lookup"><span data-stu-id="96db5-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="96db5-108">Aplikace, které vyžadují vysoká frekvence aktualizace ze serveru.</span><span class="sxs-lookup"><span data-stu-id="96db5-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="96db5-109">Příkladem jsou hry, sociálních sítích, hlasování, aukce, mapy a GPS aplikace.</span><span class="sxs-lookup"><span data-stu-id="96db5-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="96db5-110">Řídicí panely a monitorování aplikace.</span><span class="sxs-lookup"><span data-stu-id="96db5-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="96db5-111">Příklady zahrnují společnosti řídicích panelů, rychlých aktualizací prodeje, nebo cestují výstrahy.</span><span class="sxs-lookup"><span data-stu-id="96db5-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="96db5-112">Aplikace pro spolupráci.</span><span class="sxs-lookup"><span data-stu-id="96db5-112">Collaborative apps.</span></span> <span data-ttu-id="96db5-113">Aplikace tabule a týmu splnění softwaru jsou příklady aplikací pro spolupráci.</span><span class="sxs-lookup"><span data-stu-id="96db5-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="96db5-114">Aplikace, které vyžadují oznámení.</span><span class="sxs-lookup"><span data-stu-id="96db5-114">Apps that require notifications.</span></span> <span data-ttu-id="96db5-115">Sociálních sítí, e-mailu, konverzace, využívají hry, cestování výstrahy a mnoha jiných aplikací oznámení.</span><span class="sxs-lookup"><span data-stu-id="96db5-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="96db5-116">Funkce SignalR poskytuje rozhraní API pro vytváření server klient [vzdálených volání procedur (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span><span class="sxs-lookup"><span data-stu-id="96db5-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="96db5-117">Vzdálených volání procedur volají funkce JavaScript na klientských počítačích z kódu .NET Core na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="96db5-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="96db5-118">Tady jsou některé funkce SignalR technologie ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="96db5-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="96db5-119">Správa připojení automaticky zpracovává.</span><span class="sxs-lookup"><span data-stu-id="96db5-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="96db5-120">Odešle zprávy do všech připojených klientů současně.</span><span class="sxs-lookup"><span data-stu-id="96db5-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="96db5-121">Například chatovací místnosti.</span><span class="sxs-lookup"><span data-stu-id="96db5-121">For example, a chat room.</span></span>
* <span data-ttu-id="96db5-122">Odešle zprávy do konkrétních klientů nebo skupiny klientů.</span><span class="sxs-lookup"><span data-stu-id="96db5-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="96db5-123">Škálování zpracování rostoucí provoz.</span><span class="sxs-lookup"><span data-stu-id="96db5-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="96db5-124">Zdroj je hostován v [SignalR úložišti na Githubu](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span><span class="sxs-lookup"><span data-stu-id="96db5-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="96db5-125">Přenosy</span><span class="sxs-lookup"><span data-stu-id="96db5-125">Transports</span></span>

<span data-ttu-id="96db5-126">Funkce SignalR podporuje několik postupů pro zpracování komunikaci v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="96db5-126">SignalR supports several techniques for handling real-time communications:</span></span>

* [<span data-ttu-id="96db5-127">Webové sokety</span><span class="sxs-lookup"><span data-stu-id="96db5-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="96db5-128">Události odeslané serverem</span><span class="sxs-lookup"><span data-stu-id="96db5-128">Server-Sent Events</span></span>
* <span data-ttu-id="96db5-129">Dlouhým dotazováním</span><span class="sxs-lookup"><span data-stu-id="96db5-129">Long Polling</span></span>

<span data-ttu-id="96db5-130">Funkce SignalR automaticky vybere nejlepší metody přenosu, který je v rámci funkce serveru a klienta.</span><span class="sxs-lookup"><span data-stu-id="96db5-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="96db5-131">Rozbočovače</span><span class="sxs-lookup"><span data-stu-id="96db5-131">Hubs</span></span>

<span data-ttu-id="96db5-132">Používá funkci SignalR *rozbočovače* ke komunikaci mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="96db5-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="96db5-133">Centrum je základní kanál, který umožňuje klienta a serveru, volání metod na sobě navzájem.</span><span class="sxs-lookup"><span data-stu-id="96db5-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="96db5-134">Funkce SignalR zpracovává odeslání přes hranice počítač automaticky, umožňuje klientům volání metod na serveru a naopak.</span><span class="sxs-lookup"><span data-stu-id="96db5-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="96db5-135">Typově silné parametry můžete předat do metody, což umožňuje vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="96db5-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="96db5-136">Funkce SignalR poskytuje dva protokoly integrované centra: textového protokolu na základě JSON a binární protokol založený na [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="96db5-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="96db5-137">MessagePack vytváří obecně menší zprávy ve srovnání s JSON.</span><span class="sxs-lookup"><span data-stu-id="96db5-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="96db5-138">Starší prohlížeče musí podporovat [XHR úrovně 2](https://caniuse.com/#feat=xhr2) k zajištění podpory protokolu MessagePack.</span><span class="sxs-lookup"><span data-stu-id="96db5-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="96db5-139">Rozbočovače pro volání kód na straně klienta zasílání zpráv, které obsahují název a parametry metody na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="96db5-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="96db5-140">Objekty, které jako parametry metody jsou deserializaci pomocí nakonfigurovaný protokol.</span><span class="sxs-lookup"><span data-stu-id="96db5-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="96db5-141">Klient se pokusí shodovat s názvem metody v kódu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="96db5-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="96db5-142">Když klient najde shodu, volá metodu a předá data deserializovaný parametrů.</span><span class="sxs-lookup"><span data-stu-id="96db5-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96db5-143">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="96db5-143">Additional resources</span></span>

* [<span data-ttu-id="96db5-144">Začínáme s knihovnou SignalR pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="96db5-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="96db5-145">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="96db5-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="96db5-146">Centra</span><span class="sxs-lookup"><span data-stu-id="96db5-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="96db5-147">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="96db5-147">JavaScript client</span></span>](xref:signalr/javascript-client)
