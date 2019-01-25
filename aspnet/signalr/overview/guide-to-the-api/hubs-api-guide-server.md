---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-server
title: Pokyny k rozhraní API Center SignalR technologie ASP.NET – Server (C#) | Dokumentace Microsoftu
author: bradygaster
description: Tento dokument obsahuje úvod do programování na straně serveru rozhraní API pro rozbočovače SignalR technologie ASP.NET pro funkci SignalR verze 2 s představením toho, ukázky kódu...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: b19913e5-cd8a-4e4b-a872-5ac7a858a934
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-server
msc.type: authoredcontent
ms.openlocfilehash: a28663c8d5c679f85e863e7d0b4523a6f4dd4a1f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837452"
---
<a name="aspnet-signalr-hubs-api-guide---server-c"></a><span data-ttu-id="5249d-103">Pokyny k rozhraní API Center SignalR technologie ASP.NET – Server (C#)</span><span class="sxs-lookup"><span data-stu-id="5249d-103">ASP.NET SignalR Hubs API Guide - Server (C#)</span></span>
====================
<span data-ttu-id="5249d-104">podle [Patrick Fletcher](https://github.com/pfletcher), [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="5249d-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="5249d-105">Tento dokument obsahuje úvod do programování na straně serveru rozhraní API pro rozbočovače SignalR technologie ASP.NET pro funkci SignalR verze 2 s představením běžných možností ukázky kódu.</span><span class="sxs-lookup"><span data-stu-id="5249d-105">This document provides an introduction to programming the server side of the ASP.NET SignalR Hubs API for SignalR version 2, with code samples demonstrating common options.</span></span>
> 
> <span data-ttu-id="5249d-106">Rozhraní API pro rozbočovače SignalR umožňuje vytvářet vzdálených volání procedur (RPC) ze serveru pro připojené klienty a z klientů k serveru.</span><span class="sxs-lookup"><span data-stu-id="5249d-106">The SignalR Hubs API enables you to make remote procedure calls (RPCs) from a server to connected clients and from clients to the server.</span></span> <span data-ttu-id="5249d-107">V serverovém kódu můžete definovat metody, které mohou být volány klientů a volat metody, které běží na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="5249d-107">In server code, you define methods that can be called by clients, and you call methods that run on the client.</span></span> <span data-ttu-id="5249d-108">V klientském kódu můžete definovat metody, které lze volat ze serveru a volání metody, které běží na serveru.</span><span class="sxs-lookup"><span data-stu-id="5249d-108">In client code, you define methods that can be called from the server, and you call methods that run on the server.</span></span> <span data-ttu-id="5249d-109">Funkce SignalR postará za vás zajistí funkčnost systému klient server.</span><span class="sxs-lookup"><span data-stu-id="5249d-109">SignalR takes care of all of the client-to-server plumbing for you.</span></span>
> 
> <span data-ttu-id="5249d-110">Funkce SignalR také nabízí nižší úrovně rozhraní API volá trvalé připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-110">SignalR also offers a lower-level API called Persistent Connections.</span></span> <span data-ttu-id="5249d-111">Úvod do SignalR, rozbočovačů a trvalá připojení, najdete v článku [seznámení s knihovnou SignalR 2](../getting-started/introduction-to-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="5249d-111">For an introduction to SignalR, Hubs, and Persistent Connections, see [Introduction to SignalR 2](../getting-started/introduction-to-signalr.md).</span></span>
> 
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="5249d-112">Verze softwaru použitým v tomto tématu</span><span class="sxs-lookup"><span data-stu-id="5249d-112">Software versions used in this topic</span></span>
> 
> 
> - [<span data-ttu-id="5249d-113">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="5249d-113">Visual Studio 2013</span></span>](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - <span data-ttu-id="5249d-114">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="5249d-114">.NET 4.5</span></span>
> - <span data-ttu-id="5249d-115">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="5249d-115">SignalR version 2</span></span>
>   
> 
> 
> ## <a name="topic-versions"></a><span data-ttu-id="5249d-116">Verze tématu</span><span class="sxs-lookup"><span data-stu-id="5249d-116">Topic versions</span></span>
> 
> <span data-ttu-id="5249d-117">Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="5249d-117">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
> 
> ## <a name="questions-and-comments"></a><span data-ttu-id="5249d-118">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="5249d-118">Questions and comments</span></span>
> 
> <span data-ttu-id="5249d-119">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="5249d-119">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="5249d-120">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="5249d-120">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="overview"></a><span data-ttu-id="5249d-121">Přehled</span><span class="sxs-lookup"><span data-stu-id="5249d-121">Overview</span></span>

<span data-ttu-id="5249d-122">Tento dokument obsahuje následující části:</span><span class="sxs-lookup"><span data-stu-id="5249d-122">This document contains the following sections:</span></span>

- [<span data-ttu-id="5249d-123">Postup při registraci SignalR middleware</span><span class="sxs-lookup"><span data-stu-id="5249d-123">How to register SignalR middleware</span></span>](#route)

    - [<span data-ttu-id="5249d-124">Adresa URL /signalr</span><span class="sxs-lookup"><span data-stu-id="5249d-124">The /signalr URL</span></span>](#signalrurl)
    - [<span data-ttu-id="5249d-125">Konfigurace možností SignalR</span><span class="sxs-lookup"><span data-stu-id="5249d-125">Configuring SignalR options</span></span>](#options)
- [<span data-ttu-id="5249d-126">Jak vytvořit a použít třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-126">How to create and use Hub classes</span></span>](#hubclass)

    - [<span data-ttu-id="5249d-127">Doba života objektu centra</span><span class="sxs-lookup"><span data-stu-id="5249d-127">Hub object lifetime</span></span>](#transience)
    - [<span data-ttu-id="5249d-128">Camel-malých a velkých písmen názvů centra v klientech jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="5249d-128">Camel-casing of Hub names in JavaScript clients</span></span>](#hubnames)
    - [<span data-ttu-id="5249d-129">Multiple Hubs</span><span class="sxs-lookup"><span data-stu-id="5249d-129">Multiple Hubs</span></span>](#multiplehubs)
    - [<span data-ttu-id="5249d-130">Silného typu rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-130">Strongly-Typed Hubs</span></span>](#stronglytypedhubs)
- [<span data-ttu-id="5249d-131">Definování metody ve třídě rozbočovače, která může volat klientů</span><span class="sxs-lookup"><span data-stu-id="5249d-131">How to define methods in the Hub class that clients can call</span></span>](#hubmethods)

    - [<span data-ttu-id="5249d-132">Camel-malých a velkých písmen názvů metody do klientů JavaScript</span><span class="sxs-lookup"><span data-stu-id="5249d-132">Camel-casing of method names in JavaScript clients</span></span>](#methodnames)
    - [<span data-ttu-id="5249d-133">Kdy se má spustit asynchronně</span><span class="sxs-lookup"><span data-stu-id="5249d-133">When to execute asynchronously</span></span>](#asyncmethods)
    - [<span data-ttu-id="5249d-134">Definování přetížení</span><span class="sxs-lookup"><span data-stu-id="5249d-134">Defining overloads</span></span>](#overloads)
    - [<span data-ttu-id="5249d-135">Vykazování průběhu z volání metod rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-135">Reporting progress from hub method invocations</span></span>](#progress)
- [<span data-ttu-id="5249d-136">Volání metody klienta z třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-136">How to call client methods from the Hub class</span></span>](#callfromhub)

    - [<span data-ttu-id="5249d-137">Výběr klientů, kteří obdrží vzdálené volání Procedur</span><span class="sxs-lookup"><span data-stu-id="5249d-137">Selecting which clients will receive the RPC</span></span>](#selectingclients)
    - [<span data-ttu-id="5249d-138">Žádné názvy metod ověřování za kompilace</span><span class="sxs-lookup"><span data-stu-id="5249d-138">No compile-time validation for method names</span></span>](#dynamicmethodnames)
    - [<span data-ttu-id="5249d-139">Shoda názvu malá a velká písmena – metoda</span><span class="sxs-lookup"><span data-stu-id="5249d-139">Case-insensitive method name matching</span></span>](#caseinsensitive)
    - [<span data-ttu-id="5249d-140">Asynchronní provádění</span><span class="sxs-lookup"><span data-stu-id="5249d-140">Asynchronous execution</span></span>](#asyncclient)
- [<span data-ttu-id="5249d-141">Správa členství ve skupině ze třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-141">How to manage group membership from the Hub class</span></span>](#groupsfromhub)

    - [<span data-ttu-id="5249d-142">Asynchronní provádění metody přidat a odebrat</span><span class="sxs-lookup"><span data-stu-id="5249d-142">Asynchronous execution of Add and Remove methods</span></span>](#asyncgroupmethods)
    - [<span data-ttu-id="5249d-143">Trvalost členství ve skupině</span><span class="sxs-lookup"><span data-stu-id="5249d-143">Group membership persistence</span></span>](#grouppersistence)
    - [<span data-ttu-id="5249d-144">Skupiny jednoho uživatele</span><span class="sxs-lookup"><span data-stu-id="5249d-144">Single-user groups</span></span>](#singleusergroups)
- [<span data-ttu-id="5249d-145">Zpracování událostí doby platnosti ve třídě centra</span><span class="sxs-lookup"><span data-stu-id="5249d-145">How to handle connection lifetime events in the Hub class</span></span>](#connectionlifetime)

    - [<span data-ttu-id="5249d-146">Kdy jsou volány onconnected rozbočovače, ondisconnected rozbočovače a onreconnected rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-146">When OnConnected, OnDisconnected, and OnReconnected are called</span></span>](#onreconnected)
    - [<span data-ttu-id="5249d-147">Stav volajícího není naplněn.</span><span class="sxs-lookup"><span data-stu-id="5249d-147">Caller state not populated</span></span>](#nocallerstate)
- [<span data-ttu-id="5249d-148">Jak získat informace o klientovi z kontextové vlastnosti</span><span class="sxs-lookup"><span data-stu-id="5249d-148">How to get information about the client from the Context property</span></span>](#contextproperty)
- [<span data-ttu-id="5249d-149">Jak předávat stavu mezi klienty a třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-149">How to pass state between clients and the Hub class</span></span>](#passstate)
- [<span data-ttu-id="5249d-150">Zpracování chyb ve třídě centra</span><span class="sxs-lookup"><span data-stu-id="5249d-150">How to handle errors in the Hub class</span></span>](#handleErrors)
- [<span data-ttu-id="5249d-151">Jak volat metody klienta a spravovat skupiny mimo třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-151">How to call client methods and manage groups from outside the Hub class</span></span>](#callfromoutsidehub)

    - [<span data-ttu-id="5249d-152">Volání metody klienta</span><span class="sxs-lookup"><span data-stu-id="5249d-152">Calling client methods</span></span>](#callingclientsoutsidehub)
    - [<span data-ttu-id="5249d-153">Správa členství ve skupině</span><span class="sxs-lookup"><span data-stu-id="5249d-153">Managing group membership</span></span>](#managinggroupsoutsidehub)
- [<span data-ttu-id="5249d-154">Jak povolit trasování</span><span class="sxs-lookup"><span data-stu-id="5249d-154">How to enable tracing</span></span>](#tracing)
- [<span data-ttu-id="5249d-155">Přizpůsobení kanálu rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-155">How to customize the Hubs pipeline</span></span>](#hubpipeline)

<span data-ttu-id="5249d-156">Dokumentace o tom, jak program klientů naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="5249d-156">For documentation on how to program clients, see the following resources:</span></span>

- [<span data-ttu-id="5249d-157">Pokyny k rozhraní API Center SignalR – javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="5249d-157">SignalR Hubs API Guide - JavaScript Client</span></span>](hubs-api-guide-javascript-client.md)
- [<span data-ttu-id="5249d-158">Pokyny k rozhraní API Center SignalR – klient .NET</span><span class="sxs-lookup"><span data-stu-id="5249d-158">SignalR Hubs API Guide - .NET Client</span></span>](hubs-api-guide-net-client.md)

<span data-ttu-id="5249d-159">Komponenty serveru pro funkci SignalR 2 jsou dostupné pouze v rozhraní .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="5249d-159">The server components for SignalR 2 are only available in .NET 4.5.</span></span> <span data-ttu-id="5249d-160">Servery s .NET 4.0, musíte použít SignalR v1.x.</span><span class="sxs-lookup"><span data-stu-id="5249d-160">Servers running .NET 4.0 must use SignalR v1.x.</span></span>

<a id="route"></a>

## <a name="how-to-register-signalr-middleware"></a><span data-ttu-id="5249d-161">Postup při registraci SignalR middleware</span><span class="sxs-lookup"><span data-stu-id="5249d-161">How to register SignalR middleware</span></span>

<span data-ttu-id="5249d-162">Chcete-li definovat trasy, která budou klienti používat pro připojení k centru, zavolejte `MapSignalR` metoda při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="5249d-162">To define the route that clients will use to connect to your Hub, call the `MapSignalR` method when the application starts.</span></span> <span data-ttu-id="5249d-163">`MapSignalR` je [– metoda rozšíření](https://msdn.microsoft.com/library/vstudio/bb383977.aspx) pro `OwinExtensions` třídy.</span><span class="sxs-lookup"><span data-stu-id="5249d-163">`MapSignalR` is an [extension method](https://msdn.microsoft.com/library/vstudio/bb383977.aspx) for the `OwinExtensions` class.</span></span> <span data-ttu-id="5249d-164">Následující příklad ukazuje, jak definovat trasu rozbočovače SignalR pomocí třídy pro spuštění OWIN.</span><span class="sxs-lookup"><span data-stu-id="5249d-164">The following example shows how to define the SignalR Hubs route using an OWIN startup class.</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample1.cs)]

<span data-ttu-id="5249d-165">Pokud přidáváte funkce SignalR pro aplikace ASP.NET MVC, ujistěte se, přidá se trasa SignalR před jiným trasám.</span><span class="sxs-lookup"><span data-stu-id="5249d-165">If you are adding SignalR functionality to an ASP.NET MVC application, make sure that the SignalR route is added before the other routes.</span></span> <span data-ttu-id="5249d-166">Další informace najdete v tématu [kurzu: Začínáme s knihovnou SignalR 2 a MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).</span><span class="sxs-lookup"><span data-stu-id="5249d-166">For more information, see [Tutorial: Getting Started with SignalR 2 and MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).</span></span>

<a id="signalrurl"></a>

### <a name="the-signalr-url"></a><span data-ttu-id="5249d-167">Adresa URL /signalr</span><span class="sxs-lookup"><span data-stu-id="5249d-167">The /signalr URL</span></span>

<span data-ttu-id="5249d-168">Ve výchozím nastavení, je adresa URL trasy, které budou klienti používat pro připojení k vašemu centru "/ signalr".</span><span class="sxs-lookup"><span data-stu-id="5249d-168">By default, the route URL which clients will use to connect to your Hub is "/signalr".</span></span> <span data-ttu-id="5249d-169">(Nezaměňujte tuto adresu URL s adresou URL "/ signalr/centra", která je automaticky vygenerovaný soubor jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5249d-169">(Don't confuse this URL with the "/signalr/hubs" URL, which is for the automatically generated JavaScript file.</span></span> <span data-ttu-id="5249d-170">Další informace o vygenerovaný proxy server, naleznete v tématu [pokyny k rozhraní API Center SignalR – javascriptový klient - vygenerovaný proxy server a co to dělá za vás](hubs-api-guide-javascript-client.md#genproxy).)</span><span class="sxs-lookup"><span data-stu-id="5249d-170">For more information about the generated proxy, see [SignalR Hubs API Guide - JavaScript Client - The generated proxy and what it does for you](hubs-api-guide-javascript-client.md#genproxy).)</span></span>

<span data-ttu-id="5249d-171">Může být výjimečných případech, které zkontrolujte tuto základní adresu URL nelze použít pro funkci SignalR; například máte složku ve vašem projektu s názvem *signalr* a nechcete, aby změna názvu.</span><span class="sxs-lookup"><span data-stu-id="5249d-171">There might be extraordinary circumstances that make this base URL not usable for SignalR; for example, you have a folder in your project named *signalr* and you don't want to change the name.</span></span> <span data-ttu-id="5249d-172">V takovém případě můžete změnit základní adresa URL, jak je znázorněno v následujících příkladech (nahradit "/ signalr" ve vzorovém kódu s požadovanou adresu URL).</span><span class="sxs-lookup"><span data-stu-id="5249d-172">In that case, you can change the base URL, as shown in the following examples (replace "/signalr" in the sample code with your desired URL).</span></span>

<span data-ttu-id="5249d-173">**Kód serveru, který určuje adresu URL**</span><span class="sxs-lookup"><span data-stu-id="5249d-173">**Server code that specifies the URL**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample2.cs?highlight=1)]

<span data-ttu-id="5249d-174">**Klientský kód jazyka JavaScript, který určuje adresu URL (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="5249d-174">**JavaScript client code that specifies the URL (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-server/samples/sample3.js?highlight=1)]

<span data-ttu-id="5249d-175">**Klientský kód jazyka JavaScript, který určuje adresu URL (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="5249d-175">**JavaScript client code that specifies the URL (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-server/samples/sample4.js?highlight=1)]

<span data-ttu-id="5249d-176">**Klientský kód .NET, který určuje adresu URL**</span><span class="sxs-lookup"><span data-stu-id="5249d-176">**.NET client code that specifies the URL**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample5.cs?highlight=1)]

<a id="options"></a>

### <a name="configuring-signalr-options"></a><span data-ttu-id="5249d-177">Konfigurace možností SignalR</span><span class="sxs-lookup"><span data-stu-id="5249d-177">Configuring SignalR Options</span></span>

<span data-ttu-id="5249d-178">Přetížení `MapSignalR` metody umožňují určit vlastní adresu URL, překladače vlastní závislost a následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="5249d-178">Overloads of the `MapSignalR` method enable you to specify a custom URL, a custom dependency resolver, and the following options:</span></span>

- <span data-ttu-id="5249d-179">Povolte volání mezi doménami z prohlížečů klientů pomocí CORS a JSONP.</span><span class="sxs-lookup"><span data-stu-id="5249d-179">Enable cross-domain calls using CORS or JSONP from browser clients.</span></span>

    <span data-ttu-id="5249d-180">Obvykle Pokud prohlížeč načte stránku z `http://contoso.com`, připojení SignalR je ve stejné doméně, v `http://contoso.com/signalr`.</span><span class="sxs-lookup"><span data-stu-id="5249d-180">Typically if the browser loads a page from `http://contoso.com`, the SignalR connection is in the same domain, at `http://contoso.com/signalr`.</span></span> <span data-ttu-id="5249d-181">Pokud stránku z `http://contoso.com` vytvoří připojení k `http://fabrikam.com/signalr`, to znamená připojení mezi doménami.</span><span class="sxs-lookup"><span data-stu-id="5249d-181">If the page from `http://contoso.com` makes a connection to `http://fabrikam.com/signalr`, that is a cross-domain connection.</span></span> <span data-ttu-id="5249d-182">Z bezpečnostních důvodů jsou ve výchozím nastavení zakázané připojení mezi doménami.</span><span class="sxs-lookup"><span data-stu-id="5249d-182">For security reasons, cross-domain connections are disabled by default.</span></span> <span data-ttu-id="5249d-183">Další informace najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR – javascriptový klient – jak k navázání připojení mezi doménami](hubs-api-guide-javascript-client.md#crossdomain).</span><span class="sxs-lookup"><span data-stu-id="5249d-183">For more information, see [ASP.NET SignalR Hubs API Guide - JavaScript Client - How to establish a cross-domain connection](hubs-api-guide-javascript-client.md#crossdomain).</span></span>
- <span data-ttu-id="5249d-184">Povolte podrobné chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="5249d-184">Enable detailed error messages.</span></span>

    <span data-ttu-id="5249d-185">Když dojde k chybám, je výchozí chování SignalR klientům odeslat zprávu oznámení bez podrobnosti o co se stalo.</span><span class="sxs-lookup"><span data-stu-id="5249d-185">When errors occur, the default behavior of SignalR is to send to clients a notification message without details about what happened.</span></span> <span data-ttu-id="5249d-186">Odesílání podrobné informace o chybě pro klienty se nedoporučuje v produkčním prostředí, protože uživatelé se zlými úmysly může být schopni použijte informace v útoků, které vaše aplikace.</span><span class="sxs-lookup"><span data-stu-id="5249d-186">Sending detailed error information to clients is not recommended in production, because malicious users might be able to use the information in attacks against your application.</span></span> <span data-ttu-id="5249d-187">S řešením problémů, můžete použít tuto možnost dočasně povolit informativnější zasílání zpráv o chybách.</span><span class="sxs-lookup"><span data-stu-id="5249d-187">For troubleshooting, you can use this option to temporarily enable more informative error reporting.</span></span>
- <span data-ttu-id="5249d-188">Zakážete automaticky generovaných souborů proxy server JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5249d-188">Disable automatically generated JavaScript proxy files.</span></span>

    <span data-ttu-id="5249d-189">Ve výchozím nastavení je soubor JavaScript s proxy pro vaše Centrum třídy vygenerované v reakci na adresu URL "/ signalr/centra".</span><span class="sxs-lookup"><span data-stu-id="5249d-189">By default, a JavaScript file with proxies for your Hub classes is generated in response to the URL "/signalr/hubs".</span></span> <span data-ttu-id="5249d-190">Pokud už nechcete používat třídy proxy JavaScript, nebo pokud chcete vygenerovat soubor ručně a odkazovat na fyzický soubor v vašim klientům, můžete tuto možnost zakažte generování proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="5249d-190">If you don't want to use the JavaScript proxies, or if you want to generate this file manually and refer to a physical file in your clients, you can use this option to disable proxy generation.</span></span> <span data-ttu-id="5249d-191">Další informace najdete v tématu [pokyny k rozhraní API Center SignalR – javascriptový klient – proxy generované vytváření fyzického souboru pro funkci SignalR](hubs-api-guide-javascript-client.md#manualproxy).</span><span class="sxs-lookup"><span data-stu-id="5249d-191">For more information, see [SignalR Hubs API Guide - JavaScript Client - How to create a physical file for the SignalR generated proxy](hubs-api-guide-javascript-client.md#manualproxy).</span></span>

<span data-ttu-id="5249d-192">Následující příklad ukazuje, jak zadat adresu URL připojení SignalR a tyto možnosti ve volání `MapSignalR` metody.</span><span class="sxs-lookup"><span data-stu-id="5249d-192">The following example shows how to specify the SignalR connection URL and these options in a call to the `MapSignalR` method.</span></span> <span data-ttu-id="5249d-193">Chcete-li určit vlastní adresu URL, nahraďte "/ signalr" v příkladu nahraďte adresu URL, kterou chcete použít.</span><span class="sxs-lookup"><span data-stu-id="5249d-193">To specify a custom URL, replace "/signalr" in the example with the URL that you want to use.</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample6.cs)]

<a id="hubclass"></a>

## <a name="how-to-create-and-use-hub-classes"></a><span data-ttu-id="5249d-194">Jak vytvořit a použít třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-194">How to create and use Hub classes</span></span>

<span data-ttu-id="5249d-195">Pokud chcete Centrum vytvořit, vytvořit třídu, která je odvozena z [Microsoft.Aspnet.Signalr.Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx).</span><span class="sxs-lookup"><span data-stu-id="5249d-195">To create a Hub, create a class that derives from [Microsoft.Aspnet.Signalr.Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx).</span></span> <span data-ttu-id="5249d-196">Následující příklad ukazuje jednoduchý třída rozbočovače pro chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5249d-196">The following example shows a simple Hub class for a chat application.</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample7.cs)]

<span data-ttu-id="5249d-197">V tomto příkladu můžete volat připojený klient `NewContosoChatMessage` metoda, a pokud ano, je přijatá data vysílali pro všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="5249d-197">In this example, a connected client can call the `NewContosoChatMessage` method, and when it does, the data received is broadcasted to all connected clients.</span></span>

<a id="transience"></a>

### <a name="hub-object-lifetime"></a><span data-ttu-id="5249d-198">Doba života objektu centra</span><span class="sxs-lookup"><span data-stu-id="5249d-198">Hub object lifetime</span></span>

<span data-ttu-id="5249d-199">Nevidíte vytvořit instanci třídy rozbočovače nebo volání obsažených metod z vlastního kódu na serveru. všechny možnosti, které se za vás provádí kanálu rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="5249d-199">You don't instantiate the Hub class or call its methods from your own code on the server; all that is done for you by the SignalR Hubs pipeline.</span></span> <span data-ttu-id="5249d-200">Funkce SignalR vytvoří novou instanci třídy rozbočovače pokaždé, když je potřeba zpracovat operaci rozbočovače, jako je například při připojení, odpojení klienta nebo je volána metoda k serveru.</span><span class="sxs-lookup"><span data-stu-id="5249d-200">SignalR creates a new instance of your Hub class each time it needs to handle a Hub operation such as when a client connects, disconnects, or makes a method call to the server.</span></span>

<span data-ttu-id="5249d-201">Protože instance třídy centra jsou přechodné, nelze je použít pro uchování stavu z jedné metody volání na další.</span><span class="sxs-lookup"><span data-stu-id="5249d-201">Because instances of the Hub class are transient, you can't use them to maintain state from one method call to the next.</span></span> <span data-ttu-id="5249d-202">Pokaždé, když server přijímá volání metody z klienta, novou instanci třídy procesů centra zprávy.</span><span class="sxs-lookup"><span data-stu-id="5249d-202">Each time the server receives a method call from a client, a new instance of your Hub class processes the message.</span></span> <span data-ttu-id="5249d-203">Pro uchování stavu prostřednictvím více připojení a volání metod, pomocí některé jiné metody, jako jsou databáze nebo statická proměnná v třídě rozbočovač nebo jinou třídu, která není odvozena od `Hub`.</span><span class="sxs-lookup"><span data-stu-id="5249d-203">To maintain state through multiple connections and method calls, use some other method such as a database, or a static variable on the Hub class, or a different class that does not derive from `Hub`.</span></span> <span data-ttu-id="5249d-204">Pokud můžete uchovávat data v paměti, způsobem například statická proměnná u třídy rozbočovače, data se ztratí dojde k recyklování domény aplikace.</span><span class="sxs-lookup"><span data-stu-id="5249d-204">If you persist data in memory, using a method such as a static variable on the Hub class, the data will be lost when the app domain recycles.</span></span>

<span data-ttu-id="5249d-205">Pokud chcete odesílat zprávy pro klienty z vlastní kód, který běží mimo třídy rozbočovače, nemůžete to udělal po vytvoření instance třídy instanci rozbočovače, ale dělejte to tím, že získáme odkaz na objekt kontextu SignalR pro rozbočovač třídu.</span><span class="sxs-lookup"><span data-stu-id="5249d-205">If you want to send messages to clients from your own code that runs outside the Hub class, you can't do it by instantiating a Hub class instance, but you can do it by getting a reference to the SignalR context object for your Hub class.</span></span> <span data-ttu-id="5249d-206">Další informace najdete v tématu [klienta volat metody a Správa skupiny mimo třídy rozbočovače](#callfromoutsidehub) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="5249d-206">For more information, see [How to call client methods and manage groups from outside the Hub class](#callfromoutsidehub) later in this topic.</span></span>

<a id="hubnames"></a>

### <a name="camel-casing-of-hub-names-in-javascript-clients"></a><span data-ttu-id="5249d-207">Camel-malých a velkých písmen názvů centra v klientech jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="5249d-207">Camel-casing of Hub names in JavaScript clients</span></span>

<span data-ttu-id="5249d-208">Ve výchozím nastavení klientů JavaScript odkazovat centra pomocí verze název třídy – ve formátu camelCase.</span><span class="sxs-lookup"><span data-stu-id="5249d-208">By default, JavaScript clients refer to Hubs by using a camel-cased version of the class name.</span></span> <span data-ttu-id="5249d-209">Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5249d-209">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span> <span data-ttu-id="5249d-210">V předchozím příkladu by se označuje jako `contosoChatHub` v kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5249d-210">The previous example would be referred to as `contosoChatHub` in JavaScript code.</span></span>

<span data-ttu-id="5249d-211">**Server**</span><span class="sxs-lookup"><span data-stu-id="5249d-211">**Server**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample8.cs?highlight=1)]

<span data-ttu-id="5249d-212">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="5249d-212">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-server/samples/sample9.js?highlight=1)]

<span data-ttu-id="5249d-213">Pokud chcete zadat jiný název pro klienty použít, přidejte `HubName` atribut.</span><span class="sxs-lookup"><span data-stu-id="5249d-213">If you want to specify a different name for clients to use, add the `HubName` attribute.</span></span> <span data-ttu-id="5249d-214">Při použití `HubName` atribut, není žádná změna název na formát camelCase u klientů jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5249d-214">When you use a `HubName` attribute, there is no name change to camel case on JavaScript clients.</span></span>

<span data-ttu-id="5249d-215">**Server**</span><span class="sxs-lookup"><span data-stu-id="5249d-215">**Server**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample10.cs?highlight=1)]

<span data-ttu-id="5249d-216">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="5249d-216">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-server/samples/sample11.js?highlight=1)]

<a id="multiplehubs"></a>

### <a name="multiple-hubs"></a><span data-ttu-id="5249d-217">Více rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-217">Multiple Hubs</span></span>

<span data-ttu-id="5249d-218">V aplikaci můžete definovat více tříd rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-218">You can define multiple Hub classes in an application.</span></span> <span data-ttu-id="5249d-219">Když to uděláte, připojení se sdílí, ale jsou oddělené skupiny:</span><span class="sxs-lookup"><span data-stu-id="5249d-219">When you do that, the connection is shared but groups are separate:</span></span>

- <span data-ttu-id="5249d-220">Budou všichni klienti používat k navázání připojení SignalR ve vaší službě stejná adresa URL ("/ signalr" nebo vaší vlastní adresu URL, pokud byl zadán), a používá se, že připojení pro všechna centra definovány službou.</span><span class="sxs-lookup"><span data-stu-id="5249d-220">All clients will use the same URL to establish a SignalR connection with your service ("/signalr" or your custom URL if you specified one), and that connection is used for all Hubs defined by the service.</span></span>

    <span data-ttu-id="5249d-221">Neexistuje žádné rozdíly ve výkonnosti více hubs ve srovnání s definování všechny funkce centra v jedné třídě.</span><span class="sxs-lookup"><span data-stu-id="5249d-221">There is no performance difference for multiple Hubs compared to defining all Hub functionality in a single class.</span></span>
- <span data-ttu-id="5249d-222">Všechna centra získat stejné informace o požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="5249d-222">All Hubs get the same HTTP request information.</span></span>

    <span data-ttu-id="5249d-223">Od všech centrech sdílejí stejné připojení, je pouze informace žádosti HTTP, která vrací na server, co se dodává v původní požadavek protokolu HTTP, která vytváří připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="5249d-223">Since all Hubs share the same connection, the only HTTP request information that the server gets is what comes in the original HTTP request that establishes the SignalR connection.</span></span> <span data-ttu-id="5249d-224">Pokud používáte žádosti o připojení k předávání informací od klienta k serveru zadáním řetězce dotazu, nemůže poskytnout různé řetězce dotazu do různých rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-224">If you use the connection request to pass information from the client to the server by specifying a query string, you can't provide different query strings to different Hubs.</span></span> <span data-ttu-id="5249d-225">Všechna centra obdrží stejné informace.</span><span class="sxs-lookup"><span data-stu-id="5249d-225">All Hubs will receive the same information.</span></span>
- <span data-ttu-id="5249d-226">Vygenerovaný soubor JavaScript proxy bude obsahovat servery proxy pro všechna centra v jednom souboru.</span><span class="sxs-lookup"><span data-stu-id="5249d-226">The generated JavaScript proxies file will contain proxies for all Hubs in one file.</span></span>

    <span data-ttu-id="5249d-227">Informace o třídy proxy JavaScript naleznete v tématu [pokyny k rozhraní API Center SignalR – javascriptový klient - vygenerovaný proxy server a co to dělá za vás](hubs-api-guide-javascript-client.md#genproxy).</span><span class="sxs-lookup"><span data-stu-id="5249d-227">For information about JavaScript proxies, see [SignalR Hubs API Guide - JavaScript Client - The generated proxy and what it does for you](hubs-api-guide-javascript-client.md#genproxy).</span></span>
- <span data-ttu-id="5249d-228">Skupiny jsou definovány v rámci rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-228">Groups are defined within Hubs.</span></span>

    <span data-ttu-id="5249d-229">V knihovně SignalR, které můžete definovat pojmenovaným skupinám na podmnožiny připojených klientů.</span><span class="sxs-lookup"><span data-stu-id="5249d-229">In SignalR you can define named groups to broadcast to subsets of connected clients.</span></span> <span data-ttu-id="5249d-230">Skupiny jsou spravovány samostatně pro každý rozbočovač.</span><span class="sxs-lookup"><span data-stu-id="5249d-230">Groups are maintained separately for each Hub.</span></span> <span data-ttu-id="5249d-231">Například skupina s názvem "Administrators" zahrnuje jednu sadu klientů pro vaše `ContosoChatHub` třídy a stejný název skupiny by odkazovat na jinou sadu klientů pro vaše `StockTickerHub` třídy.</span><span class="sxs-lookup"><span data-stu-id="5249d-231">For example, a group named "Administrators" would include one set of clients for your `ContosoChatHub` class, and the same group name would refer to a different set of clients for your `StockTickerHub` class.</span></span>

<a id="stronglytypedhubs"></a>
### <a name="strongly-typed-hubs"></a><span data-ttu-id="5249d-232">Silného typu rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-232">Strongly-Typed Hubs</span></span>

<span data-ttu-id="5249d-233">Definování rozhraní pro vaše Centrum metody, které váš klient může odkaz (a povolení technologie Intellisense pro vaše Centrum metody), jsou odvozeny vašeho centra z `Hub<T>` (představíme v SignalR 2.1) spíše než `Hub`:</span><span class="sxs-lookup"><span data-stu-id="5249d-233">To define an interface for your hub methods that your client can reference (and enable Intellisense on your hub methods), derive your hub from `Hub<T>` (introduced in SignalR 2.1) rather than `Hub`:</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample12.cs)]

<a id="hubmethods"></a>

## <a name="how-to-define-methods-in-the-hub-class-that-clients-can-call"></a><span data-ttu-id="5249d-234">Definování metody ve třídě rozbočovače, která může volat klientů</span><span class="sxs-lookup"><span data-stu-id="5249d-234">How to define methods in the Hub class that clients can call</span></span>

<span data-ttu-id="5249d-235">Chcete-li vystavit metodu v rozbočovači, které mají být volány z klienta, deklarujte veřejnou metodu, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="5249d-235">To expose a method on the Hub that you want to be callable from the client, declare a public method, as shown in the following examples.</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample13.cs?highlight=3)]

[!code-csharp[Main](hubs-api-guide-server/samples/sample14.cs?highlight=3)]

<span data-ttu-id="5249d-236">Můžete určit návratový typ a parametry, včetně komplexní typy a pole, stejně jako v jakékoli metodě jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="5249d-236">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="5249d-237">Všechna data, která zobrazí v parametrech nebo vrácení volajícímu je komunikace mezi klientem a serverem s použitím souboru JSON a SignalR automaticky zpracovává vazby složité objekty a pole objektů.</span><span class="sxs-lookup"><span data-stu-id="5249d-237">Any data that you receive in parameters or return to the caller is communicated between the client and the server by using JSON, and SignalR handles the binding of complex objects and arrays of objects automatically.</span></span>

<a id="methodnames"></a>

### <a name="camel-casing-of-method-names-in-javascript-clients"></a><span data-ttu-id="5249d-238">Camel-malých a velkých písmen názvů metody do klientů JavaScript</span><span class="sxs-lookup"><span data-stu-id="5249d-238">Camel-casing of method names in JavaScript clients</span></span>

<span data-ttu-id="5249d-239">Ve výchozím nastavení klienti JavaScript odkazovat metod rozbočovače na pomocí-ve formátu camelCase verzi název metody.</span><span class="sxs-lookup"><span data-stu-id="5249d-239">By default, JavaScript clients refer to Hub methods by using a camel-cased version of the method name.</span></span> <span data-ttu-id="5249d-240">Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5249d-240">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="5249d-241">**Server**</span><span class="sxs-lookup"><span data-stu-id="5249d-241">**Server**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample15.cs?highlight=1)]

<span data-ttu-id="5249d-242">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="5249d-242">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-server/samples/sample16.js?highlight=1)]

<span data-ttu-id="5249d-243">Pokud chcete zadat jiný název pro klienty použít, přidejte `HubMethodName` atribut.</span><span class="sxs-lookup"><span data-stu-id="5249d-243">If you want to specify a different name for clients to use, add the `HubMethodName` attribute.</span></span>

<span data-ttu-id="5249d-244">**Server**</span><span class="sxs-lookup"><span data-stu-id="5249d-244">**Server**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample17.cs?highlight=1)]

<span data-ttu-id="5249d-245">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="5249d-245">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-server/samples/sample18.js?highlight=1)]

<a id="asyncmethods"></a>

### <a name="when-to-execute-asynchronously"></a><span data-ttu-id="5249d-246">Kdy se má spustit asynchronně</span><span class="sxs-lookup"><span data-stu-id="5249d-246">When to execute asynchronously</span></span>

<span data-ttu-id="5249d-247">Metoda bude být dlouhotrvající nebo má práce, které by zahrnovat čekání, jako je například vyhledávání v databázi nebo volání webové služby, vrácením provést asynchronní metody rozbočovače [úloh](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) (místo `void` vrátit) nebo [ Úloha&lt;T&gt; ](https://msdn.microsoft.com/library/dd321424.aspx) objektu (místo `T` návratový typ).</span><span class="sxs-lookup"><span data-stu-id="5249d-247">If the method will be long-running or has to do work that would involve waiting, such as a database lookup or a web service call, make the Hub method asynchronous by returning a [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) (in place of `void` return) or [Task&lt;T&gt;](https://msdn.microsoft.com/library/dd321424.aspx) object (in place of `T` return type).</span></span> <span data-ttu-id="5249d-248">Po návratu `Task` objekt z metody SignalR čeká `Task` k dokončení, a pak pošle nezabalené výsledek zpět do klienta, takže není žádný rozdíl v tom, jak kód volání metody v klientovi.</span><span class="sxs-lookup"><span data-stu-id="5249d-248">When you return a `Task` object from the method, SignalR waits for the `Task` to complete, and then it sends the unwrapped result back to the client, so there is no difference in how you code the method call in the client.</span></span>

<span data-ttu-id="5249d-249">Provedení metody rozbočovače asynchronní se vyhnete blokuje připojení, když používá přenos pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5249d-249">Making a Hub method asynchronous avoids blocking the connection when it uses the WebSocket transport.</span></span> <span data-ttu-id="5249d-250">Když je přenos pomocí protokolu WebSocket centra metoda provedena synchronně, následné volání metod rozbočovače ze stejného klienta jsou blokovány, dokud dokončení metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-250">When a Hub method executes synchronously and the transport is WebSocket, subsequent invocations of methods on the Hub from the same client are blocked until the Hub method completes.</span></span>

<span data-ttu-id="5249d-251">Následující příklad ukazuje stejnou metodu naprogramovány tak, aby běžely synchronně nebo asynchronně, za nímž následuje klientský kód jazyka JavaScript, který se dá použít pro volání jedné verze.</span><span class="sxs-lookup"><span data-stu-id="5249d-251">The following example shows the same method coded to run synchronously or asynchronously, followed by JavaScript client code that works for calling either version.</span></span>

<span data-ttu-id="5249d-252">**Synchronní**</span><span class="sxs-lookup"><span data-stu-id="5249d-252">**Synchronous**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample19.cs)]

<span data-ttu-id="5249d-253">**Asynchronní**</span><span class="sxs-lookup"><span data-stu-id="5249d-253">**Asynchronous**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample20.cs?highlight=1,7-8)]

<span data-ttu-id="5249d-254">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="5249d-254">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-server/samples/sample21.js)]

<span data-ttu-id="5249d-255">Další informace o tom, jak použít asynchronní metody v technologii ASP.NET 4.5 naleznete v tématu [použití asynchronních metod v architektuře ASP.NET MVC 4](../../../mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4.md).</span><span class="sxs-lookup"><span data-stu-id="5249d-255">For more information about how to use asynchronous methods in ASP.NET 4.5, see [Using Asynchronous Methods in ASP.NET MVC 4](../../../mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4.md).</span></span>

<a id="overloads"></a>

### <a name="defining-overloads"></a><span data-ttu-id="5249d-256">Definování přetížení</span><span class="sxs-lookup"><span data-stu-id="5249d-256">Defining Overloads</span></span>

<span data-ttu-id="5249d-257">Pokud chcete definovat přetížení pro metodu, musí být jiný počet parametrů v každé přetížení.</span><span class="sxs-lookup"><span data-stu-id="5249d-257">If you want to define overloads for a method, the number of parameters in each overload must be different.</span></span> <span data-ttu-id="5249d-258">Rozlišení přetížení pouze zadáním různé typy parametrů, bude zkompilována vaše třída rozbočovače, ale služby SignalR vyvolá výjimku v době běhu, když se klienti pokusí volání jednoho z přetížení.</span><span class="sxs-lookup"><span data-stu-id="5249d-258">If you differentiate an overload just by specifying different parameter types, your Hub class will compile but the SignalR service will throw an exception at run time when clients try to call one of the overloads.</span></span>

<a id="progress"></a>
### <a name="reporting-progress-from-hub-method-invocations"></a><span data-ttu-id="5249d-259">Vykazování průběhu z volání metod rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-259">Reporting progress from hub method invocations</span></span>

<span data-ttu-id="5249d-260">Přidává podporu pro funkci SignalR 2.1 [model vytváření sestav průběhu](https://blogs.msdn.com/b/dotnet/archive/2012/06/06/async-in-4-5-enabling-progress-and-cancellation-in-async-apis.aspx) zavedena v rozhraní .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="5249d-260">SignalR 2.1 adds support for the [progress reporting pattern](https://blogs.msdn.com/b/dotnet/archive/2012/06/06/async-in-4-5-enabling-progress-and-cancellation-in-async-apis.aspx) introduced in .NET 4.5.</span></span> <span data-ttu-id="5249d-261">Chcete-li implementovat vykazování průběhu, definujte `IProgress<T>` parametru pro metodu rozbočovače, které váš klient může získat:</span><span class="sxs-lookup"><span data-stu-id="5249d-261">To implement progress reporting, define an `IProgress<T>` parameter for your hub method that your client can access:</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample22.cs)]

<span data-ttu-id="5249d-262">Při zápisu metody serveru dlouhotrvající, je důležité použít asynchronní programovací model jako Async / Await namísto blokování vlákna rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-262">When writing a long-running server method, it is important to use an asynchronous programming pattern like Async/ Await rather than blocking the hub thread.</span></span>

<a id="callfromhub"></a>

## <a name="how-to-call-client-methods-from-the-hub-class"></a><span data-ttu-id="5249d-263">Volání metody klienta z třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-263">How to call client methods from the Hub class</span></span>

<span data-ttu-id="5249d-264">Chcete-li volat metody klienta ze serveru, použijte `Clients` vlastnost v metodě ve své třídě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-264">To call client methods from the server, use the `Clients` property in a method in your Hub class.</span></span> <span data-ttu-id="5249d-265">Následující příklad ukazuje kód serveru, který volá `addNewMessageToPage` na všechny připojené klienty a klientský kód, který definuje metodu v klientovi JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5249d-265">The following example shows server code that calls `addNewMessageToPage` on all connected clients, and client code that defines the method in a JavaScript client.</span></span>

<span data-ttu-id="5249d-266">**Server**</span><span class="sxs-lookup"><span data-stu-id="5249d-266">**Server**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample23.cs?highlight=5)]

<span data-ttu-id="5249d-267">Volání metody klienta je asynchronní operace a vrátí `Task`.</span><span class="sxs-lookup"><span data-stu-id="5249d-267">Invoking a client method is an asynchronous operation and returns a `Task`.</span></span> <span data-ttu-id="5249d-268">Použití `await`:</span><span class="sxs-lookup"><span data-stu-id="5249d-268">Use `await`:</span></span>

* <span data-ttu-id="5249d-269">Aby se zprávy, se pošlou bez chyby.</span><span class="sxs-lookup"><span data-stu-id="5249d-269">To ensure the message is sent without error.</span></span> 
* <span data-ttu-id="5249d-270">Pro povolení zachytávání a zpracování chyb v bloku try-catch.</span><span class="sxs-lookup"><span data-stu-id="5249d-270">To enable catching and handling errors in a try-catch block.</span></span>

<span data-ttu-id="5249d-271">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="5249d-271">**JavaScript client using generated proxy**</span></span>

[!code-html[Main](hubs-api-guide-server/samples/sample24.html?highlight=1)]

<span data-ttu-id="5249d-272">Nelze získat návratovou hodnotu z metody; syntaxe jako `int x = Clients.All.add(1,1)` nefunguje.</span><span class="sxs-lookup"><span data-stu-id="5249d-272">You can't get a return value from a client method; syntax such as `int x = Clients.All.add(1,1)` does not work.</span></span>

<span data-ttu-id="5249d-273">Můžete zadat komplexní typy a pole parametrů.</span><span class="sxs-lookup"><span data-stu-id="5249d-273">You can specify complex types and arrays for the parameters.</span></span> <span data-ttu-id="5249d-274">Následující příklad předá komplexní typ klienta v parametru metody.</span><span class="sxs-lookup"><span data-stu-id="5249d-274">The following example passes a complex type to the client in a method parameter.</span></span>

<span data-ttu-id="5249d-275">**Kód serveru, který volá metodu klienta pomocí komplexního objektu**</span><span class="sxs-lookup"><span data-stu-id="5249d-275">**Server code that calls a client method using a complex object**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample25.cs?highlight=3)]

<span data-ttu-id="5249d-276">**Kód serveru, který definuje komplexní objekt**</span><span class="sxs-lookup"><span data-stu-id="5249d-276">**Server code that defines the complex object**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample26.cs?highlight=1)]

<span data-ttu-id="5249d-277">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="5249d-277">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-server/samples/sample27.js?highlight=2-3)]

<a id="selectingclients"></a>

### <a name="selecting-which-clients-will-receive-the-rpc"></a><span data-ttu-id="5249d-278">Výběr klientů, kteří obdrží vzdálené volání Procedur</span><span class="sxs-lookup"><span data-stu-id="5249d-278">Selecting which clients will receive the RPC</span></span>

<span data-ttu-id="5249d-279">Vrátí vlastnost klientů [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx) objekt, který poskytuje několik možností pro určení, které klienti obdrží vzdálené volání Procedur:</span><span class="sxs-lookup"><span data-stu-id="5249d-279">The Clients property returns a [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx) object that provides several options for specifying which clients will receive the RPC:</span></span>

- <span data-ttu-id="5249d-280">Všichni připojení klienti.</span><span class="sxs-lookup"><span data-stu-id="5249d-280">All connected clients.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample28.cs)]
- <span data-ttu-id="5249d-281">Pouze volajícího klienta.</span><span class="sxs-lookup"><span data-stu-id="5249d-281">Only the calling client.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample29.cs)]
- <span data-ttu-id="5249d-282">Všichni klienti s výjimkou volajícího klienta.</span><span class="sxs-lookup"><span data-stu-id="5249d-282">All clients except the calling client.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample30.cs)]
- <span data-ttu-id="5249d-283">Konkrétního klienta, které identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-283">A specific client identified by connection ID.</span></span>

    [!code-css[Main](hubs-api-guide-server/samples/sample31.css)]

    <span data-ttu-id="5249d-284">Tento příklad příkladu volá `addContosoChatMessageToPage` na volajícího klienta a má stejný účinek jako použití `Clients.Caller`.</span><span class="sxs-lookup"><span data-stu-id="5249d-284">This example calls `addContosoChatMessageToPage` on the calling client and has the same effect as using `Clients.Caller`.</span></span>
- <span data-ttu-id="5249d-285">Všichni připojení klienti s výjimkou určitých klientech identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-285">All connected clients except the specified clients, identified by connection ID.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample32.cs)]
- <span data-ttu-id="5249d-286">Všichni připojení klienti do zadané skupiny.</span><span class="sxs-lookup"><span data-stu-id="5249d-286">All connected clients in a specified group.</span></span>

    [!code-css[Main](hubs-api-guide-server/samples/sample33.css)]
- <span data-ttu-id="5249d-287">Všechny připojené klienty v zadané skupině s výjimkou určitých klientech identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-287">All connected clients in a specified group except the specified clients, identified by connection ID.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample34.cs)]
- <span data-ttu-id="5249d-288">Všichni připojení klienti v zadané skupině s výjimkou volajícího klienta.</span><span class="sxs-lookup"><span data-stu-id="5249d-288">All connected clients in a specified group except the calling client.</span></span>

    [!code-css[Main](hubs-api-guide-server/samples/sample35.css)]
- <span data-ttu-id="5249d-289">Konkrétní uživatele identifikovaného parametrem ID uživatele.</span><span class="sxs-lookup"><span data-stu-id="5249d-289">A specific user, identified by userId.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample36.cs)]

    <span data-ttu-id="5249d-290">Ve výchozím nastavení je to `IPrincipal.Identity.Name`, ale to se dá změnit pomocí [registrace implementace IUserIdProvider s hostitelem globální](mapping-users-to-connections.md#IUserIdProvider).</span><span class="sxs-lookup"><span data-stu-id="5249d-290">By default, this is `IPrincipal.Identity.Name`, but this can be changed by [registering an implementation of IUserIdProvider with the global host](mapping-users-to-connections.md#IUserIdProvider).</span></span>
- <span data-ttu-id="5249d-291">Klienti a skupiny na seznam ID připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-291">All clients and groups in a list of connection IDs.</span></span>

    [!code-css[Main](hubs-api-guide-server/samples/sample37.css)]
- <span data-ttu-id="5249d-292">Seznam skupin.</span><span class="sxs-lookup"><span data-stu-id="5249d-292">A list of groups.</span></span>

    [!code-css[Main](hubs-api-guide-server/samples/sample38.css)]
- <span data-ttu-id="5249d-293">Uživatele podle jména.</span><span class="sxs-lookup"><span data-stu-id="5249d-293">A user by name.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample39.cs)]
- <span data-ttu-id="5249d-294">Seznam uživatelských jmen (představíme v SignalR 2.1).</span><span class="sxs-lookup"><span data-stu-id="5249d-294">A list of user names (introduced in SignalR 2.1).</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample40.cs)]

<a id="dynamicmethodnames"></a>

### <a name="no-compile-time-validation-for-method-names"></a><span data-ttu-id="5249d-295">Žádné názvy metod ověřování za kompilace</span><span class="sxs-lookup"><span data-stu-id="5249d-295">No compile-time validation for method names</span></span>

<span data-ttu-id="5249d-296">Název metody, který zadáte, je interpretován jako dynamický objekt, což znamená, že odpadá technologie IntelliSense a ověření za kompilace pro něj.</span><span class="sxs-lookup"><span data-stu-id="5249d-296">The method name that you specify is interpreted as a dynamic object, which means there is no IntelliSense or compile-time validation for it.</span></span> <span data-ttu-id="5249d-297">Výraz je vyhodnocen v době běhu.</span><span class="sxs-lookup"><span data-stu-id="5249d-297">The expression is evaluated at run time.</span></span> <span data-ttu-id="5249d-298">Při volání metody, které spouští, SignalR odešle klientovi názvu metody a hodnoty parametrů, a pokud má klient metodu, která odpovídá názvu, že metoda je volána a hodnoty parametru jsou předány do něj.</span><span class="sxs-lookup"><span data-stu-id="5249d-298">When the method call executes, SignalR sends the method name and the parameter values to the client, and if the client has a method that matches the name, that method is called and the parameter values are passed to it.</span></span> <span data-ttu-id="5249d-299">Pokud na straně klienta se nenašla žádná odpovídající metoda, je vyvolána žádná chyba.</span><span class="sxs-lookup"><span data-stu-id="5249d-299">If no matching method is found on the client, no error is raised.</span></span> <span data-ttu-id="5249d-300">Informace o formátu dat, která SignalR odesílá do klienta na pozadí při volání metody klienta najdete v tématu [Úvod ke knihovně SignalR](../getting-started/introduction-to-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="5249d-300">For information about the format of the data that SignalR transmits to the client behind the scenes when you call a client method, see [Introduction to SignalR](../getting-started/introduction-to-signalr.md).</span></span>

<a id="caseinsensitive"></a>

### <a name="case-insensitive-method-name-matching"></a><span data-ttu-id="5249d-301">Shoda názvu malá a velká písmena – metoda</span><span class="sxs-lookup"><span data-stu-id="5249d-301">Case-insensitive method name matching</span></span>

<span data-ttu-id="5249d-302">Shoda názvu metody je velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="5249d-302">Method name matching is case-insensitive.</span></span> <span data-ttu-id="5249d-303">Například `Clients.All.addContosoChatMessageToPage` na serveru spustí `AddContosoChatMessageToPage`, `addcontosochatmessagetopage`, nebo `addContosoChatMessageToPage` na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="5249d-303">For example, `Clients.All.addContosoChatMessageToPage` on the server will execute `AddContosoChatMessageToPage`, `addcontosochatmessagetopage`, or `addContosoChatMessageToPage` on the client.</span></span>

<a id="asyncclient"></a>

### <a name="asynchronous-execution"></a><span data-ttu-id="5249d-304">Asynchronní provádění</span><span class="sxs-lookup"><span data-stu-id="5249d-304">Asynchronous execution</span></span>

<span data-ttu-id="5249d-305">Asynchronně provede metodu, kterou je možné volat.</span><span class="sxs-lookup"><span data-stu-id="5249d-305">The method that you call executes asynchronously.</span></span> <span data-ttu-id="5249d-306">Veškerý kód, který se dodává po volání metody do klienta se spustí okamžitě bez čekání na SignalR k dokončení přenosu dat do klientů, není-li určit, že následující řádky kódu by měla počkat na dokončení metody.</span><span class="sxs-lookup"><span data-stu-id="5249d-306">Any code that comes after a method call to a client will execute immediately without waiting for SignalR to finish transmitting data to clients unless you specify that the subsequent lines of code should wait for method completion.</span></span> <span data-ttu-id="5249d-307">Následující příklad kódu ukazuje, jak provést dvě metody klienta postupně.</span><span class="sxs-lookup"><span data-stu-id="5249d-307">The following code example shows how to execute two client methods sequentially.</span></span>

<span data-ttu-id="5249d-308">**Použití operátoru Await (.NET 4.5)**</span><span class="sxs-lookup"><span data-stu-id="5249d-308">**Using Await (.NET 4.5)**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample41.cs?highlight=1,3)]

<span data-ttu-id="5249d-309">Pokud používáte `await` počkat, dokud se nedokončí metodu klienta předtím, než provede další řádek kódu, který nemusí znamenat, že klienti ve skutečnosti obdrží zprávu před provedením další řádek kódu.</span><span class="sxs-lookup"><span data-stu-id="5249d-309">If you use `await` to wait until a client method finishes before the next line of code executes, that does not mean that clients will actually receive the message before the next line of code executes.</span></span> <span data-ttu-id="5249d-310">"Dokončení" volání metody klienta pouze znamená, že SignalR provedla vše potřebné k odeslání zprávy.</span><span class="sxs-lookup"><span data-stu-id="5249d-310">"Completion" of a client method call only means that SignalR has done everything necessary to send the message.</span></span> <span data-ttu-id="5249d-311">Pokud potřebujete ověření, že klienti zobrazila zpráva, budete muset tento mechanismus program sami.</span><span class="sxs-lookup"><span data-stu-id="5249d-311">If you need verification that clients received the message, you have to program that mechanism yourself.</span></span> <span data-ttu-id="5249d-312">Například může kód `MessageReceived` metodu v rozbočovači a v `addContosoChatMessageToPage` metody na straně klienta lze volat `MessageReceived` po provedení činnost, kterou je třeba provést na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="5249d-312">For example, you could code a `MessageReceived` method on the Hub, and in the `addContosoChatMessageToPage` method on the client you could call `MessageReceived` after you do whatever work you need to do on the client.</span></span> <span data-ttu-id="5249d-313">V `MessageReceived` v centru vám pomůžou práci závisí na skutečným klientem příjem a zpracování původní volání metody.</span><span class="sxs-lookup"><span data-stu-id="5249d-313">In `MessageReceived` in the Hub you can do whatever work depends on actual client reception and processing of the original method call.</span></span>

### <a name="how-to-use-a-string-variable-as-the-method-name"></a><span data-ttu-id="5249d-314">Použití proměnné řetězce jako názvu – metoda</span><span class="sxs-lookup"><span data-stu-id="5249d-314">How to use a string variable as the method name</span></span>

<span data-ttu-id="5249d-315">Pokud chcete vyvolat metodu klienta s použitím proměnné řetězce jako názvu metody přetypování `Clients.All` (nebo `Clients.Others`, `Clients.Caller`atd) k `IClientProxy` a následně zavolat [Invoke (methodName, args...) ](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.iclientproxy.invoke(v=vs.111).aspx).</span><span class="sxs-lookup"><span data-stu-id="5249d-315">If you want to invoke a client method by using a string variable as the method name, cast `Clients.All` (or `Clients.Others`, `Clients.Caller`, etc.) to `IClientProxy` and then call [Invoke(methodName, args...)](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.iclientproxy.invoke(v=vs.111).aspx).</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample42.cs)]

<a id="groupsfromhub"></a>

## <a name="how-to-manage-group-membership-from-the-hub-class"></a><span data-ttu-id="5249d-316">Správa členství ve skupině ze třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-316">How to manage group membership from the Hub class</span></span>

<span data-ttu-id="5249d-317">Skupinami v knihovně SignalR poskytuje metodu pro vysílání zpráv do zadaného podmnožiny připojených klientů.</span><span class="sxs-lookup"><span data-stu-id="5249d-317">Groups in SignalR provide a method for broadcasting messages to specified subsets of connected clients.</span></span> <span data-ttu-id="5249d-318">Skupina může mít libovolný počet klientů a klienta můžete mít libovolný počet skupin.</span><span class="sxs-lookup"><span data-stu-id="5249d-318">A group can have any number of clients, and a client can be a member of any number of groups.</span></span>

<span data-ttu-id="5249d-319">Chcete-li spravovat členství ve skupině, použijte [přidat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) a [odebrat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) metody poskytované objektem `Groups` vlastnosti třídy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-319">To manage group membership, use the [Add](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) and [Remove](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) methods provided by the `Groups` property of the Hub class.</span></span> <span data-ttu-id="5249d-320">Následující příklad ukazuje `Groups.Add` a `Groups.Remove` metod používaných v metodách rozbočovače, které jsou volány kódem na straně klienta, za nímž následuje klientský kód jazyka JavaScript, který je volá.</span><span class="sxs-lookup"><span data-stu-id="5249d-320">The following example shows the `Groups.Add` and `Groups.Remove` methods used in Hub methods that are called by client code, followed by JavaScript client code that calls them.</span></span>

<span data-ttu-id="5249d-321">**Server**</span><span class="sxs-lookup"><span data-stu-id="5249d-321">**Server**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample43.cs?highlight=5,10)]

<span data-ttu-id="5249d-322">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="5249d-322">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-server/samples/sample44.js)]

[!code-javascript[Main](hubs-api-guide-server/samples/sample45.js)]

<span data-ttu-id="5249d-323">Není nutné explicitně vytvářet skupiny.</span><span class="sxs-lookup"><span data-stu-id="5249d-323">You don't have to explicitly create groups.</span></span> <span data-ttu-id="5249d-324">V platnosti skupiny se automaticky vytvoří při prvním zadejte jeho název ve volání `Groups.Add`, a bude odstraněn, když odeberete poslední připojení z členství v ní.</span><span class="sxs-lookup"><span data-stu-id="5249d-324">In effect a group is automatically created the first time you specify its name in a call to `Groups.Add`, and it is deleted when you remove the last connection from membership in it.</span></span>

<span data-ttu-id="5249d-325">Neexistuje žádné rozhraní API pro získání seznamu členství ve skupinách nebo seznam skupin.</span><span class="sxs-lookup"><span data-stu-id="5249d-325">There is no API for getting a group membership list or a list of groups.</span></span> <span data-ttu-id="5249d-326">Funkce SignalR odesílá zprávy do klientů a skupin na základě [modelu pub/sub](http://en.wikipedia.org/wiki/Publish/subscribe), a server neumožňuje spravovat seznam skupin nebo členství ve skupinách.</span><span class="sxs-lookup"><span data-stu-id="5249d-326">SignalR sends messages to clients and groups based on a [pub/sub model](http://en.wikipedia.org/wiki/Publish/subscribe), and the server does not maintain lists of groups or group memberships.</span></span> <span data-ttu-id="5249d-327">To pomáhá maximalizovat škálovatelnost, protože pokaždé, když přidáte uzel do webové farmy, jakýkoli stav, který udržuje SignalR musí být rozšířena na nový uzel.</span><span class="sxs-lookup"><span data-stu-id="5249d-327">This helps maximize scalability, because whenever you add a node to a web farm, any state that SignalR maintains has to be propagated to the new node.</span></span>

<a id="asyncgroupmethods"></a>

### <a name="asynchronous-execution-of-add-and-remove-methods"></a><span data-ttu-id="5249d-328">Asynchronní provádění metody přidat a odebrat</span><span class="sxs-lookup"><span data-stu-id="5249d-328">Asynchronous execution of Add and Remove methods</span></span>

<span data-ttu-id="5249d-329">`Groups.Add` a `Groups.Remove` metody spustit asynchronně.</span><span class="sxs-lookup"><span data-stu-id="5249d-329">The `Groups.Add` and `Groups.Remove` methods execute asynchronously.</span></span> <span data-ttu-id="5249d-330">Pokud chcete přidat klienta do skupiny a okamžitě odešle zprávu do klienta pomocí skupiny, budete muset Ujistěte se, že `Groups.Add` metoda skončí jako první.</span><span class="sxs-lookup"><span data-stu-id="5249d-330">If you want to add a client to a group and immediately send a message to the client by using the group, you have to make sure that the `Groups.Add` method finishes first.</span></span> <span data-ttu-id="5249d-331">Následující příklad kódu ukazuje, jak to provést.</span><span class="sxs-lookup"><span data-stu-id="5249d-331">The following code example shows how to do that.</span></span>

<span data-ttu-id="5249d-332">**Přidání klienta do skupiny a potom zasílání zpráv daného klienta**</span><span class="sxs-lookup"><span data-stu-id="5249d-332">**Adding a client to a group and then messaging that client**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample46.cs?highlight=1,3)]

<a id="grouppersistence"></a>

### <a name="group-membership-persistence"></a><span data-ttu-id="5249d-333">Trvalost členství ve skupině</span><span class="sxs-lookup"><span data-stu-id="5249d-333">Group membership persistence</span></span>

<span data-ttu-id="5249d-334">Sleduje připojení SignalR, ne s uživateli, takže když chcete uživateli být ve stejné skupině pokaždé, když uživatel vytváří připojení, je nutné volat `Groups.Add` pokaždé, když uživatel vytvoří nové připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-334">SignalR tracks connections, not users, so if you want a user to be in the same group every time the user establishes a connection, you have to call `Groups.Add` every time the user establishes a new connection.</span></span>

<span data-ttu-id="5249d-335">Po dočasné ztrátě připojení někdy SignalR můžete obnovit připojení automaticky.</span><span class="sxs-lookup"><span data-stu-id="5249d-335">After a temporary loss of connectivity, sometimes SignalR can restore the connection automatically.</span></span> <span data-ttu-id="5249d-336">V takovém případě SignalR je obnovení stejného připojení není vytvoření nového připojení, a proto se členství ve skupině klienta automaticky obnoví.</span><span class="sxs-lookup"><span data-stu-id="5249d-336">In that case, SignalR is restoring the same connection, not establishing a new connection, and so the client's group membership is automatically restored.</span></span> <span data-ttu-id="5249d-337">To je možné i v případě, že dočasné přerušení je restartování serveru nebo selhání, protože stav připojení pro každého klienta, včetně členství ve skupinách, je odbavovaná klientovi.</span><span class="sxs-lookup"><span data-stu-id="5249d-337">This is possible even when the temporary break is the result of a server reboot or failure, because connection state for each client, including group memberships, is round-tripped to the client.</span></span> <span data-ttu-id="5249d-338">Pokud server přestane fungovat a nahrazuje nový server, než vyprší časový limit připojení, může klient automaticky znovu připojit k novému serveru a znovu zaregistrovat ve skupinách, který je členem skupiny.</span><span class="sxs-lookup"><span data-stu-id="5249d-338">If a server goes down and is replaced by a new server before the connection times out, a client can reconnect automatically to the new server and re-enroll in groups it is a member of.</span></span>

<span data-ttu-id="5249d-339">Pokud po ztrátě připojení, nelze automaticky obnovit připojení, nebo při připojení vyprší časový limit, nebo při odpojení klienta (například když prohlížeči přejde na novou stránku), členství ve skupinách budou ztraceny.</span><span class="sxs-lookup"><span data-stu-id="5249d-339">When a connection can't be restored automatically after a loss of connectivity, or when the connection times out, or when the client disconnects (for example, when a browser navigates to a new page), group memberships are lost.</span></span> <span data-ttu-id="5249d-340">Při příštím připojení uživatele bude nové připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-340">The next time the user connects will be a new connection.</span></span> <span data-ttu-id="5249d-341">Chcete-li udržovat členství ve skupinách, pokud se stejnému uživateli vytvoří nové připojení, sledování přidružení mezi uživatelů a skupin a členství ve skupinách obnovení pokaždé, když uživatel vytvoří nové připojení má vaše aplikace.</span><span class="sxs-lookup"><span data-stu-id="5249d-341">To maintain group memberships when the same user establishes a new connection, your application has to track the associations between users and groups, and restore group memberships each time a user establishes a new connection.</span></span>

<span data-ttu-id="5249d-342">Další informace o připojení a opětovná připojení najdete v tématu [způsob zpracování událostí doby platnosti ve třídě centra](#connectionlifetime) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="5249d-342">For more information about connections and reconnections, see [How to handle connection lifetime events in the Hub class](#connectionlifetime) later in this topic.</span></span>

<a id="singleusergroups"></a>

### <a name="single-user-groups"></a><span data-ttu-id="5249d-343">Skupiny jednoho uživatele</span><span class="sxs-lookup"><span data-stu-id="5249d-343">Single-user groups</span></span>

<span data-ttu-id="5249d-344">Aplikace, které používají funkci SignalR obvykle nutné udržovat přehled o asociace mezi uživateli a připojení, pokud chcete zjistit, který uživatel odeslal zprávu a které uživatelé měli přijímání zprávy.</span><span class="sxs-lookup"><span data-stu-id="5249d-344">Applications that use SignalR typically have to keep track of the associations between users and connections in order to know which user has sent a message and which user(s) should be receiving a message.</span></span> <span data-ttu-id="5249d-345">Skupiny se používají v jednom ze dvou běžně používané vzory pro učinit.</span><span class="sxs-lookup"><span data-stu-id="5249d-345">Groups are used in one of the two commonly used patterns for doing that.</span></span>

- <span data-ttu-id="5249d-346">Skupiny jednoho uživatele.</span><span class="sxs-lookup"><span data-stu-id="5249d-346">Single-user groups.</span></span>

    <span data-ttu-id="5249d-347">Můžete zadat uživatelské jméno jako název skupiny a do skupiny přidat aktuální ID připojení, pokaždé, když uživatel připojí, nebo znovu připojí.</span><span class="sxs-lookup"><span data-stu-id="5249d-347">You can specify the user name as the group name, and add the current connection ID to the group every time the user connects or reconnects.</span></span> <span data-ttu-id="5249d-348">K odeslání zprávy pro uživatele, které odesíláte do skupiny.</span><span class="sxs-lookup"><span data-stu-id="5249d-348">To send messages to the user you send to the group.</span></span> <span data-ttu-id="5249d-349">Nevýhodou této metody je, že skupina nemá poskytují způsob, jak zjistit, zda uživatel je online nebo offline.</span><span class="sxs-lookup"><span data-stu-id="5249d-349">A disadvantage of this method is that the group doesn't provide you with a way to find out if the user is online or offline.</span></span>
- <span data-ttu-id="5249d-350">Sledujte přidružení mezi uživatelská jména a ID připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-350">Track associations between user names and connection IDs.</span></span>

    <span data-ttu-id="5249d-351">Můžete ukládat přidružení mezi každé uživatelské jméno a ID jeden nebo více připojení ve slovníku nebo v databázi a aktualizace uložených dat pokaždé, když uživatel připojí nebo odpojí.</span><span class="sxs-lookup"><span data-stu-id="5249d-351">You can store an association between each user name and one or more connection IDs in a dictionary or database, and update the stored data each time the user connects or disconnects.</span></span> <span data-ttu-id="5249d-352">K odeslání zprávy uživateli zadat ID připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-352">To send messages to the user you specify the connection IDs.</span></span> <span data-ttu-id="5249d-353">Nevýhodou této metody je, že přijímá větší množství paměti.</span><span class="sxs-lookup"><span data-stu-id="5249d-353">A disadvantage of this method is that it takes more memory.</span></span>

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events-in-the-hub-class"></a><span data-ttu-id="5249d-354">Zpracování událostí doby platnosti ve třídě centra</span><span class="sxs-lookup"><span data-stu-id="5249d-354">How to handle connection lifetime events in the Hub class</span></span>

<span data-ttu-id="5249d-355">Obvyklé důvody pro zpracování událostí doby platnosti jsou ke sledování, zda je uživatel připojen nebo Ne a mějte přehled o přidružení mezi uživatelská jména a ID připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-355">Typical reasons for handling connection lifetime events are to keep track of whether a user is connected or not, and to keep track of the association between user names and connection IDs.</span></span> <span data-ttu-id="5249d-356">Váš vlastní kód spustit, když klienti připojovat nebo odpojovat, přepsat `OnConnected`, `OnDisconnected`, a `OnReconnected` třídy virtuální metody rozbočovače, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="5249d-356">To run your own code when clients connect or disconnect, override the `OnConnected`, `OnDisconnected`, and `OnReconnected` virtual methods of the Hub class, as shown in the following example.</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample47.cs?highlight=3,14,22)]

<a id="onreconnected"></a>

### <a name="when-onconnected-ondisconnected-and-onreconnected-are-called"></a><span data-ttu-id="5249d-357">Kdy jsou volány onconnected rozbočovače, ondisconnected rozbočovače a onreconnected rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-357">When OnConnected, OnDisconnected, and OnReconnected are called</span></span>

<span data-ttu-id="5249d-358">Pokaždé, když prohlížeč přejde na novou stránku, nové připojení musí být zavedena, což znamená, že se spustí SignalR `OnDisconnected` následuje metoda `OnConnected` metoda.</span><span class="sxs-lookup"><span data-stu-id="5249d-358">Each time a browser navigates to a new page, a new connection has to be established, which means SignalR will execute the `OnDisconnected` method followed by the `OnConnected` method.</span></span> <span data-ttu-id="5249d-359">Funkce SignalR vždy vytvoří nové ID připojení, po vytvoření nového připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-359">SignalR always creates a new connection ID when a new connection is established.</span></span>

<span data-ttu-id="5249d-360">`OnReconnected` Metoda je volána, když bude zjištěna dočasné přerušení připojení, které funkci SignalR může automaticky zotavit po, například když kabelu dočasně odpojení a opětovném připojení předtím, než vyprší časový limit připojení. `OnDisconnected` Metoda se volá, když je klient odpojen a SignalR nelze znovu připojit automaticky, například pokud prohlížeč přejde na novou stránku.</span><span class="sxs-lookup"><span data-stu-id="5249d-360">The `OnReconnected` method is called when there has been a temporary break in connectivity that SignalR can automatically recover from, such as when a cable is temporarily disconnected and reconnected before the connection times out. The `OnDisconnected` method is called when the client is disconnected and SignalR can't automatically reconnect, such as when a browser navigates to a new page.</span></span> <span data-ttu-id="5249d-361">Proto je možné pořadí událostí pro daného klienta `OnConnected`, `OnReconnected`, `OnDisconnected`; nebo `OnConnected`, `OnDisconnected`.</span><span class="sxs-lookup"><span data-stu-id="5249d-361">Therefore, a possible sequence of events for a given client is `OnConnected`, `OnReconnected`, `OnDisconnected`; or `OnConnected`, `OnDisconnected`.</span></span> <span data-ttu-id="5249d-362">Neuvidíte sekvence `OnConnected`, `OnDisconnected`, `OnReconnected` pro dané připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-362">You won't see the sequence `OnConnected`, `OnDisconnected`, `OnReconnected` for a given connection.</span></span>

<span data-ttu-id="5249d-363">`OnDisconnected` Metoda nebude zavolána v některých případech, například když server ocitne mimo provoz nebo doména aplikace získá recyklován.</span><span class="sxs-lookup"><span data-stu-id="5249d-363">The `OnDisconnected` method doesn't get called in some scenarios, such as when a server goes down or the App Domain gets recycled.</span></span> <span data-ttu-id="5249d-364">Když jiný server je v řádku nebo doména aplikace dokončí jeho Koš, může být někteří klienti moci znovu připojit a aktivuje `OnReconnected` událostí.</span><span class="sxs-lookup"><span data-stu-id="5249d-364">When another server comes on line or the App Domain completes its recycle, some clients may be able to reconnect and fire the `OnReconnected` event.</span></span>

<span data-ttu-id="5249d-365">Další informace najdete v tématu [principy a zpracování událostí doby platnosti v knihovně SignalR](handling-connection-lifetime-events.md).</span><span class="sxs-lookup"><span data-stu-id="5249d-365">For more information, see [Understanding and Handling Connection Lifetime Events in SignalR](handling-connection-lifetime-events.md).</span></span>

<a id="nocallerstate"></a>

### <a name="caller-state-not-populated"></a><span data-ttu-id="5249d-366">Stav volajícího není naplněn.</span><span class="sxs-lookup"><span data-stu-id="5249d-366">Caller state not populated</span></span>

<span data-ttu-id="5249d-367">Metody zpracování událostí životního cyklu připojení se nazývají ze serveru, což znamená, že jakýkoli stav, kam si ukládáte `state` objektu na straně klienta nebude dosazeny `Caller` vlastnost na serveru.</span><span class="sxs-lookup"><span data-stu-id="5249d-367">The connection lifetime event handler methods are called from the server, which means that any state that you put in the `state` object on the client will not be populated in the `Caller` property on the server.</span></span> <span data-ttu-id="5249d-368">Informace o `state` objektu a `Caller` vlastnost, naleznete v tématu [jak předat stavu mezi klienty a třídy rozbočovače](#passstate) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="5249d-368">For information about the `state` object and the `Caller` property, see [How to pass state between clients and the Hub class](#passstate) later in this topic.</span></span>

<a id="contextproperty"></a>

## <a name="how-to-get-information-about-the-client-from-the-context-property"></a><span data-ttu-id="5249d-369">Jak získat informace o klientovi z kontextové vlastnosti</span><span class="sxs-lookup"><span data-stu-id="5249d-369">How to get information about the client from the Context property</span></span>

<span data-ttu-id="5249d-370">Chcete-li získat informace o klientovi, použijte `Context` vlastnosti třídy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-370">To get information about the client, use the `Context` property of the Hub class.</span></span> <span data-ttu-id="5249d-371">`Context` Vrátí vlastnost [HubCallerContext](https://msdn.microsoft.com/library/jj890883(v=vs.111).aspx) objekt, který poskytuje přístup k následujícím informacím:</span><span class="sxs-lookup"><span data-stu-id="5249d-371">The `Context` property returns a [HubCallerContext](https://msdn.microsoft.com/library/jj890883(v=vs.111).aspx) object which provides access to the following information:</span></span>

- <span data-ttu-id="5249d-372">ID připojení volajícího klienta.</span><span class="sxs-lookup"><span data-stu-id="5249d-372">The connection ID of the calling client.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample48.cs?highlight=1)]

    <span data-ttu-id="5249d-373">ID připojení je identifikátor GUID, který je přiřazen nástrojem SignalR (hodnota nelze zadat ve svém vlastním kódu).</span><span class="sxs-lookup"><span data-stu-id="5249d-373">The connection ID is a GUID that is assigned by SignalR (you can't specify the value in your own code).</span></span> <span data-ttu-id="5249d-374">Existuje jedno ID připojení pro každé připojení a stejného připojení ID se používá ve všech centrech, pokud máte více Center ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5249d-374">There is one connection ID for each connection, and the same connection ID is used by all Hubs if you have multiple Hubs in your application.</span></span>
- <span data-ttu-id="5249d-375">Data hlavičky protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5249d-375">HTTP header data.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample49.cs?highlight=1)]

    <span data-ttu-id="5249d-376">Můžete také získat hlavičky protokolu HTTP z `Context.Headers`.</span><span class="sxs-lookup"><span data-stu-id="5249d-376">You can also get HTTP headers from `Context.Headers`.</span></span> <span data-ttu-id="5249d-377">Z důvodu více odkazů na stejnou věc je, že `Context.Headers` byla vytvořena jako první, `Context.Request` vlastnost byla přidána později, a `Context.Headers` se zachovává kvůli zpětné kompatibilitě.</span><span class="sxs-lookup"><span data-stu-id="5249d-377">The reason for multiple references to the same thing is that `Context.Headers` was created first, the `Context.Request` property was added later, and `Context.Headers` was retained for backward compatibility.</span></span>
- <span data-ttu-id="5249d-378">Data řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="5249d-378">Query string data.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample50.cs?highlight=1)]

    <span data-ttu-id="5249d-379">Můžete také získat data řetězce dotazu z `Context.QueryString`.</span><span class="sxs-lookup"><span data-stu-id="5249d-379">You can also get query string data from `Context.QueryString`.</span></span>

    <span data-ttu-id="5249d-380">Řetězec dotazu, který dostanete v této vlastnosti je ten, který byl použit v požadavku HTTP, který navázalo se připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="5249d-380">The query string that you get in this property is the one that was used with the HTTP request that established the SignalR connection.</span></span> <span data-ttu-id="5249d-381">Můžete přidat parametry řetězce dotazu v klientovi nakonfigurováním připojení, které je pohodlný způsob, jak předat data o klientovi z klienta na server.</span><span class="sxs-lookup"><span data-stu-id="5249d-381">You can add query string parameters in the client by configuring the connection, which is a convenient way to pass data about the client from the client to the server.</span></span> <span data-ttu-id="5249d-382">Následující příklad ukazuje jeden způsob, jak přidat řetězec dotazu v jazyce JavaScript klienta při použití vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="5249d-382">The following example shows one way to add a query string in a JavaScript client when you use the generated proxy.</span></span>

    [!code-javascript[Main](hubs-api-guide-server/samples/sample51.js?highlight=1)]

    <span data-ttu-id="5249d-383">Další informace o nastavení parametrů řetězce dotazu, naleznete v tématu příručky rozhraní API pro [JavaScript](hubs-api-guide-javascript-client.md) a [.NET](hubs-api-guide-net-client.md) klientů.</span><span class="sxs-lookup"><span data-stu-id="5249d-383">For more information about setting query string parameters, see the API guides for the [JavaScript](hubs-api-guide-javascript-client.md) and [.NET](hubs-api-guide-net-client.md) clients.</span></span>

    <span data-ttu-id="5249d-384">Můžete najít metodu přenosu používá pro připojení v data řetězce dotazu spolu s některé jiné hodnoty používaná interně knihovnou SignalR:</span><span class="sxs-lookup"><span data-stu-id="5249d-384">You can find the transport method used for the connection in the query string data, along with some other values used internally by SignalR:</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample52.cs)]

    <span data-ttu-id="5249d-385">Hodnota `transportMethod` bude "webSockets", "serverSentEvents", "foreverFrame" nebo "longPolling".</span><span class="sxs-lookup"><span data-stu-id="5249d-385">The value of `transportMethod` will be "webSockets", "serverSentEvents", "foreverFrame" or "longPolling".</span></span> <span data-ttu-id="5249d-386">Poznámka: Pokud tuto hodnotu zkontrolovat `OnConnected` metoda obslužné rutiny událostí, v některých scénářích může být zpočátku získat hodnotu přenosu, který není konečný vyjednávaný přenosu metodu připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-386">Note that if you check this value in the `OnConnected` event handler method, in some scenarios you might initially get a transport value that is not the final negotiated transport method for the connection.</span></span> <span data-ttu-id="5249d-387">V takovém případě metoda vyvolá výjimku a zavolá se později po vytvoření finální přepravy.</span><span class="sxs-lookup"><span data-stu-id="5249d-387">In that case the method will throw an exception and will be called again later when the final transport method is established.</span></span>
- <span data-ttu-id="5249d-388">Soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="5249d-388">Cookies.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample53.cs?highlight=1)]

    <span data-ttu-id="5249d-389">Můžete také získat soubory cookie z `Context.RequestCookies`.</span><span class="sxs-lookup"><span data-stu-id="5249d-389">You can also get cookies from `Context.RequestCookies`.</span></span>
- <span data-ttu-id="5249d-390">Informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="5249d-390">User information.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample54.cs?highlight=1)]
- <span data-ttu-id="5249d-391">Objektu HttpContext žádosti:</span><span class="sxs-lookup"><span data-stu-id="5249d-391">The HttpContext object for the request :</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample55.cs?highlight=1)]

    <span data-ttu-id="5249d-392">Tuto metodu použijte místo zobrazování `HttpContext.Current` zobrazíte `HttpContext` objektu pro připojení k systému SignalR.</span><span class="sxs-lookup"><span data-stu-id="5249d-392">Use this method instead of getting `HttpContext.Current` to get the `HttpContext` object for the SignalR connection.</span></span>

<a id="passstate"></a>

## <a name="how-to-pass-state-between-clients-and-the-hub-class"></a><span data-ttu-id="5249d-393">Jak předávat stavu mezi klienty a třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-393">How to pass state between clients and the Hub class</span></span>

<span data-ttu-id="5249d-394">Poskytuje proxy serveru klienta `state` objektu, ve kterém můžete ukládat data, která chcete předávat na server se každé volání metody.</span><span class="sxs-lookup"><span data-stu-id="5249d-394">The client proxy provides a `state` object in which you can store data that you want to be transmitted to the server with each method call.</span></span> <span data-ttu-id="5249d-395">Na serveru můžete přístup k těmto datům v `Clients.Caller` vlastnost v metodách rozbočovače, které jsou volány klienty.</span><span class="sxs-lookup"><span data-stu-id="5249d-395">On the server you can access this data in the `Clients.Caller` property in Hub methods that are called by clients.</span></span> <span data-ttu-id="5249d-396">`Clients.Caller` Vlastnost není vyplněný pro metody zpracování událostí životního cyklu připojení `OnConnected`, `OnDisconnected`, a `OnReconnected`.</span><span class="sxs-lookup"><span data-stu-id="5249d-396">The `Clients.Caller` property is not populated for the connection lifetime event handler methods `OnConnected`, `OnDisconnected`, and `OnReconnected`.</span></span>

<span data-ttu-id="5249d-397">Vytváření nebo aktualizaci dat v `state` objektu a `Clients.Caller` vlastnost funguje v obou směrech.</span><span class="sxs-lookup"><span data-stu-id="5249d-397">Creating or updating data in the `state` object and the `Clients.Caller` property works in both directions.</span></span> <span data-ttu-id="5249d-398">Hodnoty na serveru můžete aktualizovat a jsou předány zpět do klienta.</span><span class="sxs-lookup"><span data-stu-id="5249d-398">You can update values in the server and they are passed back to the client.</span></span>

<span data-ttu-id="5249d-399">Následující příklad ukazuje klientský kód jazyka JavaScript, který ukládá stav přenosu na server se každé volání metody.</span><span class="sxs-lookup"><span data-stu-id="5249d-399">The following example shows JavaScript client code that stores state for transmission to the server with every method call.</span></span>

[!code-javascript[Main](hubs-api-guide-server/samples/sample56.js?highlight=1-2)]

<span data-ttu-id="5249d-400">Následující příklad ukazuje odpovídající kód v .NET klienta.</span><span class="sxs-lookup"><span data-stu-id="5249d-400">The following example shows the equivalent code in a .NET client.</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample57.cs?highlight=1-2)]

<span data-ttu-id="5249d-401">Ve třídě Hub, můžete přístup k těmto datům v `Clients.Caller` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5249d-401">In your Hub class, you can access this data in the `Clients.Caller` property.</span></span> <span data-ttu-id="5249d-402">Následující příklad ukazuje kód, který načte stav uvedené v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="5249d-402">The following example shows code that retrieves the state referred to in the previous example.</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample58.cs?highlight=3-4)]

> [!NOTE]
> <span data-ttu-id="5249d-403">Tento mechanismus pro zachování stavu není určena pro velké objemy dat, od všechno, co vložíte do `state` nebo `Clients.Caller` vlastnost je odbavovaná se každé volání metody.</span><span class="sxs-lookup"><span data-stu-id="5249d-403">This mechanism for persisting state is not intended for large amounts of data, since everything you put in the `state` or `Clients.Caller` property is round-tripped with every method invocation.</span></span> <span data-ttu-id="5249d-404">Je vhodné pro menší položky, jako jsou uživatelská jména nebo čítače.</span><span class="sxs-lookup"><span data-stu-id="5249d-404">It's useful for smaller items such as user names or counters.</span></span>


<span data-ttu-id="5249d-405">VB.NET nebo v centru se silnými typy, volající objekt stavu nelze přistupovat prostřednictvím `Clients.Caller`; místo toho použijte `Clients.CallerState` (představíme v SignalR 2.1):</span><span class="sxs-lookup"><span data-stu-id="5249d-405">In VB.NET or in a strongly-typed hub, the caller state object can't be accessed through `Clients.Caller`; instead, use `Clients.CallerState` (introduced in SignalR 2.1):</span></span>

<span data-ttu-id="5249d-406">**Pomocí CallerState v jazyce C#**</span><span class="sxs-lookup"><span data-stu-id="5249d-406">**Using CallerState in C#**</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample59.cs?highlight=3-4)]

<span data-ttu-id="5249d-407">**Pomocí CallerState v jazyce Visual Basic**</span><span class="sxs-lookup"><span data-stu-id="5249d-407">**Using CallerState in Visual Basic**</span></span>

[!code-vb[Main](hubs-api-guide-server/samples/sample60.vb)]

<a id="handleErrors"></a>

## <a name="how-to-handle-errors-in-the-hub-class"></a><span data-ttu-id="5249d-408">Zpracování chyb ve třídě centra</span><span class="sxs-lookup"><span data-stu-id="5249d-408">How to handle errors in the Hub class</span></span>

<span data-ttu-id="5249d-409">Zpracování chyb, ke kterým dochází ve vašich metodách rozbočovače třídy, nejprve zkontrolujte můžete "sledovat" všechny výjimky v asynchronních operací (například volání metody klienta) s použitím `await`.</span><span class="sxs-lookup"><span data-stu-id="5249d-409">To handle errors that occur in your Hub class methods, first ensure you "observe" any exceptions from async operations (such as invoking client methods) by using `await`.</span></span> <span data-ttu-id="5249d-410">Použije jeden nebo více z následujících metod:</span><span class="sxs-lookup"><span data-stu-id="5249d-410">Then use one or more of the following methods:</span></span>

- <span data-ttu-id="5249d-411">Zabalit váš kód metody do bloků try-catch a protokolu objekt výjimky.</span><span class="sxs-lookup"><span data-stu-id="5249d-411">Wrap your method code in try-catch blocks and log the exception object.</span></span> <span data-ttu-id="5249d-412">Pro účely ladění může posílat výjimku do klienta, ale pro zabezpečení se nedoporučuje z důvodů odeslání podrobné informace pro klienty v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="5249d-412">For debugging purposes you can send the exception to the client, but for security reasons sending detailed information to clients in production is not recommended.</span></span>
- <span data-ttu-id="5249d-413">Vytvoření modulu kanálu rozbočovače, který zpracovává [OnIncomingError](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubpipelinemodule.onincomingerror(v=vs.111).aspx) metody.</span><span class="sxs-lookup"><span data-stu-id="5249d-413">Create a Hubs pipeline module that handles the [OnIncomingError](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubpipelinemodule.onincomingerror(v=vs.111).aspx) method.</span></span> <span data-ttu-id="5249d-414">Následující příklad ukazuje modulu kanálu, který protokoluje chyby, za nímž následuje kód v souboru Startup.cs, který se vloží do kanálu rozbočovače modul.</span><span class="sxs-lookup"><span data-stu-id="5249d-414">The following example shows a pipeline module that logs errors, followed by code in Startup.cs that injects the module into the Hubs pipeline.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample61.cs)]

    [!code-csharp[Main](hubs-api-guide-server/samples/sample62.cs?highlight=4)]
- <span data-ttu-id="5249d-415">Použití `HubException` třídy (zaveden v SignalR 2).</span><span class="sxs-lookup"><span data-stu-id="5249d-415">Use the `HubException` class (introduced in SignalR 2).</span></span> <span data-ttu-id="5249d-416">Tato chyba může být vyvolána z jakékoli volání rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-416">This error can be thrown from any hub invocation.</span></span> <span data-ttu-id="5249d-417">`HubError` Konstruktoru přijímá řetězcovou zprávu a objektu pro ukládání dodatečné údaje o chybě.</span><span class="sxs-lookup"><span data-stu-id="5249d-417">The `HubError` constructor takes a string message, and an object for storing extra error data.</span></span> <span data-ttu-id="5249d-418">SignalR bude automaticky serializovat výjimku a ho odeslat klientovi, kde se bude používat odmítnout nebo selže volání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-418">SignalR will auto-serialize the exception and send it to the client, where it will be used to reject or fail the hub method invocation.</span></span>

    <span data-ttu-id="5249d-419">Následující ukázky kódu ukazují, jak vyvolat `HubException` během volání rozbočovače a způsob zpracování výjimek v jazyce JavaScript a .NET klientech.</span><span class="sxs-lookup"><span data-stu-id="5249d-419">The following code samples demonstrate how to throw a `HubException` during a Hub invocation, and how to handle the exception on JavaScript and .NET clients.</span></span>

    <span data-ttu-id="5249d-420">**Ukázka třídy HubException kódu serveru**</span><span class="sxs-lookup"><span data-stu-id="5249d-420">**Server code demonstrating the HubException class**</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample63.cs)]

    <span data-ttu-id="5249d-421">**Ukázka reakci na vyvolání HubException v rozbočovači kódu klienta jazyka JavaScript**</span><span class="sxs-lookup"><span data-stu-id="5249d-421">**JavaScript client code demonstrating response to throwing a HubException in a hub**</span></span>

    [!code-html[Main](hubs-api-guide-server/samples/sample64.html)]

    <span data-ttu-id="5249d-422">**Ukázka reakci na vyvolání HubException v rozbočovači kód klienta .NET**</span><span class="sxs-lookup"><span data-stu-id="5249d-422">**.NET client code demonstrating response to throwing a HubException in a hub**</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample65.cs)]

<span data-ttu-id="5249d-423">Další informace o modulech kanálu rozbočovače, naleznete v tématu [přizpůsobení kanálu rozbočovače](#hubpipeline) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="5249d-423">For more information about Hub pipeline modules, see [How to customize the Hubs pipeline](#hubpipeline) later in this topic.</span></span>

<a id="tracing"></a>

## <a name="how-to-enable-tracing"></a><span data-ttu-id="5249d-424">Jak povolit trasování</span><span class="sxs-lookup"><span data-stu-id="5249d-424">How to enable tracing</span></span>

<span data-ttu-id="5249d-425">Povolení trasování na straně serveru, přidejte System.Diagnostics – element do souboru Web.config, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="5249d-425">To enable server-side tracing, add a system.diagnostics element to your Web.config file, as shown in this example:</span></span>

[!code-html[Main](hubs-api-guide-server/samples/sample66.html?highlight=17-72)]

<span data-ttu-id="5249d-426">Když aplikaci spouštíte v sadě Visual Studio, můžete zobrazit v protokolech **výstup** okna.</span><span class="sxs-lookup"><span data-stu-id="5249d-426">When you run the application in Visual Studio, you can view the logs in the **Output** window.</span></span>

<a id="callfromoutsidehub"></a>

## <a name="how-to-call-client-methods-and-manage-groups-from-outside-the-hub-class"></a><span data-ttu-id="5249d-427">Jak volat metody klienta a spravovat skupiny mimo třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-427">How to call client methods and manage groups from outside the Hub class</span></span>

<span data-ttu-id="5249d-428">Volání metody klienta z jinou třídu než vaše třída rozbočovače, získejte odkaz na objekt kontextu SignalR pro rozbočovač a použijte ho k volání metody na straně klienta nebo spravovat skupiny.</span><span class="sxs-lookup"><span data-stu-id="5249d-428">To call client methods from a different class than your Hub class, get a reference to the SignalR context object for the Hub and use that to call methods on the client or manage groups.</span></span>

<span data-ttu-id="5249d-429">Následující ukázka `StockTicker` třídy získá objekt context, uloží je v instanci třídy, ukládá instance třídy ve statické vlastnosti a používá kontext z instance třídy singleton, aby volala `updateStockPrice` metodu na klienty, kteří jsou připojení k rozbočovači s názvem `StockTickerHub`.</span><span class="sxs-lookup"><span data-stu-id="5249d-429">The following sample `StockTicker` class gets the context object, stores it in an instance of the class, stores the class instance in a static property, and uses the context from the singleton class instance to call the `updateStockPrice` method on clients that are connected to a Hub named `StockTickerHub`.</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample67.cs?highlight=8,24)]

<span data-ttu-id="5249d-430">Pokud budete muset použít kontext více a časy v objektu s dlouhým poločasem rozpadu, získat odkaz na jednou a uložte spíše získání ho znovu pokaždé, když.</span><span class="sxs-lookup"><span data-stu-id="5249d-430">If you need to use the context multiple-times in a long-lived object, get the reference once and save it rather than getting it again each time.</span></span> <span data-ttu-id="5249d-431">Po získání kontextu zajistí, že SignalR odesílá zprávy do klientů ve stejném pořadí, ve kterém zkontrolujte svoje metody rozbočovače klienta volání metod.</span><span class="sxs-lookup"><span data-stu-id="5249d-431">Getting the context once ensures that SignalR sends messages to clients in the same sequence in which your Hub methods make client method invocations.</span></span> <span data-ttu-id="5249d-432">Kurz ukazuje, jak používat objekt context SignalR pro rozbočovač, najdete v tématu [serverové vysílání s knihovnou ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="5249d-432">For a tutorial that shows how to use the SignalR context for a Hub, see [Server Broadcast with ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).</span></span>

<a id="callingclientsoutsidehub"></a>

### <a name="calling-client-methods"></a><span data-ttu-id="5249d-433">Volání metody klienta</span><span class="sxs-lookup"><span data-stu-id="5249d-433">Calling client methods</span></span>

<span data-ttu-id="5249d-434">Můžete určit, kteří klienti dostanou vzdáleného volání Procedur, ale máte k dispozici méně možností než při volání z třídy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-434">You can specify which clients will receive the RPC, but you have fewer options than when you call from a Hub class.</span></span> <span data-ttu-id="5249d-435">Důvodem je, že kontextu není přidružen konkrétní volání z klienta, tak jakékoli metody, které vyžadují znalost aktuální ID připojení, například `Clients.Others`, nebo `Clients.Caller`, nebo `Clients.OthersInGroup`, nejsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="5249d-435">The reason for this is that the context is not associated with a particular call from a client, so any methods that require knowledge of the current connection ID, such as `Clients.Others`, or `Clients.Caller`, or `Clients.OthersInGroup`, are not available.</span></span> <span data-ttu-id="5249d-436">Jsou k dispozici následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="5249d-436">The following options are available:</span></span>

- <span data-ttu-id="5249d-437">Všichni připojení klienti.</span><span class="sxs-lookup"><span data-stu-id="5249d-437">All connected clients.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample68.cs)]
- <span data-ttu-id="5249d-438">Konkrétního klienta, které identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-438">A specific client identified by connection ID.</span></span>

    [!code-css[Main](hubs-api-guide-server/samples/sample69.css)]
- <span data-ttu-id="5249d-439">Všichni připojení klienti s výjimkou určitých klientech identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-439">All connected clients except the specified clients, identified by connection ID.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample70.cs)]
- <span data-ttu-id="5249d-440">Všichni připojení klienti do zadané skupiny.</span><span class="sxs-lookup"><span data-stu-id="5249d-440">All connected clients in a specified group.</span></span>

    [!code-css[Main](hubs-api-guide-server/samples/sample71.css)]
- <span data-ttu-id="5249d-441">Všechny připojené klienty v zadané skupině s výjimkou určitých klientech, identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="5249d-441">All connected clients in a specified group except specified clients, identified by connection ID.</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample72.cs)]

<span data-ttu-id="5249d-442">Při volání do vaší třídy bez centra z metody ve třídě centra, můžete předat ID aktuálního připojení a použít s `Clients.Client`, `Clients.AllExcept`, nebo `Clients.Group` pro simulaci `Clients.Caller`, `Clients.Others`, nebo `Clients.OthersInGroup`.</span><span class="sxs-lookup"><span data-stu-id="5249d-442">If you are calling into your non-Hub class from methods in your Hub class, you can pass in the current connection ID and use that with `Clients.Client`, `Clients.AllExcept`, or `Clients.Group` to simulate `Clients.Caller`, `Clients.Others`, or `Clients.OthersInGroup`.</span></span> <span data-ttu-id="5249d-443">V následujícím příkladu `MoveShapeHub` třídy předá ID připojení pro `Broadcaster` třídy tak, aby `Broadcaster` třídy můžete simulovat `Clients.Others`.</span><span class="sxs-lookup"><span data-stu-id="5249d-443">In the following example, the `MoveShapeHub` class passes the connection ID to the `Broadcaster` class so that the `Broadcaster` class can simulate `Clients.Others`.</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample73.cs?highlight=12,36)]

<a id="managinggroupsoutsidehub"></a>

### <a name="managing-group-membership"></a><span data-ttu-id="5249d-444">Správa členství ve skupině</span><span class="sxs-lookup"><span data-stu-id="5249d-444">Managing group membership</span></span>

<span data-ttu-id="5249d-445">Pro správu skupin máte stejné možnosti jako třída rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-445">For managing groups you have the same options as you do in a Hub class.</span></span>

- <span data-ttu-id="5249d-446">Přidání klienta do skupiny</span><span class="sxs-lookup"><span data-stu-id="5249d-446">Add a client to a group</span></span>

    [!code-csharp[Main](hubs-api-guide-server/samples/sample74.cs)]
- <span data-ttu-id="5249d-447">Odeberte klienta ze skupiny</span><span class="sxs-lookup"><span data-stu-id="5249d-447">Remove a client from a group</span></span>

    [!code-css[Main](hubs-api-guide-server/samples/sample75.css)]

<a id="hubpipeline"></a>

## <a name="how-to-customize-the-hubs-pipeline"></a><span data-ttu-id="5249d-448">Přizpůsobení kanálu rozbočovače</span><span class="sxs-lookup"><span data-stu-id="5249d-448">How to customize the Hubs pipeline</span></span>

<span data-ttu-id="5249d-449">Funkce SignalR umožňuje vložit vlastní kód do kanálu rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5249d-449">SignalR enables you to inject your own code into the Hub pipeline.</span></span> <span data-ttu-id="5249d-450">Následující příklad ukazuje vlastní modul kanálu rozbočovače, který zaznamenává každou příchozí volání metody přijatých z klienta a odchozích volání metody vyvolat na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="5249d-450">The following example shows a custom Hub pipeline module that logs each incoming method call received from the client and outgoing method call invoked on the client:</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample76.cs)]

<span data-ttu-id="5249d-451">Následující kód na *Startup.cs* souboru registruje modul pro spuštění v kanálu rozbočovače:</span><span class="sxs-lookup"><span data-stu-id="5249d-451">The following code in the *Startup.cs* file registers the module to run in the Hub pipeline:</span></span>

[!code-csharp[Main](hubs-api-guide-server/samples/sample77.cs?highlight=3)]

<span data-ttu-id="5249d-452">Existuje celá řada různých metod, které můžete přepsat.</span><span class="sxs-lookup"><span data-stu-id="5249d-452">There are many different methods that you can override.</span></span> <span data-ttu-id="5249d-453">Úplný seznam najdete v tématu [HubPipelineModule metody](https://msdn.microsoft.com/library/jj918633(v=vs.111).aspx).</span><span class="sxs-lookup"><span data-stu-id="5249d-453">For a complete list, see [HubPipelineModule Methods](https://msdn.microsoft.com/library/jj918633(v=vs.111).aspx).</span></span>
