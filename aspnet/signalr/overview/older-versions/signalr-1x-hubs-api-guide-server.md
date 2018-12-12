---
uid: signalr/overview/older-versions/signalr-1x-hubs-api-guide-server
title: Pokyny k rozhraní API Center SignalR technologie ASP.NET – Server (SignalR 1.x) | Dokumentace Microsoftu
author: pfletcher
description: Tento dokument obsahuje úvod do programování na straně serveru rozhraní API pro rozbočovače SignalR technologie ASP.NET SignalR verze 1.1, s demonstratin ukázky kódu...
ms.author: riande
ms.date: 04/17/2013
ms.assetid: 03e4b9f5-0fea-4d94-959f-014b2762a301
msc.legacyurl: /signalr/overview/older-versions/signalr-1x-hubs-api-guide-server
msc.type: authoredcontent
ms.openlocfilehash: a51a2077e0b6cde80bc679e3a310c0c804d19d68
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53288019"
---
<a name="aspnet-signalr-hubs-api-guide---server-signalr-1x"></a><span data-ttu-id="a23e9-103">Pokyny k rozhraní API Center SignalR technologie ASP.NET – Server (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="a23e9-103">ASP.NET SignalR Hubs API Guide - Server (SignalR 1.x)</span></span>
====================
<span data-ttu-id="a23e9-104">podle [Patrick Fletcher](https://github.com/pfletcher), [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="a23e9-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="a23e9-105">Tento dokument obsahuje úvod do programování na straně serveru rozhraní API pro rozbočovače SignalR technologie ASP.NET SignalR verze 1.1, s představením běžných možností ukázky kódu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-105">This document provides an introduction to programming the server side of the ASP.NET SignalR Hubs API for SignalR version 1.1, with code samples demonstrating common options.</span></span>
> 
> <span data-ttu-id="a23e9-106">Rozhraní API pro rozbočovače SignalR umožňuje vytvářet vzdálených volání procedur (RPC) ze serveru pro připojené klienty a z klientů k serveru.</span><span class="sxs-lookup"><span data-stu-id="a23e9-106">The SignalR Hubs API enables you to make remote procedure calls (RPCs) from a server to connected clients and from clients to the server.</span></span> <span data-ttu-id="a23e9-107">V serverovém kódu můžete definovat metody, které mohou být volány klientů a volat metody, které běží na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a23e9-107">In server code, you define methods that can be called by clients, and you call methods that run on the client.</span></span> <span data-ttu-id="a23e9-108">V klientském kódu můžete definovat metody, které lze volat ze serveru a volání metody, které běží na serveru.</span><span class="sxs-lookup"><span data-stu-id="a23e9-108">In client code, you define methods that can be called from the server, and you call methods that run on the server.</span></span> <span data-ttu-id="a23e9-109">Funkce SignalR postará za vás zajistí funkčnost systému klient server.</span><span class="sxs-lookup"><span data-stu-id="a23e9-109">SignalR takes care of all of the client-to-server plumbing for you.</span></span>
> 
> <span data-ttu-id="a23e9-110">Funkce SignalR také nabízí nižší úrovně rozhraní API volá trvalé připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-110">SignalR also offers a lower-level API called Persistent Connections.</span></span> <span data-ttu-id="a23e9-111">Úvod do SignalR, rozbočovačů a trvalá připojení, nebo kurz, který ukazuje, jak sestavit kompletní aplikace SignalR, přečtěte si téma [SignalR – Začínáme](index.md).</span><span class="sxs-lookup"><span data-stu-id="a23e9-111">For an introduction to SignalR, Hubs, and Persistent Connections, or for a tutorial that shows how to build a complete SignalR application, see [SignalR - Getting Started](index.md).</span></span>


## <a name="overview"></a><span data-ttu-id="a23e9-112">Přehled</span><span class="sxs-lookup"><span data-stu-id="a23e9-112">Overview</span></span>

<span data-ttu-id="a23e9-113">Tento dokument obsahuje následující části:</span><span class="sxs-lookup"><span data-stu-id="a23e9-113">This document contains the following sections:</span></span>

- [<span data-ttu-id="a23e9-114">Jak zaregistrovat směrování funkce SignalR a nakonfigurovat možnosti SignalR</span><span class="sxs-lookup"><span data-stu-id="a23e9-114">How to register the SignalR route and configure SignalR options</span></span>](#route)

    - [<span data-ttu-id="a23e9-115">Adresa URL /signalr</span><span class="sxs-lookup"><span data-stu-id="a23e9-115">The /signalr URL</span></span>](#signalrurl)
    - [<span data-ttu-id="a23e9-116">Konfigurace možností SignalR</span><span class="sxs-lookup"><span data-stu-id="a23e9-116">Configuring SignalR options</span></span>](#options)
- [<span data-ttu-id="a23e9-117">Jak vytvořit a použít třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-117">How to create and use Hub classes</span></span>](#hubclass)

    - [<span data-ttu-id="a23e9-118">Doba života objektu centra</span><span class="sxs-lookup"><span data-stu-id="a23e9-118">Hub object lifetime</span></span>](#transience)
    - [<span data-ttu-id="a23e9-119">Camel-malých a velkých písmen názvů centra v klientech jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="a23e9-119">Camel-casing of Hub names in JavaScript clients</span></span>](#hubnames)
    - [<span data-ttu-id="a23e9-120">Více rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-120">Multiple Hubs</span></span>](#multiplehubs)
- [<span data-ttu-id="a23e9-121">Definování metody ve třídě rozbočovače, která může volat klientů</span><span class="sxs-lookup"><span data-stu-id="a23e9-121">How to define methods in the Hub class that clients can call</span></span>](#hubmethods)

    - [<span data-ttu-id="a23e9-122">Camel-malých a velkých písmen názvů metody do klientů JavaScript</span><span class="sxs-lookup"><span data-stu-id="a23e9-122">Camel-casing of method names in JavaScript clients</span></span>](#methodnames)
    - [<span data-ttu-id="a23e9-123">Kdy se má spustit asynchronně</span><span class="sxs-lookup"><span data-stu-id="a23e9-123">When to execute asynchronously</span></span>](#asyncmethods)
    - [<span data-ttu-id="a23e9-124">Definování přetížení</span><span class="sxs-lookup"><span data-stu-id="a23e9-124">Defining overloads</span></span>](#overloads)
- [<span data-ttu-id="a23e9-125">Volání metody klienta z třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-125">How to call client methods from the Hub class</span></span>](#callfromhub)

    - [<span data-ttu-id="a23e9-126">Výběr klientů, kteří obdrží vzdálené volání Procedur</span><span class="sxs-lookup"><span data-stu-id="a23e9-126">Selecting which clients will receive the RPC</span></span>](#selectingclients)
    - [<span data-ttu-id="a23e9-127">Žádné názvy metod ověřování za kompilace</span><span class="sxs-lookup"><span data-stu-id="a23e9-127">No compile-time validation for method names</span></span>](#dynamicmethodnames)
    - [<span data-ttu-id="a23e9-128">Shoda názvu malá a velká písmena – metoda</span><span class="sxs-lookup"><span data-stu-id="a23e9-128">Case-insensitive method name matching</span></span>](#caseinsensitive)
    - [<span data-ttu-id="a23e9-129">Asynchronní provádění</span><span class="sxs-lookup"><span data-stu-id="a23e9-129">Asynchronous execution</span></span>](#asyncclient)
- [<span data-ttu-id="a23e9-130">Správa členství ve skupině ze třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-130">How to manage group membership from the Hub class</span></span>](#groupsfromhub)

    - [<span data-ttu-id="a23e9-131">Asynchronní provádění metody přidat a odebrat</span><span class="sxs-lookup"><span data-stu-id="a23e9-131">Asynchronous execution of Add and Remove methods</span></span>](#asyncgroupmethods)
    - [<span data-ttu-id="a23e9-132">Trvalost členství ve skupině</span><span class="sxs-lookup"><span data-stu-id="a23e9-132">Group membership persistence</span></span>](#grouppersistence)
    - [<span data-ttu-id="a23e9-133">Skupiny jednoho uživatele</span><span class="sxs-lookup"><span data-stu-id="a23e9-133">Single-user groups</span></span>](#singleusergroups)
- [<span data-ttu-id="a23e9-134">Zpracování událostí doby platnosti ve třídě centra</span><span class="sxs-lookup"><span data-stu-id="a23e9-134">How to handle connection lifetime events in the Hub class</span></span>](#connectionlifetime)

    - [<span data-ttu-id="a23e9-135">Kdy jsou volány onconnected rozbočovače, ondisconnected rozbočovače a onreconnected rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-135">When OnConnected, OnDisconnected, and OnReconnected are called</span></span>](#onreconnected)
    - [<span data-ttu-id="a23e9-136">Stav volajícího není naplněn.</span><span class="sxs-lookup"><span data-stu-id="a23e9-136">Caller state not populated</span></span>](#nocallerstate)
- [<span data-ttu-id="a23e9-137">Jak získat informace o klientovi z kontextové vlastnosti</span><span class="sxs-lookup"><span data-stu-id="a23e9-137">How to get information about the client from the Context property</span></span>](#contextproperty)
- [<span data-ttu-id="a23e9-138">Jak předávat stavu mezi klienty a třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-138">How to pass state between clients and the Hub class</span></span>](#passstate)
- [<span data-ttu-id="a23e9-139">Zpracování chyb ve třídě centra</span><span class="sxs-lookup"><span data-stu-id="a23e9-139">How to handle errors in the Hub class</span></span>](#handleErrors)
- [<span data-ttu-id="a23e9-140">Jak volat metody klienta a spravovat skupiny mimo třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-140">How to call client methods and manage groups from outside the Hub class</span></span>](#callfromoutsidehub)

    - [<span data-ttu-id="a23e9-141">Volání metody klienta</span><span class="sxs-lookup"><span data-stu-id="a23e9-141">Calling client methods</span></span>](#callingclientsoutsidehub)
    - [<span data-ttu-id="a23e9-142">Správa členství ve skupině</span><span class="sxs-lookup"><span data-stu-id="a23e9-142">Managing group membership</span></span>](#managinggroupsoutsidehub)
- [<span data-ttu-id="a23e9-143">Jak povolit trasování</span><span class="sxs-lookup"><span data-stu-id="a23e9-143">How to enable tracing</span></span>](#tracing)
- [<span data-ttu-id="a23e9-144">Přizpůsobení kanálu rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-144">How to customize the Hubs pipeline</span></span>](#hubpipeline)

<span data-ttu-id="a23e9-145">Dokumentace o tom, jak program klientů naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="a23e9-145">For documentation on how to program clients, see the following resources:</span></span>

- [<span data-ttu-id="a23e9-146">Pokyny k rozhraní API Center SignalR – javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="a23e9-146">SignalR Hubs API Guide - JavaScript Client</span></span>](index.md)
- [<span data-ttu-id="a23e9-147">Pokyny k rozhraní API Center SignalR – klient .NET</span><span class="sxs-lookup"><span data-stu-id="a23e9-147">SignalR Hubs API Guide - .NET Client</span></span>](index.md)

<span data-ttu-id="a23e9-148">Odkazy na témata, Reference k rozhraní API se API verze rozhraní .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="a23e9-148">Links to API Reference topics are to the .NET 4.5 version of the API.</span></span> <span data-ttu-id="a23e9-149">Pokud používáte .NET 4, přečtěte si téma [verze .NET 4 témat API](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).</span><span class="sxs-lookup"><span data-stu-id="a23e9-149">If you're using .NET 4, see [the .NET 4 version of the API topics](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).</span></span>

<a id="route"></a>

## <a name="how-to-register-the-signalr-route-and-configure-signalr-options"></a><span data-ttu-id="a23e9-150">Jak zaregistrovat směrování funkce SignalR a nakonfigurovat možnosti SignalR</span><span class="sxs-lookup"><span data-stu-id="a23e9-150">How to register the SignalR route and configure SignalR options</span></span>

<span data-ttu-id="a23e9-151">Chcete-li definovat trasy, která budou klienti používat pro připojení k centru, zavolejte [MapHubs](https://msdn.microsoft.com/library/system.web.routing.signalrrouteextensions.maphubs(v=vs.111).aspx) metoda při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a23e9-151">To define the route that clients will use to connect to your Hub, call the [MapHubs](https://msdn.microsoft.com/library/system.web.routing.signalrrouteextensions.maphubs(v=vs.111).aspx) method when the application starts.</span></span> <span data-ttu-id="a23e9-152">`MapHubs` je [– metoda rozšíření](https://msdn.microsoft.com/library/vstudio/bb383977.aspx) pro `System.Web.Routing.RouteCollection` třídy.</span><span class="sxs-lookup"><span data-stu-id="a23e9-152">`MapHubs` is an [extension method](https://msdn.microsoft.com/library/vstudio/bb383977.aspx) for the `System.Web.Routing.RouteCollection` class.</span></span> <span data-ttu-id="a23e9-153">Následující příklad ukazuje, jak definovat trasu rozbočovače SignalR v *Global.asax* souboru.</span><span class="sxs-lookup"><span data-stu-id="a23e9-153">The following example shows how to define the SignalR Hubs route in the *Global.asax* file.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample1.cs)]

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample2.cs?highlight=5)]

<span data-ttu-id="a23e9-154">Pokud přidáváte funkce SignalR pro aplikace ASP.NET MVC, ujistěte se, přidá se trasa SignalR před jiným trasám.</span><span class="sxs-lookup"><span data-stu-id="a23e9-154">If you are adding SignalR functionality to an ASP.NET MVC application, make sure that the SignalR route is added before the other routes.</span></span> <span data-ttu-id="a23e9-155">Další informace najdete v tématu [kurzu: Začínáme s knihovnou SignalR a MVC 4](index.md).</span><span class="sxs-lookup"><span data-stu-id="a23e9-155">For more information, see [Tutorial: Getting Started with SignalR and MVC 4](index.md).</span></span>

<a id="signalrurl"></a>

### <a name="the-signalr-url"></a><span data-ttu-id="a23e9-156">Adresa URL /signalr</span><span class="sxs-lookup"><span data-stu-id="a23e9-156">The /signalr URL</span></span>

<span data-ttu-id="a23e9-157">Ve výchozím nastavení, je adresa URL trasy, které budou klienti používat pro připojení k vašemu centru "/ signalr".</span><span class="sxs-lookup"><span data-stu-id="a23e9-157">By default, the route URL which clients will use to connect to your Hub is "/signalr".</span></span> <span data-ttu-id="a23e9-158">(Nezaměňujte tuto adresu URL s adresou URL "/ signalr/centra", která je automaticky vygenerovaný soubor jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a23e9-158">(Don't confuse this URL with the "/signalr/hubs" URL, which is for the automatically generated JavaScript file.</span></span> <span data-ttu-id="a23e9-159">Další informace o vygenerovaný proxy server, naleznete v tématu [pokyny k rozhraní API Center SignalR – javascriptový klient - vygenerovaný proxy server a co to dělá za vás](index.md).)</span><span class="sxs-lookup"><span data-stu-id="a23e9-159">For more information about the generated proxy, see [SignalR Hubs API Guide - JavaScript Client - The generated proxy and what it does for you](index.md).)</span></span>

<span data-ttu-id="a23e9-160">Může být výjimečných případech, které zkontrolujte tuto základní adresu URL nelze použít pro funkci SignalR; například máte složku ve vašem projektu s názvem *signalr* a nechcete, aby změna názvu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-160">There might be extraordinary circumstances that make this base URL not usable for SignalR; for example, you have a folder in your project named *signalr* and you don't want to change the name.</span></span> <span data-ttu-id="a23e9-161">V takovém případě můžete změnit základní adresa URL, jak je znázorněno v následujících příkladech (nahradit "/ signalr" ve vzorovém kódu s požadovanou adresu URL).</span><span class="sxs-lookup"><span data-stu-id="a23e9-161">In that case, you can change the base URL, as shown in the following examples (replace "/signalr" in the sample code with your desired URL).</span></span>

<span data-ttu-id="a23e9-162">**Kód serveru, který určuje adresu URL**</span><span class="sxs-lookup"><span data-stu-id="a23e9-162">**Server code that specifies the URL**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample3.cs?highlight=1)]

<span data-ttu-id="a23e9-163">**Klientský kód jazyka JavaScript, který určuje adresu URL (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a23e9-163">**JavaScript client code that specifies the URL (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample4.js?highlight=1)]

<span data-ttu-id="a23e9-164">**Klientský kód jazyka JavaScript, který určuje adresu URL (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a23e9-164">**JavaScript client code that specifies the URL (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample5.js?highlight=1)]

<span data-ttu-id="a23e9-165">**Klientský kód .NET, který určuje adresu URL**</span><span class="sxs-lookup"><span data-stu-id="a23e9-165">**.NET client code that specifies the URL**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample6.cs?highlight=1)]

<a id="options"></a>

### <a name="configuring-signalr-options"></a><span data-ttu-id="a23e9-166">Konfigurace možností SignalR</span><span class="sxs-lookup"><span data-stu-id="a23e9-166">Configuring SignalR Options</span></span>

<span data-ttu-id="a23e9-167">Přetížení `MapHubs` metody umožňují určit vlastní adresu URL, překladače vlastní závislost a následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="a23e9-167">Overloads of the `MapHubs` method enable you to specify a custom URL, a custom dependency resolver, and the following options:</span></span>

- <span data-ttu-id="a23e9-168">Povolte volání mezi doménami z prohlížečů klientů.</span><span class="sxs-lookup"><span data-stu-id="a23e9-168">Enable cross-domain calls from browser clients.</span></span>

    <span data-ttu-id="a23e9-169">Obvykle Pokud prohlížeč načte stránku z `http://contoso.com`, připojení SignalR je ve stejné doméně, v `http://contoso.com/signalr`.</span><span class="sxs-lookup"><span data-stu-id="a23e9-169">Typically if the browser loads a page from `http://contoso.com`, the SignalR connection is in the same domain, at `http://contoso.com/signalr`.</span></span> <span data-ttu-id="a23e9-170">Pokud stránku z `http://contoso.com` vytvoří připojení k `http://fabrikam.com/signalr`, to znamená připojení mezi doménami.</span><span class="sxs-lookup"><span data-stu-id="a23e9-170">If the page from `http://contoso.com` makes a connection to `http://fabrikam.com/signalr`, that is a cross-domain connection.</span></span> <span data-ttu-id="a23e9-171">Z bezpečnostních důvodů jsou ve výchozím nastavení zakázané připojení mezi doménami.</span><span class="sxs-lookup"><span data-stu-id="a23e9-171">For security reasons, cross-domain connections are disabled by default.</span></span> <span data-ttu-id="a23e9-172">Další informace najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR – javascriptový klient – jak k navázání připojení mezi doménami](index.md).</span><span class="sxs-lookup"><span data-stu-id="a23e9-172">For more information, see [ASP.NET SignalR Hubs API Guide - JavaScript Client - How to establish a cross-domain connection](index.md).</span></span>
- <span data-ttu-id="a23e9-173">Povolte podrobné chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="a23e9-173">Enable detailed error messages.</span></span>

    <span data-ttu-id="a23e9-174">Když dojde k chybám, je výchozí chování SignalR klientům odeslat zprávu oznámení bez podrobnosti o co se stalo.</span><span class="sxs-lookup"><span data-stu-id="a23e9-174">When errors occur, the default behavior of SignalR is to send to clients a notification message without details about what happened.</span></span> <span data-ttu-id="a23e9-175">Odesílání podrobné informace o chybě pro klienty se nedoporučuje v produkčním prostředí, protože uživatelé se zlými úmysly může být schopni použijte informace v útoků, které vaše aplikace.</span><span class="sxs-lookup"><span data-stu-id="a23e9-175">Sending detailed error information to clients is not recommended in production, because malicious users might be able to use the information in attacks against your application.</span></span> <span data-ttu-id="a23e9-176">S řešením problémů, můžete použít tuto možnost dočasně povolit informativnější zasílání zpráv o chybách.</span><span class="sxs-lookup"><span data-stu-id="a23e9-176">For troubleshooting, you can use this option to temporarily enable more informative error reporting.</span></span>
- <span data-ttu-id="a23e9-177">Zakážete automaticky generovaných souborů proxy server JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a23e9-177">Disable automatically generated JavaScript proxy files.</span></span>

    <span data-ttu-id="a23e9-178">Ve výchozím nastavení je soubor JavaScript s proxy pro vaše Centrum třídy vygenerované v reakci na adresu URL "/ signalr/centra".</span><span class="sxs-lookup"><span data-stu-id="a23e9-178">By default, a JavaScript file with proxies for your Hub classes is generated in response to the URL "/signalr/hubs".</span></span> <span data-ttu-id="a23e9-179">Pokud už nechcete používat třídy proxy JavaScript, nebo pokud chcete vygenerovat soubor ručně a odkazovat na fyzický soubor v vašim klientům, můžete tuto možnost zakažte generování proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="a23e9-179">If you don't want to use the JavaScript proxies, or if you want to generate this file manually and refer to a physical file in your clients, you can use this option to disable proxy generation.</span></span> <span data-ttu-id="a23e9-180">Další informace najdete v tématu [pokyny k rozhraní API Center SignalR – javascriptový klient – proxy generované vytváření fyzického souboru pro funkci SignalR](index.md).</span><span class="sxs-lookup"><span data-stu-id="a23e9-180">For more information, see [SignalR Hubs API Guide - JavaScript Client - How to create a physical file for the SignalR generated proxy](index.md).</span></span>

<span data-ttu-id="a23e9-181">Následující příklad ukazuje, jak zadat adresu URL připojení SignalR a tyto možnosti ve volání `MapHubs` metody.</span><span class="sxs-lookup"><span data-stu-id="a23e9-181">The following example shows how to specify the SignalR connection URL and these options in a call to the `MapHubs` method.</span></span> <span data-ttu-id="a23e9-182">Chcete-li určit vlastní adresu URL, nahraďte "/ signalr" v příkladu nahraďte adresu URL, kterou chcete použít.</span><span class="sxs-lookup"><span data-stu-id="a23e9-182">To specify a custom URL, replace "/signalr" in the example with the URL that you want to use.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample7.cs)]

<a id="hubclass"></a>

## <a name="how-to-create-and-use-hub-classes"></a><span data-ttu-id="a23e9-183">Jak vytvořit a použít třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-183">How to create and use Hub classes</span></span>

<span data-ttu-id="a23e9-184">Pokud chcete Centrum vytvořit, vytvořit třídu, která je odvozena z [Microsoft.Aspnet.Signalr.Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx).</span><span class="sxs-lookup"><span data-stu-id="a23e9-184">To create a Hub, create a class that derives from [Microsoft.Aspnet.Signalr.Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx).</span></span> <span data-ttu-id="a23e9-185">Následující příklad ukazuje jednoduchý třída rozbočovače pro chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a23e9-185">The following example shows a simple Hub class for a chat application.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample8.cs)]

<span data-ttu-id="a23e9-186">V tomto příkladu můžete volat připojený klient `NewContosoChatMessage` metoda, a pokud ano, je přijatá data vysílali pro všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="a23e9-186">In this example, a connected client can call the `NewContosoChatMessage` method, and when it does, the data received is broadcasted to all connected clients.</span></span>

<a id="transience"></a>

### <a name="hub-object-lifetime"></a><span data-ttu-id="a23e9-187">Doba života objektu centra</span><span class="sxs-lookup"><span data-stu-id="a23e9-187">Hub object lifetime</span></span>

<span data-ttu-id="a23e9-188">Nevidíte vytvořit instanci třídy rozbočovače nebo volání obsažených metod z vlastního kódu na serveru. všechny možnosti, které se za vás provádí kanálu rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="a23e9-188">You don't instantiate the Hub class or call its methods from your own code on the server; all that is done for you by the SignalR Hubs pipeline.</span></span> <span data-ttu-id="a23e9-189">Funkce SignalR vytvoří novou instanci třídy rozbočovače pokaždé, když je potřeba zpracovat operaci rozbočovače, jako je například při připojení, odpojení klienta nebo je volána metoda k serveru.</span><span class="sxs-lookup"><span data-stu-id="a23e9-189">SignalR creates a new instance of your Hub class each time it needs to handle a Hub operation such as when a client connects, disconnects, or makes a method call to the server.</span></span>

<span data-ttu-id="a23e9-190">Protože instance třídy centra jsou přechodné, nelze je použít pro uchování stavu z jedné metody volání na další.</span><span class="sxs-lookup"><span data-stu-id="a23e9-190">Because instances of the Hub class are transient, you can't use them to maintain state from one method call to the next.</span></span> <span data-ttu-id="a23e9-191">Pokaždé, když server přijímá volání metody z klienta, novou instanci třídy procesů centra zprávy.</span><span class="sxs-lookup"><span data-stu-id="a23e9-191">Each time the server receives a method call from a client, a new instance of your Hub class processes the message.</span></span> <span data-ttu-id="a23e9-192">Pro uchování stavu prostřednictvím více připojení a volání metod, pomocí některé jiné metody, jako jsou databáze nebo statická proměnná v třídě rozbočovač nebo jinou třídu, která není odvozena od `Hub`.</span><span class="sxs-lookup"><span data-stu-id="a23e9-192">To maintain state through multiple connections and method calls, use some other method such as a database, or a static variable on the Hub class, or a different class that does not derive from `Hub`.</span></span> <span data-ttu-id="a23e9-193">Pokud můžete uchovávat data v paměti, způsobem například statická proměnná u třídy rozbočovače, data se ztratí dojde k recyklování domény aplikace.</span><span class="sxs-lookup"><span data-stu-id="a23e9-193">If you persist data in memory, using a method such as a static variable on the Hub class, the data will be lost when the app domain recycles.</span></span>

<span data-ttu-id="a23e9-194">Pokud chcete odesílat zprávy pro klienty z vlastní kód, který běží mimo třídy rozbočovače, nemůžete to udělal po vytvoření instance třídy instanci rozbočovače, ale dělejte to tím, že získáme odkaz na objekt kontextu SignalR pro rozbočovač třídu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-194">If you want to send messages to clients from your own code that runs outside the Hub class, you can't do it by instantiating a Hub class instance, but you can do it by getting a reference to the SignalR context object for your Hub class.</span></span> <span data-ttu-id="a23e9-195">Další informace najdete v tématu [klienta volat metody a Správa skupiny mimo třídy rozbočovače](#callfromoutsidehub) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-195">For more information, see [How to call client methods and manage groups from outside the Hub class](#callfromoutsidehub) later in this topic.</span></span>

<a id="hubnames"></a>

### <a name="camel-casing-of-hub-names-in-javascript-clients"></a><span data-ttu-id="a23e9-196">Camel-malých a velkých písmen názvů centra v klientech jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="a23e9-196">Camel-casing of Hub names in JavaScript clients</span></span>

<span data-ttu-id="a23e9-197">Ve výchozím nastavení klientů JavaScript odkazovat centra pomocí verze název třídy – ve formátu camelCase.</span><span class="sxs-lookup"><span data-stu-id="a23e9-197">By default, JavaScript clients refer to Hubs by using a camel-cased version of the class name.</span></span> <span data-ttu-id="a23e9-198">Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a23e9-198">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span> <span data-ttu-id="a23e9-199">V předchozím příkladu by se označuje jako `contosoChatHub` v kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a23e9-199">The previous example would be referred to as `contosoChatHub` in JavaScript code.</span></span>

<span data-ttu-id="a23e9-200">**Server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-200">**Server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample9.cs?highlight=1)]

<span data-ttu-id="a23e9-201">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-201">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample10.js?highlight=1)]

<span data-ttu-id="a23e9-202">Pokud chcete zadat jiný název pro klienty použít, přidejte `HubName` atribut.</span><span class="sxs-lookup"><span data-stu-id="a23e9-202">If you want to specify a different name for clients to use, add the `HubName` attribute.</span></span> <span data-ttu-id="a23e9-203">Při použití `HubName` atribut, není žádná změna název na formát camelCase u klientů jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a23e9-203">When you use a `HubName` attribute, there is no name change to camel case on JavaScript clients.</span></span>

<span data-ttu-id="a23e9-204">**Server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-204">**Server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample11.cs?highlight=1)]

<span data-ttu-id="a23e9-205">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-205">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample12.js?highlight=1)]

<a id="multiplehubs"></a>

### <a name="multiple-hubs"></a><span data-ttu-id="a23e9-206">Více rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-206">Multiple Hubs</span></span>

<span data-ttu-id="a23e9-207">V aplikaci můžete definovat více tříd rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a23e9-207">You can define multiple Hub classes in an application.</span></span> <span data-ttu-id="a23e9-208">Když to uděláte, připojení se sdílí, ale jsou oddělené skupiny:</span><span class="sxs-lookup"><span data-stu-id="a23e9-208">When you do that, the connection is shared but groups are separate:</span></span>

- <span data-ttu-id="a23e9-209">Budou všichni klienti používat k navázání připojení SignalR ve vaší službě stejná adresa URL ("/ signalr" nebo vaší vlastní adresu URL, pokud byl zadán), a používá se, že připojení pro všechna centra definovány službou.</span><span class="sxs-lookup"><span data-stu-id="a23e9-209">All clients will use the same URL to establish a SignalR connection with your service ("/signalr" or your custom URL if you specified one), and that connection is used for all Hubs defined by the service.</span></span>

    <span data-ttu-id="a23e9-210">Neexistuje žádné rozdíly ve výkonnosti více hubs ve srovnání s definování všechny funkce centra v jedné třídě.</span><span class="sxs-lookup"><span data-stu-id="a23e9-210">There is no performance difference for multiple Hubs compared to defining all Hub functionality in a single class.</span></span>
- <span data-ttu-id="a23e9-211">Všechna centra získat stejné informace o požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="a23e9-211">All Hubs get the same HTTP request information.</span></span>

    <span data-ttu-id="a23e9-212">Od všech centrech sdílejí stejné připojení, je pouze informace žádosti HTTP, která vrací na server, co se dodává v původní požadavek protokolu HTTP, která vytváří připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="a23e9-212">Since all Hubs share the same connection, the only HTTP request information that the server gets is what comes in the original HTTP request that establishes the SignalR connection.</span></span> <span data-ttu-id="a23e9-213">Pokud používáte žádosti o připojení k předávání informací od klienta k serveru zadáním řetězce dotazu, nemůže poskytnout různé řetězce dotazu do různých rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a23e9-213">If you use the connection request to pass information from the client to the server by specifying a query string, you can't provide different query strings to different Hubs.</span></span> <span data-ttu-id="a23e9-214">Všechna centra obdrží stejné informace.</span><span class="sxs-lookup"><span data-stu-id="a23e9-214">All Hubs will receive the same information.</span></span>
- <span data-ttu-id="a23e9-215">Vygenerovaný soubor JavaScript proxy bude obsahovat servery proxy pro všechna centra v jednom souboru.</span><span class="sxs-lookup"><span data-stu-id="a23e9-215">The generated JavaScript proxies file will contain proxies for all Hubs in one file.</span></span>

    <span data-ttu-id="a23e9-216">Informace o třídy proxy JavaScript naleznete v tématu [pokyny k rozhraní API Center SignalR – javascriptový klient - vygenerovaný proxy server a co to dělá za vás](index.md).</span><span class="sxs-lookup"><span data-stu-id="a23e9-216">For information about JavaScript proxies, see [SignalR Hubs API Guide - JavaScript Client - The generated proxy and what it does for you](index.md).</span></span>
- <span data-ttu-id="a23e9-217">Skupiny jsou definovány v rámci rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a23e9-217">Groups are defined within Hubs.</span></span>

    <span data-ttu-id="a23e9-218">V knihovně SignalR, které můžete definovat pojmenovaným skupinám na podmnožiny připojených klientů.</span><span class="sxs-lookup"><span data-stu-id="a23e9-218">In SignalR you can define named groups to broadcast to subsets of connected clients.</span></span> <span data-ttu-id="a23e9-219">Skupiny jsou spravovány samostatně pro každý rozbočovač.</span><span class="sxs-lookup"><span data-stu-id="a23e9-219">Groups are maintained separately for each Hub.</span></span> <span data-ttu-id="a23e9-220">Například skupina s názvem "Administrators" zahrnuje jednu sadu klientů pro vaše `ContosoChatHub` třídy a stejný název skupiny by odkazovat na jinou sadu klientů pro vaše `StockTickerHub` třídy.</span><span class="sxs-lookup"><span data-stu-id="a23e9-220">For example, a group named "Administrators" would include one set of clients for your `ContosoChatHub` class, and the same group name would refer to a different set of clients for your `StockTickerHub` class.</span></span>

<a id="hubmethods"></a>

## <a name="how-to-define-methods-in-the-hub-class-that-clients-can-call"></a><span data-ttu-id="a23e9-221">Definování metody ve třídě rozbočovače, která může volat klientů</span><span class="sxs-lookup"><span data-stu-id="a23e9-221">How to define methods in the Hub class that clients can call</span></span>

<span data-ttu-id="a23e9-222">Chcete-li vystavit metodu v rozbočovači, které mají být volány z klienta, deklarujte veřejnou metodu, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-222">To expose a method on the Hub that you want to be callable from the client, declare a public method, as shown in the following examples.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample13.cs?highlight=3)]

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample14.cs?highlight=3)]

<span data-ttu-id="a23e9-223">Můžete určit návratový typ a parametry, včetně komplexní typy a pole, stejně jako v jakékoli metodě jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="a23e9-223">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="a23e9-224">Všechna data, která zobrazí v parametrech nebo vrácení volajícímu je komunikace mezi klientem a serverem s použitím souboru JSON a SignalR automaticky zpracovává vazby složité objekty a pole objektů.</span><span class="sxs-lookup"><span data-stu-id="a23e9-224">Any data that you receive in parameters or return to the caller is communicated between the client and the server by using JSON, and SignalR handles the binding of complex objects and arrays of objects automatically.</span></span>

<a id="methodnames"></a>

### <a name="camel-casing-of-method-names-in-javascript-clients"></a><span data-ttu-id="a23e9-225">Camel-malých a velkých písmen názvů metody do klientů JavaScript</span><span class="sxs-lookup"><span data-stu-id="a23e9-225">Camel-casing of method names in JavaScript clients</span></span>

<span data-ttu-id="a23e9-226">Ve výchozím nastavení klienti JavaScript odkazovat metod rozbočovače na pomocí-ve formátu camelCase verzi název metody.</span><span class="sxs-lookup"><span data-stu-id="a23e9-226">By default, JavaScript clients refer to Hub methods by using a camel-cased version of the method name.</span></span> <span data-ttu-id="a23e9-227">Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a23e9-227">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="a23e9-228">**Server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-228">**Server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample15.cs?highlight=1)]

<span data-ttu-id="a23e9-229">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-229">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample16.js?highlight=1)]

<span data-ttu-id="a23e9-230">Pokud chcete zadat jiný název pro klienty použít, přidejte `HubMethodName` atribut.</span><span class="sxs-lookup"><span data-stu-id="a23e9-230">If you want to specify a different name for clients to use, add the `HubMethodName` attribute.</span></span>

<span data-ttu-id="a23e9-231">**Server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-231">**Server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample17.cs?highlight=1)]

<span data-ttu-id="a23e9-232">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-232">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample18.js?highlight=1)]

<a id="asyncmethods"></a>

### <a name="when-to-execute-asynchronously"></a><span data-ttu-id="a23e9-233">Kdy se má spustit asynchronně</span><span class="sxs-lookup"><span data-stu-id="a23e9-233">When to execute asynchronously</span></span>

<span data-ttu-id="a23e9-234">Metoda bude být dlouhotrvající nebo má práce, které by zahrnovat čekání, jako je například vyhledávání v databázi nebo volání webové služby, vrácením provést asynchronní metody rozbočovače [úloh](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) (místo `void` vrátit) nebo [ Úloha&lt;T&gt; ](https://msdn.microsoft.com/library/dd321424.aspx) objektu (místo `T` návratový typ).</span><span class="sxs-lookup"><span data-stu-id="a23e9-234">If the method will be long-running or has to do work that would involve waiting, such as a database lookup or a web service call, make the Hub method asynchronous by returning a [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) (in place of `void` return) or [Task&lt;T&gt;](https://msdn.microsoft.com/library/dd321424.aspx) object (in place of `T` return type).</span></span> <span data-ttu-id="a23e9-235">Po návratu `Task` objekt z metody SignalR čeká `Task` k dokončení, a pak pošle nezabalené výsledek zpět do klienta, takže není žádný rozdíl v tom, jak kód volání metody v klientovi.</span><span class="sxs-lookup"><span data-stu-id="a23e9-235">When you return a `Task` object from the method, SignalR waits for the `Task` to complete, and then it sends the unwrapped result back to the client, so there is no difference in how you code the method call in the client.</span></span>

<span data-ttu-id="a23e9-236">Provedení metody rozbočovače asynchronní se vyhnete blokuje připojení, když používá přenos pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a23e9-236">Making a Hub method asynchronous avoids blocking the connection when it uses the WebSocket transport.</span></span> <span data-ttu-id="a23e9-237">Když je přenos pomocí protokolu WebSocket centra metoda provedena synchronně, následné volání metod rozbočovače ze stejného klienta jsou blokovány, dokud dokončení metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a23e9-237">When a Hub method executes synchronously and the transport is WebSocket, subsequent invocations of methods on the Hub from the same client are blocked until the Hub method completes.</span></span>

<span data-ttu-id="a23e9-238">Následující příklad ukazuje stejnou metodu naprogramovány tak, aby běžely synchronně nebo asynchronně, za nímž následuje klientský kód jazyka JavaScript, který se dá použít pro volání jedné verze.</span><span class="sxs-lookup"><span data-stu-id="a23e9-238">The following example shows the same method coded to run synchronously or asynchronously, followed by JavaScript client code that works for calling either version.</span></span>

<span data-ttu-id="a23e9-239">**Synchronní**</span><span class="sxs-lookup"><span data-stu-id="a23e9-239">**Synchronous**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample19.cs)]

<span data-ttu-id="a23e9-240">**Asynchronní – ASP.NET 4.5**</span><span class="sxs-lookup"><span data-stu-id="a23e9-240">**Asynchronous - ASP.NET 4.5**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample20.cs?highlight=1,7-8)]

<span data-ttu-id="a23e9-241">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-241">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample21.js)]

<span data-ttu-id="a23e9-242">Další informace o tom, jak použít asynchronní metody v technologii ASP.NET 4.5 naleznete v tématu [použití asynchronních metod v architektuře ASP.NET MVC 4](../../../mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4.md).</span><span class="sxs-lookup"><span data-stu-id="a23e9-242">For more information about how to use asynchronous methods in ASP.NET 4.5, see [Using Asynchronous Methods in ASP.NET MVC 4](../../../mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4.md).</span></span>

<a id="overloads"></a>

### <a name="defining-overloads"></a><span data-ttu-id="a23e9-243">Definování přetížení</span><span class="sxs-lookup"><span data-stu-id="a23e9-243">Defining Overloads</span></span>

<span data-ttu-id="a23e9-244">Pokud chcete definovat přetížení pro metodu, musí být jiný počet parametrů v každé přetížení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-244">If you want to define overloads for a method, the number of parameters in each overload must be different.</span></span> <span data-ttu-id="a23e9-245">Rozlišení přetížení pouze zadáním různé typy parametrů, bude zkompilována vaše třída rozbočovače, ale služby SignalR vyvolá výjimku v době běhu, když se klienti pokusí volání jednoho z přetížení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-245">If you differentiate an overload just by specifying different parameter types, your Hub class will compile but the SignalR service will throw an exception at run time when clients try to call one of the overloads.</span></span>

<a id="callfromhub"></a>

## <a name="how-to-call-client-methods-from-the-hub-class"></a><span data-ttu-id="a23e9-246">Volání metody klienta z třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-246">How to call client methods from the Hub class</span></span>

<span data-ttu-id="a23e9-247">Chcete-li volat metody klienta ze serveru, použijte `Clients` vlastnost v metodě ve své třídě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a23e9-247">To call client methods from the server, use the `Clients` property in a method in your Hub class.</span></span> <span data-ttu-id="a23e9-248">Následující příklad ukazuje kód serveru, který volá `addNewMessageToPage` na všechny připojené klienty a klientský kód, který definuje metodu v klientovi JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a23e9-248">The following example shows server code that calls `addNewMessageToPage` on all connected clients, and client code that defines the method in a JavaScript client.</span></span>

<span data-ttu-id="a23e9-249">**Server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-249">**Server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample22.cs?highlight=5)]

<span data-ttu-id="a23e9-250">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-250">**JavaScript client using generated proxy**</span></span>

[!code-html[Main](signalr-1x-hubs-api-guide-server/samples/sample23.html?highlight=1)]

<span data-ttu-id="a23e9-251">Nelze získat návratovou hodnotu z metody; syntaxe jako `int x = Clients.All.add(1,1)` nefunguje.</span><span class="sxs-lookup"><span data-stu-id="a23e9-251">You can't get a return value from a client method; syntax such as `int x = Clients.All.add(1,1)` does not work.</span></span>

<span data-ttu-id="a23e9-252">Můžete zadat komplexní typy a pole parametrů.</span><span class="sxs-lookup"><span data-stu-id="a23e9-252">You can specify complex types and arrays for the parameters.</span></span> <span data-ttu-id="a23e9-253">Následující příklad předá komplexní typ klienta v parametru metody.</span><span class="sxs-lookup"><span data-stu-id="a23e9-253">The following example passes a complex type to the client in a method parameter.</span></span>

<span data-ttu-id="a23e9-254">**Kód serveru, který volá metodu klienta pomocí komplexního objektu**</span><span class="sxs-lookup"><span data-stu-id="a23e9-254">**Server code that calls a client method using a complex object**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample24.cs?highlight=3)]

<span data-ttu-id="a23e9-255">**Kód serveru, který definuje komplexní objekt**</span><span class="sxs-lookup"><span data-stu-id="a23e9-255">**Server code that defines the complex object**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample25.cs?highlight=1)]

<span data-ttu-id="a23e9-256">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-256">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample26.js?highlight=2-3)]

<a id="selectingclients"></a>

### <a name="selecting-which-clients-will-receive-the-rpc"></a><span data-ttu-id="a23e9-257">Výběr klientů, kteří obdrží vzdálené volání Procedur</span><span class="sxs-lookup"><span data-stu-id="a23e9-257">Selecting which clients will receive the RPC</span></span>

<span data-ttu-id="a23e9-258">Vrátí vlastnost klientů [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx) objekt, který poskytuje několik možností pro určení, které klienti obdrží vzdálené volání Procedur:</span><span class="sxs-lookup"><span data-stu-id="a23e9-258">The Clients property returns a [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx) object that provides several options for specifying which clients will receive the RPC:</span></span>

- <span data-ttu-id="a23e9-259">Všichni připojení klienti.</span><span class="sxs-lookup"><span data-stu-id="a23e9-259">All connected clients.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample27.cs)]
- <span data-ttu-id="a23e9-260">Pouze volajícího klienta.</span><span class="sxs-lookup"><span data-stu-id="a23e9-260">Only the calling client.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample28.cs)]
- <span data-ttu-id="a23e9-261">Všichni klienti s výjimkou volajícího klienta.</span><span class="sxs-lookup"><span data-stu-id="a23e9-261">All clients except the calling client.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample29.cs)]
- <span data-ttu-id="a23e9-262">Konkrétního klienta, které identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-262">A specific client identified by connection ID.</span></span>

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample30.css)]

    <span data-ttu-id="a23e9-263">Tento příklad příkladu volá `addContosoChatMessageToPage` na volajícího klienta a má stejný účinek jako použití `Clients.Caller`.</span><span class="sxs-lookup"><span data-stu-id="a23e9-263">This example calls `addContosoChatMessageToPage` on the calling client and has the same effect as using `Clients.Caller`.</span></span>
- <span data-ttu-id="a23e9-264">Všichni připojení klienti s výjimkou určitých klientech identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-264">All connected clients except the specified clients, identified by connection ID.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample31.cs)]
- <span data-ttu-id="a23e9-265">Všichni připojení klienti do zadané skupiny.</span><span class="sxs-lookup"><span data-stu-id="a23e9-265">All connected clients in a specified group.</span></span>

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample32.css)]
- <span data-ttu-id="a23e9-266">Všechny připojené klienty v zadané skupině s výjimkou určitých klientech identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-266">All connected clients in a specified group except the specified clients, identified by connection ID.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample33.cs)]
- <span data-ttu-id="a23e9-267">Všichni připojení klienti v zadané skupině s výjimkou volajícího klienta.</span><span class="sxs-lookup"><span data-stu-id="a23e9-267">All connected clients in a specified group except the calling client.</span></span>

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample34.css)]

<a id="dynamicmethodnames"></a>

### <a name="no-compile-time-validation-for-method-names"></a><span data-ttu-id="a23e9-268">Žádné názvy metod ověřování za kompilace</span><span class="sxs-lookup"><span data-stu-id="a23e9-268">No compile-time validation for method names</span></span>

<span data-ttu-id="a23e9-269">Název metody, který zadáte, je interpretován jako dynamický objekt, což znamená, že odpadá technologie IntelliSense a ověření za kompilace pro něj.</span><span class="sxs-lookup"><span data-stu-id="a23e9-269">The method name that you specify is interpreted as a dynamic object, which means there is no IntelliSense or compile-time validation for it.</span></span> <span data-ttu-id="a23e9-270">Výraz je vyhodnocen v době běhu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-270">The expression is evaluated at run time.</span></span> <span data-ttu-id="a23e9-271">Při volání metody, které spouští, SignalR odešle klientovi názvu metody a hodnoty parametrů, a pokud má klient metodu, která odpovídá názvu, že metoda je volána a hodnoty parametru jsou předány do něj.</span><span class="sxs-lookup"><span data-stu-id="a23e9-271">When the method call executes, SignalR sends the method name and the parameter values to the client, and if the client has a method that matches the name, that method is called and the parameter values are passed to it.</span></span> <span data-ttu-id="a23e9-272">Pokud na straně klienta se nenašla žádná odpovídající metoda, je vyvolána žádná chyba.</span><span class="sxs-lookup"><span data-stu-id="a23e9-272">If no matching method is found on the client, no error is raised.</span></span> <span data-ttu-id="a23e9-273">Informace o formátu dat, která SignalR odesílá do klienta na pozadí při volání metody klienta najdete v tématu [Úvod ke knihovně SignalR](index.md).</span><span class="sxs-lookup"><span data-stu-id="a23e9-273">For information about the format of the data that SignalR transmits to the client behind the scenes when you call a client method, see [Introduction to SignalR](index.md).</span></span>

<a id="caseinsensitive"></a>

### <a name="case-insensitive-method-name-matching"></a><span data-ttu-id="a23e9-274">Shoda názvu malá a velká písmena – metoda</span><span class="sxs-lookup"><span data-stu-id="a23e9-274">Case-insensitive method name matching</span></span>

<span data-ttu-id="a23e9-275">Shoda názvu metody je velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="a23e9-275">Method name matching is case-insensitive.</span></span> <span data-ttu-id="a23e9-276">Například `Clients.All.addContosoChatMessageToPage` na serveru spustí `AddContosoChatMessageToPage`, `addcontosochatmessagetopage`, nebo `addContosoChatMessageToPage` na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a23e9-276">For example, `Clients.All.addContosoChatMessageToPage` on the server will execute `AddContosoChatMessageToPage`, `addcontosochatmessagetopage`, or `addContosoChatMessageToPage` on the client.</span></span>

<a id="asyncclient"></a>

### <a name="asynchronous-execution"></a><span data-ttu-id="a23e9-277">Asynchronní provádění</span><span class="sxs-lookup"><span data-stu-id="a23e9-277">Asynchronous execution</span></span>

<span data-ttu-id="a23e9-278">Asynchronně provede metodu, kterou je možné volat.</span><span class="sxs-lookup"><span data-stu-id="a23e9-278">The method that you call executes asynchronously.</span></span> <span data-ttu-id="a23e9-279">Veškerý kód, který se dodává po volání metody do klienta se spustí okamžitě bez čekání na SignalR k dokončení přenosu dat do klientů, není-li určit, že následující řádky kódu by měla počkat na dokončení metody.</span><span class="sxs-lookup"><span data-stu-id="a23e9-279">Any code that comes after a method call to a client will execute immediately without waiting for SignalR to finish transmitting data to clients unless you specify that the subsequent lines of code should wait for method completion.</span></span> <span data-ttu-id="a23e9-280">Následující příklady kódu ukazují, jak provést dvě metody klienta postupně, ji pomocí kódu funkčnosti v rozhraní .NET 4.5 a ji pomocí kódu funkčnosti v rozhraní .NET 4.</span><span class="sxs-lookup"><span data-stu-id="a23e9-280">The following code examples show how to execute two client methods sequentially, one using code that works in .NET 4.5, and one using code that works in .NET 4.</span></span>

<span data-ttu-id="a23e9-281">**Příklad rozhraní .NET 4.5**</span><span class="sxs-lookup"><span data-stu-id="a23e9-281">**.NET 4.5 example**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample35.cs?highlight=1,3)]

<span data-ttu-id="a23e9-282">**Příklad rozhraní .NET 4**</span><span class="sxs-lookup"><span data-stu-id="a23e9-282">**.NET 4 example**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample36.cs?highlight=3-4)]

<span data-ttu-id="a23e9-283">Pokud používáte `await` nebo `ContinueWith` počkat, dokud se nedokončí metodu klienta předtím, než provede další řádek kódu, který nemusí znamenat, že klienti ve skutečnosti obdrží zprávu před provedením další řádek kódu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-283">If you use `await` or `ContinueWith` to wait until a client method finishes before the next line of code executes, that does not mean that clients will actually receive the message before the next line of code executes.</span></span> <span data-ttu-id="a23e9-284">"Dokončení" volání metody klienta pouze znamená, že SignalR provedla vše potřebné k odeslání zprávy.</span><span class="sxs-lookup"><span data-stu-id="a23e9-284">"Completion" of a client method call only means that SignalR has done everything necessary to send the message.</span></span> <span data-ttu-id="a23e9-285">Pokud potřebujete ověření, že klienti zobrazila zpráva, budete muset tento mechanismus program sami.</span><span class="sxs-lookup"><span data-stu-id="a23e9-285">If you need verification that clients received the message, you have to program that mechanism yourself.</span></span> <span data-ttu-id="a23e9-286">Například může kód `MessageReceived` metodu v rozbočovači a v `addContosoChatMessageToPage` metody na straně klienta lze volat `MessageReceived` po provedení činnost, kterou je třeba provést na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a23e9-286">For example, you could code a `MessageReceived` method on the Hub, and in the `addContosoChatMessageToPage` method on the client you could call `MessageReceived` after you do whatever work you need to do on the client.</span></span> <span data-ttu-id="a23e9-287">V `MessageReceived` v centru vám pomůžou práci závisí na skutečným klientem příjem a zpracování původní volání metody.</span><span class="sxs-lookup"><span data-stu-id="a23e9-287">In `MessageReceived` in the Hub you can do whatever work depends on actual client reception and processing of the original method call.</span></span>

### <a name="how-to-use-a-string-variable-as-the-method-name"></a><span data-ttu-id="a23e9-288">Použití proměnné řetězce jako názvu – metoda</span><span class="sxs-lookup"><span data-stu-id="a23e9-288">How to use a string variable as the method name</span></span>

<span data-ttu-id="a23e9-289">Pokud chcete vyvolat metodu klienta s použitím proměnné řetězce jako názvu metody přetypování `Clients.All` (nebo `Clients.Others`, `Clients.Caller`atd) k `IClientProxy` a následně zavolat [Invoke (methodName, args...) ](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.iclientproxy.invoke(v=vs.111).aspx).</span><span class="sxs-lookup"><span data-stu-id="a23e9-289">If you want to invoke a client method by using a string variable as the method name, cast `Clients.All` (or `Clients.Others`, `Clients.Caller`, etc.) to `IClientProxy` and then call [Invoke(methodName, args...)](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.iclientproxy.invoke(v=vs.111).aspx).</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample37.cs)]

<a id="groupsfromhub"></a>

## <a name="how-to-manage-group-membership-from-the-hub-class"></a><span data-ttu-id="a23e9-290">Správa členství ve skupině ze třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-290">How to manage group membership from the Hub class</span></span>

<span data-ttu-id="a23e9-291">Skupinami v knihovně SignalR poskytuje metodu pro vysílání zpráv do zadaného podmnožiny připojených klientů.</span><span class="sxs-lookup"><span data-stu-id="a23e9-291">Groups in SignalR provide a method for broadcasting messages to specified subsets of connected clients.</span></span> <span data-ttu-id="a23e9-292">Skupina může mít libovolný počet klientů a klienta můžete mít libovolný počet skupin.</span><span class="sxs-lookup"><span data-stu-id="a23e9-292">A group can have any number of clients, and a client can be a member of any number of groups.</span></span>

<span data-ttu-id="a23e9-293">Chcete-li spravovat členství ve skupině, použijte [přidat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) a [odebrat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) metody poskytované objektem `Groups` vlastnosti třídy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a23e9-293">To manage group membership, use the [Add](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) and [Remove](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) methods provided by the `Groups` property of the Hub class.</span></span> <span data-ttu-id="a23e9-294">Následující příklad ukazuje `Groups.Add` a `Groups.Remove` metod používaných v metodách rozbočovače, které jsou volány kódem na straně klienta, za nímž následuje klientský kód jazyka JavaScript, který je volá.</span><span class="sxs-lookup"><span data-stu-id="a23e9-294">The following example shows the `Groups.Add` and `Groups.Remove` methods used in Hub methods that are called by client code, followed by JavaScript client code that calls them.</span></span>

<span data-ttu-id="a23e9-295">**Server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-295">**Server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample38.cs?highlight=5,10)]

<span data-ttu-id="a23e9-296">**JavaScript klienta s použitím vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="a23e9-296">**JavaScript client using generated proxy**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample39.js)]

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample40.js)]

<span data-ttu-id="a23e9-297">Není nutné explicitně vytvářet skupiny.</span><span class="sxs-lookup"><span data-stu-id="a23e9-297">You don't have to explicitly create groups.</span></span> <span data-ttu-id="a23e9-298">V platnosti skupiny se automaticky vytvoří při prvním zadejte jeho název ve volání `Groups.Add`, a bude odstraněn, když odeberete poslední připojení z členství v ní.</span><span class="sxs-lookup"><span data-stu-id="a23e9-298">In effect a group is automatically created the first time you specify its name in a call to `Groups.Add`, and it is deleted when you remove the last connection from membership in it.</span></span>

<span data-ttu-id="a23e9-299">Neexistuje žádné rozhraní API pro získání seznamu členství ve skupinách nebo seznam skupin.</span><span class="sxs-lookup"><span data-stu-id="a23e9-299">There is no API for getting a group membership list or a list of groups.</span></span> <span data-ttu-id="a23e9-300">Funkce SignalR odesílá zprávy do klientů a skupin na základě [modelu pub/sub](http://en.wikipedia.org/wiki/Publish/subscribe), a server neumožňuje spravovat seznam skupin nebo členství ve skupinách.</span><span class="sxs-lookup"><span data-stu-id="a23e9-300">SignalR sends messages to clients and groups based on a [pub/sub model](http://en.wikipedia.org/wiki/Publish/subscribe), and the server does not maintain lists of groups or group memberships.</span></span> <span data-ttu-id="a23e9-301">To pomáhá maximalizovat škálovatelnost, protože pokaždé, když přidáte uzel do webové farmy, jakýkoli stav, který udržuje SignalR musí být rozšířena na nový uzel.</span><span class="sxs-lookup"><span data-stu-id="a23e9-301">This helps maximize scalability, because whenever you add a node to a web farm, any state that SignalR maintains has to be propagated to the new node.</span></span>

<a id="asyncgroupmethods"></a>

### <a name="asynchronous-execution-of-add-and-remove-methods"></a><span data-ttu-id="a23e9-302">Asynchronní provádění metody přidat a odebrat</span><span class="sxs-lookup"><span data-stu-id="a23e9-302">Asynchronous execution of Add and Remove methods</span></span>

<span data-ttu-id="a23e9-303">`Groups.Add` a `Groups.Remove` metody spustit asynchronně.</span><span class="sxs-lookup"><span data-stu-id="a23e9-303">The `Groups.Add` and `Groups.Remove` methods execute asynchronously.</span></span> <span data-ttu-id="a23e9-304">Pokud chcete přidat klienta do skupiny a okamžitě odešle zprávu do klienta pomocí skupiny, budete muset Ujistěte se, že `Groups.Add` metoda skončí jako první.</span><span class="sxs-lookup"><span data-stu-id="a23e9-304">If you want to add a client to a group and immediately send a message to the client by using the group, you have to make sure that the `Groups.Add` method finishes first.</span></span> <span data-ttu-id="a23e9-305">Následující příklady kódu ukazují, jak to udělat, ji pomocí kódu, který funguje v rozhraní .NET 4.5 a pomocí kódu, který funguje v rozhraní .NET 4</span><span class="sxs-lookup"><span data-stu-id="a23e9-305">The following code examples show how to do that, one by using code that works in .NET 4.5, and one by using code that works in .NET 4</span></span>

<span data-ttu-id="a23e9-306">**Příklad rozhraní .NET 4.5**</span><span class="sxs-lookup"><span data-stu-id="a23e9-306">**.NET 4.5 example**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample41.cs?highlight=1,3)]

<span data-ttu-id="a23e9-307">**Příklad rozhraní .NET 4**</span><span class="sxs-lookup"><span data-stu-id="a23e9-307">**.NET 4 example**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample42.cs?highlight=3-4)]

<a id="grouppersistence"></a>

### <a name="group-membership-persistence"></a><span data-ttu-id="a23e9-308">Trvalost členství ve skupině</span><span class="sxs-lookup"><span data-stu-id="a23e9-308">Group membership persistence</span></span>

<span data-ttu-id="a23e9-309">Sleduje připojení SignalR, ne s uživateli, takže když chcete uživateli být ve stejné skupině pokaždé, když uživatel vytváří připojení, je nutné volat `Groups.Add` pokaždé, když uživatel vytvoří nové připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-309">SignalR tracks connections, not users, so if you want a user to be in the same group every time the user establishes a connection, you have to call `Groups.Add` every time the user establishes a new connection.</span></span>

<span data-ttu-id="a23e9-310">Po dočasné ztrátě připojení někdy SignalR můžete obnovit připojení automaticky.</span><span class="sxs-lookup"><span data-stu-id="a23e9-310">After a temporary loss of connectivity, sometimes SignalR can restore the connection automatically.</span></span> <span data-ttu-id="a23e9-311">V takovém případě SignalR je obnovení stejného připojení není vytvoření nového připojení, a proto se členství ve skupině klienta automaticky obnoví.</span><span class="sxs-lookup"><span data-stu-id="a23e9-311">In that case, SignalR is restoring the same connection, not establishing a new connection, and so the client's group membership is automatically restored.</span></span> <span data-ttu-id="a23e9-312">To je možné i v případě, že dočasné přerušení je restartování serveru nebo selhání, protože stav připojení pro každého klienta, včetně členství ve skupinách, je odbavovaná klientovi.</span><span class="sxs-lookup"><span data-stu-id="a23e9-312">This is possible even when the temporary break is the result of a server reboot or failure, because connection state for each client, including group memberships, is round-tripped to the client.</span></span> <span data-ttu-id="a23e9-313">Pokud server přestane fungovat a nahrazuje nový server, než vyprší časový limit připojení, může klient automaticky znovu připojit k novému serveru a znovu zaregistrovat ve skupinách, který je členem skupiny.</span><span class="sxs-lookup"><span data-stu-id="a23e9-313">If a server goes down and is replaced by a new server before the connection times out, a client can reconnect automatically to the new server and re-enroll in groups it is a member of.</span></span>

<span data-ttu-id="a23e9-314">Pokud po ztrátě připojení, nelze automaticky obnovit připojení, nebo při připojení vyprší časový limit, nebo při odpojení klienta (například když prohlížeči přejde na novou stránku), členství ve skupinách budou ztraceny.</span><span class="sxs-lookup"><span data-stu-id="a23e9-314">When a connection can't be restored automatically after a loss of connectivity, or when the connection times out, or when the client disconnects (for example, when a browser navigates to a new page), group memberships are lost.</span></span> <span data-ttu-id="a23e9-315">Při příštím připojení uživatele bude nové připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-315">The next time the user connects will be a new connection.</span></span> <span data-ttu-id="a23e9-316">Chcete-li udržovat členství ve skupinách, pokud se stejnému uživateli vytvoří nové připojení, sledování přidružení mezi uživatelů a skupin a členství ve skupinách obnovení pokaždé, když uživatel vytvoří nové připojení má vaše aplikace.</span><span class="sxs-lookup"><span data-stu-id="a23e9-316">To maintain group memberships when the same user establishes a new connection, your application has to track the associations between users and groups, and restore group memberships each time a user establishes a new connection.</span></span>

<span data-ttu-id="a23e9-317">Další informace o připojení a opětovná připojení najdete v tématu [způsob zpracování událostí doby platnosti ve třídě centra](#connectionlifetime) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-317">For more information about connections and reconnections, see [How to handle connection lifetime events in the Hub class](#connectionlifetime) later in this topic.</span></span>

<a id="singleusergroups"></a>

### <a name="single-user-groups"></a><span data-ttu-id="a23e9-318">Skupiny jednoho uživatele</span><span class="sxs-lookup"><span data-stu-id="a23e9-318">Single-user groups</span></span>

<span data-ttu-id="a23e9-319">Aplikace, které používají funkci SignalR obvykle nutné udržovat přehled o asociace mezi uživateli a připojení, pokud chcete zjistit, který uživatel odeslal zprávu a které uživatelé měli přijímání zprávy.</span><span class="sxs-lookup"><span data-stu-id="a23e9-319">Applications that use SignalR typically have to keep track of the associations between users and connections in order to know which user has sent a message and which user(s) should be receiving a message.</span></span> <span data-ttu-id="a23e9-320">Skupiny se používají v jednom ze dvou běžně používané vzory pro učinit.</span><span class="sxs-lookup"><span data-stu-id="a23e9-320">Groups are used in one of the two commonly used patterns for doing that.</span></span>

- <span data-ttu-id="a23e9-321">Skupiny jednoho uživatele.</span><span class="sxs-lookup"><span data-stu-id="a23e9-321">Single-user groups.</span></span>

    <span data-ttu-id="a23e9-322">Můžete zadat uživatelské jméno jako název skupiny a do skupiny přidat aktuální ID připojení, pokaždé, když uživatel připojí, nebo znovu připojí.</span><span class="sxs-lookup"><span data-stu-id="a23e9-322">You can specify the user name as the group name, and add the current connection ID to the group every time the user connects or reconnects.</span></span> <span data-ttu-id="a23e9-323">K odeslání zprávy pro uživatele, které odesíláte do skupiny.</span><span class="sxs-lookup"><span data-stu-id="a23e9-323">To send messages to the user you send to the group.</span></span> <span data-ttu-id="a23e9-324">Nevýhodou této metody je, že skupina nemá poskytují způsob, jak zjistit, zda uživatel je online nebo offline.</span><span class="sxs-lookup"><span data-stu-id="a23e9-324">A disadvantage of this method is that the group doesn't provide you with a way to find out if the user is online or offline.</span></span>
- <span data-ttu-id="a23e9-325">Sledujte přidružení mezi uživatelská jména a ID připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-325">Track associations between user names and connection IDs.</span></span>

    <span data-ttu-id="a23e9-326">Můžete ukládat přidružení mezi každé uživatelské jméno a ID jeden nebo více připojení ve slovníku nebo v databázi a aktualizace uložených dat pokaždé, když uživatel připojí nebo odpojí.</span><span class="sxs-lookup"><span data-stu-id="a23e9-326">You can store an association between each user name and one or more connection IDs in a dictionary or database, and update the stored data each time the user connects or disconnects.</span></span> <span data-ttu-id="a23e9-327">K odeslání zprávy uživateli zadat ID připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-327">To send messages to the user you specify the connection IDs.</span></span> <span data-ttu-id="a23e9-328">Nevýhodou této metody je, že přijímá větší množství paměti.</span><span class="sxs-lookup"><span data-stu-id="a23e9-328">A disadvantage of this method is that it takes more memory.</span></span>

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events-in-the-hub-class"></a><span data-ttu-id="a23e9-329">Zpracování událostí doby platnosti ve třídě centra</span><span class="sxs-lookup"><span data-stu-id="a23e9-329">How to handle connection lifetime events in the Hub class</span></span>

<span data-ttu-id="a23e9-330">Obvyklé důvody pro zpracování událostí doby platnosti jsou ke sledování, zda je uživatel připojen nebo Ne a mějte přehled o přidružení mezi uživatelská jména a ID připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-330">Typical reasons for handling connection lifetime events are to keep track of whether a user is connected or not, and to keep track of the association between user names and connection IDs.</span></span> <span data-ttu-id="a23e9-331">Váš vlastní kód spustit, když klienti připojovat nebo odpojovat, přepsat `OnConnected`, `OnDisconnected`, a `OnReconnected` třídy virtuální metody rozbočovače, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-331">To run your own code when clients connect or disconnect, override the `OnConnected`, `OnDisconnected`, and `OnReconnected` virtual methods of the Hub class, as shown in the following example.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample43.cs?highlight=3,14,22)]

<a id="onreconnected"></a>

### <a name="when-onconnected-ondisconnected-and-onreconnected-are-called"></a><span data-ttu-id="a23e9-332">Kdy jsou volány onconnected rozbočovače, ondisconnected rozbočovače a onreconnected rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-332">When OnConnected, OnDisconnected, and OnReconnected are called</span></span>

<span data-ttu-id="a23e9-333">Pokaždé, když prohlížeč přejde na novou stránku, nové připojení musí být zavedena, což znamená, že se spustí SignalR `OnDisconnected` následuje metoda `OnConnected` metoda.</span><span class="sxs-lookup"><span data-stu-id="a23e9-333">Each time a browser navigates to a new page, a new connection has to be established, which means SignalR will execute the `OnDisconnected` method followed by the `OnConnected` method.</span></span> <span data-ttu-id="a23e9-334">Funkce SignalR vždy vytvoří nové ID připojení, po vytvoření nového připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-334">SignalR always creates a new connection ID when a new connection is established.</span></span>

<span data-ttu-id="a23e9-335">`OnReconnected` Metoda je volána, když bude zjištěna dočasné přerušení připojení, které funkci SignalR může automaticky zotavit po, například když kabelu dočasně odpojení a opětovném připojení předtím, než vyprší časový limit připojení. `OnDisconnected` Metoda se volá, když je klient odpojen a SignalR nelze znovu připojit automaticky, například pokud prohlížeč přejde na novou stránku.</span><span class="sxs-lookup"><span data-stu-id="a23e9-335">The `OnReconnected` method is called when there has been a temporary break in connectivity that SignalR can automatically recover from, such as when a cable is temporarily disconnected and reconnected before the connection times out. The `OnDisconnected` method is called when the client is disconnected and SignalR can't automatically reconnect, such as when a browser navigates to a new page.</span></span> <span data-ttu-id="a23e9-336">Proto je možné pořadí událostí pro daného klienta `OnConnected`, `OnReconnected`, `OnDisconnected`; nebo `OnConnected`, `OnDisconnected`.</span><span class="sxs-lookup"><span data-stu-id="a23e9-336">Therefore, a possible sequence of events for a given client is `OnConnected`, `OnReconnected`, `OnDisconnected`; or `OnConnected`, `OnDisconnected`.</span></span> <span data-ttu-id="a23e9-337">Neuvidíte sekvence `OnConnected`, `OnDisconnected`, `OnReconnected` pro dané připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-337">You won't see the sequence `OnConnected`, `OnDisconnected`, `OnReconnected` for a given connection.</span></span>

<span data-ttu-id="a23e9-338">`OnDisconnected` Metoda nebude zavolána v některých případech, například když server ocitne mimo provoz nebo doména aplikace získá recyklován.</span><span class="sxs-lookup"><span data-stu-id="a23e9-338">The `OnDisconnected` method doesn't get called in some scenarios, such as when a server goes down or the App Domain gets recycled.</span></span> <span data-ttu-id="a23e9-339">Když jiný server je v řádku nebo doména aplikace dokončí jeho Koš, může být někteří klienti moci znovu připojit a aktivuje `OnReconnected` událostí.</span><span class="sxs-lookup"><span data-stu-id="a23e9-339">When another server comes on line or the App Domain completes its recycle, some clients may be able to reconnect and fire the `OnReconnected` event.</span></span>

<span data-ttu-id="a23e9-340">Další informace najdete v tématu [principy a zpracování událostí doby platnosti v knihovně SignalR](index.md).</span><span class="sxs-lookup"><span data-stu-id="a23e9-340">For more information, see [Understanding and Handling Connection Lifetime Events in SignalR](index.md).</span></span>

<a id="nocallerstate"></a>

### <a name="caller-state-not-populated"></a><span data-ttu-id="a23e9-341">Stav volajícího není naplněn.</span><span class="sxs-lookup"><span data-stu-id="a23e9-341">Caller state not populated</span></span>

<span data-ttu-id="a23e9-342">Metody zpracování událostí životního cyklu připojení se nazývají ze serveru, což znamená, že jakýkoli stav, kam si ukládáte `state` objektu na straně klienta nebude dosazeny `Caller` vlastnost na serveru.</span><span class="sxs-lookup"><span data-stu-id="a23e9-342">The connection lifetime event handler methods are called from the server, which means that any state that you put in the `state` object on the client will not be populated in the `Caller` property on the server.</span></span> <span data-ttu-id="a23e9-343">Informace o `state` objektu a `Caller` vlastnost, naleznete v tématu [jak předat stavu mezi klienty a třídy rozbočovače](#passstate) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-343">For information about the `state` object and the `Caller` property, see [How to pass state between clients and the Hub class](#passstate) later in this topic.</span></span>

<a id="contextproperty"></a>

## <a name="how-to-get-information-about-the-client-from-the-context-property"></a><span data-ttu-id="a23e9-344">Jak získat informace o klientovi z kontextové vlastnosti</span><span class="sxs-lookup"><span data-stu-id="a23e9-344">How to get information about the client from the Context property</span></span>

<span data-ttu-id="a23e9-345">Chcete-li získat informace o klientovi, použijte `Context` vlastnosti třídy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a23e9-345">To get information about the client, use the `Context` property of the Hub class.</span></span> <span data-ttu-id="a23e9-346">`Context` Vrátí vlastnost [HubCallerContext](https://msdn.microsoft.com/library/jj890883(v=vs.111).aspx) objekt, který poskytuje přístup k následujícím informacím:</span><span class="sxs-lookup"><span data-stu-id="a23e9-346">The `Context` property returns a [HubCallerContext](https://msdn.microsoft.com/library/jj890883(v=vs.111).aspx) object which provides access to the following information:</span></span>

- <span data-ttu-id="a23e9-347">ID připojení volajícího klienta.</span><span class="sxs-lookup"><span data-stu-id="a23e9-347">The connection ID of the calling client.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample44.cs?highlight=1)]

    <span data-ttu-id="a23e9-348">ID připojení je identifikátor GUID, který je přiřazen nástrojem SignalR (hodnota nelze zadat ve svém vlastním kódu).</span><span class="sxs-lookup"><span data-stu-id="a23e9-348">The connection ID is a GUID that is assigned by SignalR (you can't specify the value in your own code).</span></span> <span data-ttu-id="a23e9-349">Existuje jedno ID připojení pro každé připojení a stejného připojení ID se používá ve všech centrech, pokud máte více Center ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a23e9-349">There is one connection ID for each connection, and the same connection ID is used by all Hubs if you have multiple Hubs in your application.</span></span>
- <span data-ttu-id="a23e9-350">Data hlavičky protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a23e9-350">HTTP header data.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample45.cs?highlight=1)]

    <span data-ttu-id="a23e9-351">Můžete také získat hlavičky protokolu HTTP z `Context.Headers`.</span><span class="sxs-lookup"><span data-stu-id="a23e9-351">You can also get HTTP headers from `Context.Headers`.</span></span> <span data-ttu-id="a23e9-352">Z důvodu více odkazů na stejnou věc je, že `Context.Headers` byla vytvořena jako první, `Context.Request` vlastnost byla přidána později, a `Context.Headers` se zachovává kvůli zpětné kompatibilitě.</span><span class="sxs-lookup"><span data-stu-id="a23e9-352">The reason for multiple references to the same thing is that `Context.Headers` was created first, the `Context.Request` property was added later, and `Context.Headers` was retained for backward compatibility.</span></span>
- <span data-ttu-id="a23e9-353">Data řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-353">Query string data.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample46.cs?highlight=1)]

    <span data-ttu-id="a23e9-354">Můžete také získat data řetězce dotazu z `Context.QueryString`.</span><span class="sxs-lookup"><span data-stu-id="a23e9-354">You can also get query string data from `Context.QueryString`.</span></span>

    <span data-ttu-id="a23e9-355">Řetězec dotazu, který dostanete v této vlastnosti je ten, který byl použit v požadavku HTTP, který navázalo se připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="a23e9-355">The query string that you get in this property is the one that was used with the HTTP request that established the SignalR connection.</span></span> <span data-ttu-id="a23e9-356">Můžete přidat parametry řetězce dotazu v klientovi nakonfigurováním připojení, které je pohodlný způsob, jak předat data o klientovi z klienta na server.</span><span class="sxs-lookup"><span data-stu-id="a23e9-356">You can add query string parameters in the client by configuring the connection, which is a convenient way to pass data about the client from the client to the server.</span></span> <span data-ttu-id="a23e9-357">Následující příklad ukazuje jeden způsob, jak přidat řetězec dotazu v jazyce JavaScript klienta při použití vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="a23e9-357">The following example shows one way to add a query string in a JavaScript client when you use the generated proxy.</span></span>

    [!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample47.js?highlight=1)]

    <span data-ttu-id="a23e9-358">Další informace o nastavení parametrů řetězce dotazu, naleznete v tématu příručky rozhraní API pro [JavaScript](index.md) a [.NET](index.md) klientů.</span><span class="sxs-lookup"><span data-stu-id="a23e9-358">For more information about setting query string parameters, see the API guides for the [JavaScript](index.md) and [.NET](index.md) clients.</span></span>

    <span data-ttu-id="a23e9-359">Můžete najít metodu přenosu používá pro připojení v data řetězce dotazu spolu s některé jiné hodnoty používaná interně knihovnou SignalR:</span><span class="sxs-lookup"><span data-stu-id="a23e9-359">You can find the transport method used for the connection in the query string data, along with some other values used internally by SignalR:</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample48.cs)]

    <span data-ttu-id="a23e9-360">Hodnota `transportMethod` bude "webSockets", "serverSentEvents", "foreverFrame" nebo "longPolling".</span><span class="sxs-lookup"><span data-stu-id="a23e9-360">The value of `transportMethod` will be "webSockets", "serverSentEvents", "foreverFrame" or "longPolling".</span></span> <span data-ttu-id="a23e9-361">Poznámka: Pokud tuto hodnotu zkontrolovat `OnConnected` metoda obslužné rutiny událostí, v některých scénářích může být zpočátku získat hodnotu přenosu, který není konečný vyjednávaný přenosu metodu připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-361">Note that if you check this value in the `OnConnected` event handler method, in some scenarios you might initially get a transport value that is not the final negotiated transport method for the connection.</span></span> <span data-ttu-id="a23e9-362">V takovém případě metoda vyvolá výjimku a zavolá se později po vytvoření finální přepravy.</span><span class="sxs-lookup"><span data-stu-id="a23e9-362">In that case the method will throw an exception and will be called again later when the final transport method is established.</span></span>
- <span data-ttu-id="a23e9-363">Soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="a23e9-363">Cookies.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample49.cs?highlight=1)]

    <span data-ttu-id="a23e9-364">Můžete také získat soubory cookie z `Context.RequestCookies`.</span><span class="sxs-lookup"><span data-stu-id="a23e9-364">You can also get cookies from `Context.RequestCookies`.</span></span>
- <span data-ttu-id="a23e9-365">Informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="a23e9-365">User information.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample50.cs?highlight=1)]
- <span data-ttu-id="a23e9-366">Objektu HttpContext žádosti:</span><span class="sxs-lookup"><span data-stu-id="a23e9-366">The HttpContext object for the request :</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample51.cs?highlight=1)]

    <span data-ttu-id="a23e9-367">Tuto metodu použijte místo zobrazování `HttpContext.Current` zobrazíte `HttpContext` objektu pro připojení k systému SignalR.</span><span class="sxs-lookup"><span data-stu-id="a23e9-367">Use this method instead of getting `HttpContext.Current` to get the `HttpContext` object for the SignalR connection.</span></span>

<a id="passstate"></a>

## <a name="how-to-pass-state-between-clients-and-the-hub-class"></a><span data-ttu-id="a23e9-368">Jak předávat stavu mezi klienty a třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-368">How to pass state between clients and the Hub class</span></span>

<span data-ttu-id="a23e9-369">Poskytuje proxy serveru klienta `state` objektu, ve kterém můžete ukládat data, která chcete předávat na server se každé volání metody.</span><span class="sxs-lookup"><span data-stu-id="a23e9-369">The client proxy provides a `state` object in which you can store data that you want to be transmitted to the server with each method call.</span></span> <span data-ttu-id="a23e9-370">Na serveru můžete přístup k těmto datům v `Clients.Caller` vlastnost v metodách rozbočovače, které jsou volány klienty.</span><span class="sxs-lookup"><span data-stu-id="a23e9-370">On the server you can access this data in the `Clients.Caller` property in Hub methods that are called by clients.</span></span> <span data-ttu-id="a23e9-371">`Clients.Caller` Vlastnost není vyplněný pro metody zpracování událostí životního cyklu připojení `OnConnected`, `OnDisconnected`, a `OnReconnected`.</span><span class="sxs-lookup"><span data-stu-id="a23e9-371">The `Clients.Caller` property is not populated for the connection lifetime event handler methods `OnConnected`, `OnDisconnected`, and `OnReconnected`.</span></span>

<span data-ttu-id="a23e9-372">Vytváření nebo aktualizaci dat v `state` objektu a `Clients.Caller` vlastnost funguje v obou směrech.</span><span class="sxs-lookup"><span data-stu-id="a23e9-372">Creating or updating data in the `state` object and the `Clients.Caller` property works in both directions.</span></span> <span data-ttu-id="a23e9-373">Hodnoty na serveru můžete aktualizovat a jsou předány zpět do klienta.</span><span class="sxs-lookup"><span data-stu-id="a23e9-373">You can update values in the server and they are passed back to the client.</span></span>

<span data-ttu-id="a23e9-374">Následující příklad ukazuje klientský kód jazyka JavaScript, který ukládá stav přenosu na server se každé volání metody.</span><span class="sxs-lookup"><span data-stu-id="a23e9-374">The following example shows JavaScript client code that stores state for transmission to the server with every method call.</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample52.js?highlight=1-2)]

<span data-ttu-id="a23e9-375">Následující příklad ukazuje odpovídající kód v .NET klienta.</span><span class="sxs-lookup"><span data-stu-id="a23e9-375">The following example shows the equivalent code in a .NET client.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample53.cs?highlight=1-2)]

<span data-ttu-id="a23e9-376">Ve třídě Hub, můžete přístup k těmto datům v `Clients.Caller` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a23e9-376">In your Hub class, you can access this data in the `Clients.Caller` property.</span></span> <span data-ttu-id="a23e9-377">Následující příklad ukazuje kód, který načte stav uvedené v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-377">The following example shows code that retrieves the state referred to in the previous example.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample54.cs?highlight=3-4)]

> [!NOTE]
> <span data-ttu-id="a23e9-378">Tento mechanismus pro zachování stavu není určena pro velké objemy dat, od všechno, co vložíte do `state` nebo `Clients.Caller` vlastnost je odbavovaná se každé volání metody.</span><span class="sxs-lookup"><span data-stu-id="a23e9-378">This mechanism for persisting state is not intended for large amounts of data, since everything you put in the `state` or `Clients.Caller` property is round-tripped with every method invocation.</span></span> <span data-ttu-id="a23e9-379">Je vhodné pro menší položky, jako jsou uživatelská jména nebo čítače.</span><span class="sxs-lookup"><span data-stu-id="a23e9-379">It's useful for smaller items such as user names or counters.</span></span>


<a id="handleErrors"></a>

## <a name="how-to-handle-errors-in-the-hub-class"></a><span data-ttu-id="a23e9-380">Zpracování chyb ve třídě centra</span><span class="sxs-lookup"><span data-stu-id="a23e9-380">How to handle errors in the Hub class</span></span>

<span data-ttu-id="a23e9-381">Zpracování chyb, ke kterým dochází ve vašich metodách rozbočovače třída, používá jedno nebo obě z následujících metod:</span><span class="sxs-lookup"><span data-stu-id="a23e9-381">To handle errors that occur in your Hub class methods, use one or both of the following methods:</span></span>

- <span data-ttu-id="a23e9-382">Zabalit váš kód metody do bloků try-catch a protokolu objekt výjimky.</span><span class="sxs-lookup"><span data-stu-id="a23e9-382">Wrap your method code in try-catch blocks and log the exception object.</span></span> <span data-ttu-id="a23e9-383">Pro účely ladění může posílat výjimku do klienta, ale pro zabezpečení se nedoporučuje z důvodů odeslání podrobné informace pro klienty v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="a23e9-383">For debugging purposes you can send the exception to the client, but for security reasons sending detailed information to clients in production is not recommended.</span></span>
- <span data-ttu-id="a23e9-384">Vytvoření modulu kanálu rozbočovače, který zpracovává [OnIncomingError](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubpipelinemodule.onincomingerror(v=vs.111).aspx) metody.</span><span class="sxs-lookup"><span data-stu-id="a23e9-384">Create a Hubs pipeline module that handles the [OnIncomingError](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubpipelinemodule.onincomingerror(v=vs.111).aspx) method.</span></span> <span data-ttu-id="a23e9-385">Následující příklad ukazuje modulu kanálu, který protokoluje chyby, za nímž následuje kód v souboru Global.asax, který se vloží do kanálu rozbočovače modul.</span><span class="sxs-lookup"><span data-stu-id="a23e9-385">The following example shows a pipeline module that logs errors, followed by code in Global.asax that injects the module into the Hubs pipeline.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample55.cs)]

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample56.cs?highlight=3)]

<span data-ttu-id="a23e9-386">Další informace o modulech kanálu rozbočovače, naleznete v tématu [přizpůsobení kanálu rozbočovače](#hubpipeline) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a23e9-386">For more information about Hub pipeline modules, see [How to customize the Hubs pipeline](#hubpipeline) later in this topic.</span></span>

<a id="tracing"></a>

## <a name="how-to-enable-tracing"></a><span data-ttu-id="a23e9-387">Jak povolit trasování</span><span class="sxs-lookup"><span data-stu-id="a23e9-387">How to enable tracing</span></span>

<span data-ttu-id="a23e9-388">Povolení trasování na straně serveru, přidejte System.Diagnostics – element do souboru Web.config, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="a23e9-388">To enable server-side tracing, add a system.diagnostics element to your Web.config file, as shown in this example:</span></span>

[!code-html[Main](signalr-1x-hubs-api-guide-server/samples/sample57.html?highlight=17-72)]

<span data-ttu-id="a23e9-389">Když aplikaci spouštíte v sadě Visual Studio, můžete zobrazit v protokolech **výstup** okna.</span><span class="sxs-lookup"><span data-stu-id="a23e9-389">When you run the application in Visual Studio, you can view the logs in the **Output** window.</span></span>

<a id="callfromoutsidehub"></a>

## <a name="how-to-call-client-methods-and-manage-groups-from-outside-the-hub-class"></a><span data-ttu-id="a23e9-390">Jak volat metody klienta a spravovat skupiny mimo třídy rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-390">How to call client methods and manage groups from outside the Hub class</span></span>

<span data-ttu-id="a23e9-391">Volání metody klienta z jinou třídu než vaše třída rozbočovače, získejte odkaz na objekt kontextu SignalR pro rozbočovač a použijte ho k volání metody na straně klienta nebo spravovat skupiny.</span><span class="sxs-lookup"><span data-stu-id="a23e9-391">To call client methods from a different class than your Hub class, get a reference to the SignalR context object for the Hub and use that to call methods on the client or manage groups.</span></span>

<span data-ttu-id="a23e9-392">Následující ukázka `StockTicker` třídy získá objekt context, uloží je v instanci třídy, ukládá instance třídy ve statické vlastnosti a používá kontext z instance třídy singleton, aby volala `updateStockPrice` metodu na klienty, kteří jsou připojení k rozbočovači s názvem `StockTickerHub`.</span><span class="sxs-lookup"><span data-stu-id="a23e9-392">The following sample `StockTicker` class gets the context object, stores it in an instance of the class, stores the class instance in a static property, and uses the context from the singleton class instance to call the `updateStockPrice` method on clients that are connected to a Hub named `StockTickerHub`.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample58.cs?highlight=8,24)]

<span data-ttu-id="a23e9-393">Pokud budete muset použít kontext více a časy v objektu s dlouhým poločasem rozpadu, získat odkaz na jednou a uložte spíše získání ho znovu pokaždé, když.</span><span class="sxs-lookup"><span data-stu-id="a23e9-393">If you need to use the context multiple-times in a long-lived object, get the reference once and save it rather than getting it again each time.</span></span> <span data-ttu-id="a23e9-394">Po získání kontextu zajistí, že SignalR odesílá zprávy do klientů ve stejném pořadí, ve kterém zkontrolujte svoje metody rozbočovače klienta volání metod.</span><span class="sxs-lookup"><span data-stu-id="a23e9-394">Getting the context once ensures that SignalR sends messages to clients in the same sequence in which your Hub methods make client method invocations.</span></span> <span data-ttu-id="a23e9-395">Kurz ukazuje, jak používat objekt context SignalR pro rozbočovač, najdete v tématu [serverové vysílání s knihovnou ASP.NET SignalR](index.md).</span><span class="sxs-lookup"><span data-stu-id="a23e9-395">For a tutorial that shows how to use the SignalR context for a Hub, see [Server Broadcast with ASP.NET SignalR](index.md).</span></span>

<a id="callingclientsoutsidehub"></a>

### <a name="calling-client-methods"></a><span data-ttu-id="a23e9-396">Volání metody klienta</span><span class="sxs-lookup"><span data-stu-id="a23e9-396">Calling client methods</span></span>

<span data-ttu-id="a23e9-397">Můžete určit, kteří klienti dostanou vzdáleného volání Procedur, ale máte k dispozici méně možností než při volání z třídy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a23e9-397">You can specify which clients will receive the RPC, but you have fewer options than when you call from a Hub class.</span></span> <span data-ttu-id="a23e9-398">Důvodem je, že kontextu není přidružen konkrétní volání z klienta, tak jakékoli metody, které vyžadují znalost aktuální ID připojení, například `Clients.Others`, nebo `Clients.Caller`, nebo `Clients.OthersInGroup`, nejsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a23e9-398">The reason for this is that the context is not associated with a particular call from a client, so any methods that require knowledge of the current connection ID, such as `Clients.Others`, or `Clients.Caller`, or `Clients.OthersInGroup`, are not available.</span></span> <span data-ttu-id="a23e9-399">Jsou k dispozici následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="a23e9-399">The following options are available:</span></span>

- <span data-ttu-id="a23e9-400">Všichni připojení klienti.</span><span class="sxs-lookup"><span data-stu-id="a23e9-400">All connected clients.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample59.cs)]
- <span data-ttu-id="a23e9-401">Konkrétního klienta, které identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-401">A specific client identified by connection ID.</span></span>

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample60.css)]
- <span data-ttu-id="a23e9-402">Všichni připojení klienti s výjimkou určitých klientech identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-402">All connected clients except the specified clients, identified by connection ID.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample61.cs)]
- <span data-ttu-id="a23e9-403">Všichni připojení klienti do zadané skupiny.</span><span class="sxs-lookup"><span data-stu-id="a23e9-403">All connected clients in a specified group.</span></span>

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample62.css)]
- <span data-ttu-id="a23e9-404">Všechny připojené klienty v zadané skupině s výjimkou určitých klientech, identifikují pomocí ID připojení.</span><span class="sxs-lookup"><span data-stu-id="a23e9-404">All connected clients in a specified group except specified clients, identified by connection ID.</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample63.cs)]

<span data-ttu-id="a23e9-405">Při volání do vaší třídy bez centra z metody ve třídě centra, můžete předat ID aktuálního připojení a použít s `Clients.Client`, `Clients.AllExcept`, nebo `Clients.Group` pro simulaci `Clients.Caller`, `Clients.Others`, nebo `Clients.OthersInGroup`.</span><span class="sxs-lookup"><span data-stu-id="a23e9-405">If you are calling into your non-Hub class from methods in your Hub class, you can pass in the current connection ID and use that with `Clients.Client`, `Clients.AllExcept`, or `Clients.Group` to simulate `Clients.Caller`, `Clients.Others`, or `Clients.OthersInGroup`.</span></span> <span data-ttu-id="a23e9-406">V následujícím příkladu `MoveShapeHub` třídy předá ID připojení pro `Broadcaster` třídy tak, aby `Broadcaster` třídy můžete simulovat `Clients.Others`.</span><span class="sxs-lookup"><span data-stu-id="a23e9-406">In the following example, the `MoveShapeHub` class passes the connection ID to the `Broadcaster` class so that the `Broadcaster` class can simulate `Clients.Others`.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample64.cs?highlight=12,36)]

<a id="managinggroupsoutsidehub"></a>

### <a name="managing-group-membership"></a><span data-ttu-id="a23e9-407">Správa členství ve skupině</span><span class="sxs-lookup"><span data-stu-id="a23e9-407">Managing group membership</span></span>

<span data-ttu-id="a23e9-408">Pro správu skupin máte stejné možnosti jako třída rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a23e9-408">For managing groups you have the same options as you do in a Hub class.</span></span>

- <span data-ttu-id="a23e9-409">Přidání klienta do skupiny</span><span class="sxs-lookup"><span data-stu-id="a23e9-409">Add a client to a group</span></span>

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample65.cs)]
- <span data-ttu-id="a23e9-410">Odeberte klienta ze skupiny</span><span class="sxs-lookup"><span data-stu-id="a23e9-410">Remove a client from a group</span></span>

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample66.css)]

<a id="hubpipeline"></a>

## <a name="how-to-customize-the-hubs-pipeline"></a><span data-ttu-id="a23e9-411">Přizpůsobení kanálu rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a23e9-411">How to customize the Hubs pipeline</span></span>

<span data-ttu-id="a23e9-412">Funkce SignalR umožňuje vložit vlastní kód do kanálu rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a23e9-412">SignalR enables you to inject your own code into the Hub pipeline.</span></span> <span data-ttu-id="a23e9-413">Následující příklad ukazuje vlastní modul kanálu rozbočovače, který zaznamenává každou příchozí volání metody přijatých z klienta a odchozích volání metody vyvolat na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="a23e9-413">The following example shows a custom Hub pipeline module that logs each incoming method call received from the client and outgoing method call invoked on the client:</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample67.cs)]

<span data-ttu-id="a23e9-414">Následující kód na *Global.asax* souboru registruje modul pro spuštění v kanálu rozbočovače:</span><span class="sxs-lookup"><span data-stu-id="a23e9-414">The following code in the *Global.asax* file registers the module to run in the Hub pipeline:</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample68.cs?highlight=3)]

<span data-ttu-id="a23e9-415">Existuje celá řada různých metod, které můžete přepsat.</span><span class="sxs-lookup"><span data-stu-id="a23e9-415">There are many different methods that you can override.</span></span> <span data-ttu-id="a23e9-416">Úplný seznam najdete v tématu [HubPipelineModule metody](https://msdn.microsoft.com/library/jj918633(v=vs.111).aspx).</span><span class="sxs-lookup"><span data-stu-id="a23e9-416">For a complete list, see [HubPipelineModule Methods](https://msdn.microsoft.com/library/jj918633(v=vs.111).aspx).</span></span>
