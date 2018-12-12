---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
title: Funkce SignalR technologie ASP.NET pokyny k rozhraní API Center – javascriptový klient | Dokumentace Microsoftu
author: pfletcher
description: Tento dokument obsahuje úvod k používání rozhraní API rozbočovače pro funkci SignalR verze 2 v klientů JavaScript, například s prohlížeči a Windows Store (WinJS) applicat...
ms.author: riande
ms.date: 09/28/2015
ms.assetid: a9fd4dc0-1b96-4443-82ca-932a5b4a8ea4
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
msc.type: authoredcontent
ms.openlocfilehash: 8e493eda256351904da49e1222773f188e6a2058
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53288063"
---
<a name="aspnet-signalr-hubs-api-guide---javascript-client"></a><span data-ttu-id="a9154-103">Funkce SignalR technologie ASP.NET pokyny k rozhraní API Center – javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="a9154-103">ASP.NET SignalR Hubs API Guide - JavaScript Client</span></span>
====================
<span data-ttu-id="a9154-104">podle [Patrick Fletcher](https://github.com/pfletcher), [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="a9154-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="a9154-105">Tento dokument obsahuje úvod k používání rozhraní API rozbočovače pro funkci SignalR verze 2 v klientů JavaScript, například s prohlížeči a aplikace Windows Store (WinJS).</span><span class="sxs-lookup"><span data-stu-id="a9154-105">This document provides an introduction to using the Hubs API for SignalR version 2 in JavaScript clients, such as browsers and Windows Store (WinJS) applications.</span></span>
>
> <span data-ttu-id="a9154-106">Rozhraní API pro rozbočovače SignalR umožňuje vytvářet vzdálených volání procedur (RPC) ze serveru pro připojené klienty a z klientů k serveru.</span><span class="sxs-lookup"><span data-stu-id="a9154-106">The SignalR Hubs API enables you to make remote procedure calls (RPCs) from a server to connected clients and from clients to the server.</span></span> <span data-ttu-id="a9154-107">V serverovém kódu můžete definovat metody, které mohou být volány klientů a volat metody, které běží na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a9154-107">In server code, you define methods that can be called by clients, and you call methods that run on the client.</span></span> <span data-ttu-id="a9154-108">V klientském kódu můžete definovat metody, které lze volat ze serveru a volání metody, které běží na serveru.</span><span class="sxs-lookup"><span data-stu-id="a9154-108">In client code, you define methods that can be called from the server, and you call methods that run on the server.</span></span> <span data-ttu-id="a9154-109">Funkce SignalR postará za vás zajistí funkčnost systému klient server.</span><span class="sxs-lookup"><span data-stu-id="a9154-109">SignalR takes care of all of the client-to-server plumbing for you.</span></span>
>
> <span data-ttu-id="a9154-110">Funkce SignalR také nabízí nižší úrovně rozhraní API volá trvalé připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-110">SignalR also offers a lower-level API called Persistent Connections.</span></span> <span data-ttu-id="a9154-111">Úvod do SignalR, rozbočovačů a trvalá připojení, najdete v článku [Úvod ke knihovně SignalR](../getting-started/introduction-to-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="a9154-111">For an introduction to SignalR, Hubs, and Persistent Connections, see [Introduction to SignalR](../getting-started/introduction-to-signalr.md).</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="a9154-112">Verze softwaru použitým v tomto tématu</span><span class="sxs-lookup"><span data-stu-id="a9154-112">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="a9154-113">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="a9154-113">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="a9154-114">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="a9154-114">.NET 4.5</span></span>
> - <span data-ttu-id="a9154-115">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="a9154-115">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="a9154-116">Předchozích verzích tohoto tématu</span><span class="sxs-lookup"><span data-stu-id="a9154-116">Previous versions of this topic</span></span>
>
> <span data-ttu-id="a9154-117">Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="a9154-117">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="a9154-118">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="a9154-118">Questions and comments</span></span>
>
> <span data-ttu-id="a9154-119">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="a9154-119">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="a9154-120">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="a9154-120">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="overview"></a><span data-ttu-id="a9154-121">Přehled</span><span class="sxs-lookup"><span data-stu-id="a9154-121">Overview</span></span>

<span data-ttu-id="a9154-122">Tento dokument obsahuje následující části:</span><span class="sxs-lookup"><span data-stu-id="a9154-122">This document contains the following sections:</span></span>

- [<span data-ttu-id="a9154-123">Vygenerovaný proxy server a co to dělá za vás</span><span class="sxs-lookup"><span data-stu-id="a9154-123">The generated proxy and what it does for you</span></span>](#genproxy)

    - [<span data-ttu-id="a9154-124">Kdy použít vygenerovaný proxy server</span><span class="sxs-lookup"><span data-stu-id="a9154-124">When to use the generated proxy</span></span>](#cantusegenproxy)
- [<span data-ttu-id="a9154-125">Instalace klienta</span><span class="sxs-lookup"><span data-stu-id="a9154-125">Client Setup</span></span>](#clientsetup)

    - [<span data-ttu-id="a9154-126">Způsob vytvoření odkazu dynamicky generované proxy</span><span class="sxs-lookup"><span data-stu-id="a9154-126">How to reference the dynamically generated proxy</span></span>](#dynamicproxy)
    - [<span data-ttu-id="a9154-127">Vytváření fyzického souboru pro funkci SignalR generované proxy</span><span class="sxs-lookup"><span data-stu-id="a9154-127">How to create a physical file for the SignalR generated proxy</span></span>](#manualproxy)
- [<span data-ttu-id="a9154-128">Postup vytvoření připojení</span><span class="sxs-lookup"><span data-stu-id="a9154-128">How to establish a connection</span></span>](#establishconnection)

    - [<span data-ttu-id="a9154-129">$. connection.hub je stejný objekt, vytvoří tento $.hubConnection()</span><span class="sxs-lookup"><span data-stu-id="a9154-129">$.connection.hub is the same object that $.hubConnection() creates</span></span>](#connequivalence)
    - [<span data-ttu-id="a9154-130">Asynchronní provádění tohoto start – metoda</span><span class="sxs-lookup"><span data-stu-id="a9154-130">Asynchronous execution of the start method</span></span>](#asyncstart)
- [<span data-ttu-id="a9154-131">Jak vytvořit připojení mezi doménami</span><span class="sxs-lookup"><span data-stu-id="a9154-131">How to establish a cross-domain connection</span></span>](#crossdomain)
- [<span data-ttu-id="a9154-132">Postup konfigurace připojení</span><span class="sxs-lookup"><span data-stu-id="a9154-132">How to configure the connection</span></span>](#configureconnection)

    - [<span data-ttu-id="a9154-133">Určení parametrů řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="a9154-133">How to specify query string parameters</span></span>](#querystring)
    - [<span data-ttu-id="a9154-134">Jak určit metodu přenosu</span><span class="sxs-lookup"><span data-stu-id="a9154-134">How to specify the transport method</span></span>](#transport)
- [<span data-ttu-id="a9154-135">Získání proxy serveru pro rozbočovač třídy</span><span class="sxs-lookup"><span data-stu-id="a9154-135">How to get a proxy for a Hub class</span></span>](#getproxy)
- [<span data-ttu-id="a9154-136">Definování metody na straně klienta, která může volat na serveru</span><span class="sxs-lookup"><span data-stu-id="a9154-136">How to define methods on the client that the server can call</span></span>](#callclient)
- [<span data-ttu-id="a9154-137">Volání metody serveru z klienta</span><span class="sxs-lookup"><span data-stu-id="a9154-137">How to call server methods from the client</span></span>](#callserver)
- [<span data-ttu-id="a9154-138">Zpracování událostí doby platnosti</span><span class="sxs-lookup"><span data-stu-id="a9154-138">How to handle connection lifetime events</span></span>](#connectionlifetime)
- [<span data-ttu-id="a9154-139">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="a9154-139">How to handle errors</span></span>](#handleerrors)
- [<span data-ttu-id="a9154-140">Jak povolit protokolování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="a9154-140">How to enable client-side logging</span></span>](#logging)

<span data-ttu-id="a9154-141">Dokumentace o tom, jak program na serveru nebo klientů .NET, naleznete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="a9154-141">For documentation on how to program the server or .NET clients, see the following resources:</span></span>

- [<span data-ttu-id="a9154-142">Pokyny k rozhraní API Center SignalR – Server</span><span class="sxs-lookup"><span data-stu-id="a9154-142">SignalR Hubs API Guide - Server</span></span>](hubs-api-guide-server.md)
- [<span data-ttu-id="a9154-143">Pokyny k rozhraní API Center SignalR – klient .NET</span><span class="sxs-lookup"><span data-stu-id="a9154-143">SignalR Hubs API Guide - .NET Client</span></span>](hubs-api-guide-net-client.md)

<span data-ttu-id="a9154-144">Součást serveru SignalR 2 je dostupná pouze na rozhraní .NET 4.5 (i když je klient .NET pro funkci SignalR 2 v rozhraní .NET 4.0).</span><span class="sxs-lookup"><span data-stu-id="a9154-144">The SignalR 2 server component is only available on .NET 4.5 (though there is a .NET client for SignalR 2 on .NET 4.0).</span></span>

<a id="genproxy"></a>

## <a name="the-generated-proxy-and-what-it-does-for-you"></a><span data-ttu-id="a9154-145">Vygenerovaný proxy server a co to dělá za vás</span><span class="sxs-lookup"><span data-stu-id="a9154-145">The generated proxy and what it does for you</span></span>

<span data-ttu-id="a9154-146">Můžete naprogramovat javascriptový klient komunikovat se službou SignalR s nebo bez proxy serveru, který generuje SignalR za vás.</span><span class="sxs-lookup"><span data-stu-id="a9154-146">You can program a JavaScript client to communicate with a SignalR service with or without a proxy that SignalR generates for you.</span></span> <span data-ttu-id="a9154-147">Proxy vykonává pro vás je zjednodušení syntaxe kódu slouží k připojení metod zápisu, která volá na server, a volání metod na serveru.</span><span class="sxs-lookup"><span data-stu-id="a9154-147">What the proxy does for you is simplify the syntax of the code you use to connect, write methods that the server calls, and call methods on the server.</span></span>

<span data-ttu-id="a9154-148">Při psaní kódu pro volání metody serveru vygenerovaný proxy server vám umožní použít syntaxi, která vypadá jako by byly provádění lokální funkce: můžete napsat `serverMethod(arg1, arg2)` místo `invoke('serverMethod', arg1, arg2)`.</span><span class="sxs-lookup"><span data-stu-id="a9154-148">When you write code to call server methods, the generated proxy enables you to use syntax that looks as though you were executing a local function: you can write `serverMethod(arg1, arg2)` instead of `invoke('serverMethod', arg1, arg2)`.</span></span> <span data-ttu-id="a9154-149">Syntaxe vygenerovaný proxy server také umožňuje okamžité a srozumitelné chybě na straně klienta, pokud napíšete metodu název serveru.</span><span class="sxs-lookup"><span data-stu-id="a9154-149">The generated proxy syntax also enables an immediate and intelligible client-side error if you mistype a server method name.</span></span> <span data-ttu-id="a9154-150">A pokud ručně vytvořit soubor, který definuje náhledy, můžete také získat podporu technologie IntelliSense pro psaní kódu, který volá metody serveru.</span><span class="sxs-lookup"><span data-stu-id="a9154-150">And if you manually create the file that defines the proxies, you can also get IntelliSense support for writing code that calls server methods.</span></span>

<span data-ttu-id="a9154-151">Předpokládejme například, že máte následující třídy rozbočovače na serveru:</span><span class="sxs-lookup"><span data-stu-id="a9154-151">For example, suppose you have the following Hub class on the server:</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample1.cs?highlight=1,3,5)]

<span data-ttu-id="a9154-152">Následující příklady kódu ukazují, co vypadá kódu JavaScript jako pro volání `NewContosoChatMessage` metodu na serveru a přijímají volání `addContosoChatMessageToPage` metoda ze serveru.</span><span class="sxs-lookup"><span data-stu-id="a9154-152">The following code examples show what JavaScript code looks like for invoking the `NewContosoChatMessage` method on the server and receiving invocations of the `addContosoChatMessageToPage` method from the server.</span></span>

<span data-ttu-id="a9154-153">**Vygenerovaný proxy**</span><span class="sxs-lookup"><span data-stu-id="a9154-153">**With the generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample2.js?highlight=1-2,8)]

<span data-ttu-id="a9154-154">**Bez vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="a9154-154">**Without the generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample3.js?highlight=2-3,9)]

<a id="cantusegenproxy"></a>

### <a name="when-to-use-the-generated-proxy"></a><span data-ttu-id="a9154-155">Kdy použít vygenerovaný proxy server</span><span class="sxs-lookup"><span data-stu-id="a9154-155">When to use the generated proxy</span></span>

<span data-ttu-id="a9154-156">Pokud chcete registrovat více obslužných rutin události pro metody, která volá na server, nemůžete použít vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="a9154-156">If you want to register multiple event handlers for a client method that the server calls, you can't use the generated proxy.</span></span> <span data-ttu-id="a9154-157">V opačném případě můžete použít vygenerovaný proxy server nebo není založen na předvolbu kódování.</span><span class="sxs-lookup"><span data-stu-id="a9154-157">Otherwise, you can choose to use the generated proxy or not based on your coding preference.</span></span> <span data-ttu-id="a9154-158">Pokud se rozhodnete nepoužít, není nutné odkazovat na adresu URL "Center/signalr" v `script` elementu v kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="a9154-158">If you choose not to use it, you don't have to reference the "signalr/hubs" URL in a `script` element in your client code.</span></span>

<a id="clientsetup"></a>

## <a name="client-setup"></a><span data-ttu-id="a9154-159">Instalace klienta</span><span class="sxs-lookup"><span data-stu-id="a9154-159">Client setup</span></span>

<span data-ttu-id="a9154-160">Klient JavaScriptu vyžaduje odkazy na knihovny jQuery a soubor JavaScript základní funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="a9154-160">A JavaScript client requires references to jQuery and the SignalR core JavaScript file.</span></span> <span data-ttu-id="a9154-161">1.6.4 nebo hlavní novější verze, jako je například 1.7.2, 1.8.2 nebo 1.9.1 musí být verze jQuery.</span><span class="sxs-lookup"><span data-stu-id="a9154-161">The jQuery version must be 1.6.4 or major later versions, such as 1.7.2, 1.8.2, or 1.9.1.</span></span> <span data-ttu-id="a9154-162">Pokud se rozhodnete použít vygenerovaný proxy server, potřebujete také odkaz k proxy serveru SignalR vygeneruje soubor jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a9154-162">If you decide to use the generated proxy, you also need a reference to the SignalR generated proxy JavaScript file.</span></span> <span data-ttu-id="a9154-163">Následující příklad ukazuje, jak může odkazy vypadat na stránku HTML, který používá vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="a9154-163">The following example shows what the references might look like in an HTML page that uses the generated proxy.</span></span>

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample4.html)]

<span data-ttu-id="a9154-164">Tyto odkazy musí být zahrnuty v tomto pořadí: jQuery nejprve naposledy SignalR jádra po tomto a proxy servery SignalR.</span><span class="sxs-lookup"><span data-stu-id="a9154-164">These references must be included in this order: jQuery first, SignalR core after that, and SignalR proxies last.</span></span>

<a id="dynamicproxy"></a>

### <a name="how-to-reference-the-dynamically-generated-proxy"></a><span data-ttu-id="a9154-165">Způsob vytvoření odkazu dynamicky generované proxy</span><span class="sxs-lookup"><span data-stu-id="a9154-165">How to reference the dynamically generated proxy</span></span>

<span data-ttu-id="a9154-166">V předchozím příkladu je odkaz na proxy serveru SignalR vygeneruje dynamicky generovaném kódu jazyka JavaScript, není k fyzickému souboru.</span><span class="sxs-lookup"><span data-stu-id="a9154-166">In the preceding example, the reference to the SignalR generated proxy is to dynamically generated JavaScript code, not to a physical file.</span></span> <span data-ttu-id="a9154-167">Funkce SignalR vytvoří kód jazyka JavaScript pro proxy server v reálném čase a slouží ke klientovi jako odpověď na adresu "/ signalr/centra".</span><span class="sxs-lookup"><span data-stu-id="a9154-167">SignalR creates the JavaScript code for the proxy on the fly and serves it to the client in response to the "/signalr/hubs" URL.</span></span> <span data-ttu-id="a9154-168">Pokud jste zadali jiné základní adresu URL pro připojení SignalR na serveru ve vaší `MapSignalR` metoda, adresu URL k souboru dynamicky generované proxy je vaše vlastní adresu URL s "/ hubs" připojí k němu.</span><span class="sxs-lookup"><span data-stu-id="a9154-168">If you specified a different base URL for SignalR connections on the server in your `MapSignalR` method, the URL for the dynamically generated proxy file is your custom URL with "/hubs" appended to it.</span></span>

> [!NOTE]
> <span data-ttu-id="a9154-169">Pro klienty systému Windows 8 (Windows Store) jazyka JavaScript použijte soubor fyzické proxy místo dynamicky vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="a9154-169">For Windows 8 (Windows Store) JavaScript clients, use the physical proxy file instead of the dynamically generated one.</span></span> <span data-ttu-id="a9154-170">Další informace najdete v tématu [vytváření fyzického souboru pro funkci SignalR generované proxy](#manualproxy) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a9154-170">For more information, see [How to create a physical file for the SignalR generated proxy](#manualproxy) later in this topic.</span></span>


<span data-ttu-id="a9154-171">V ASP.NET MVC 4 a 5 zobrazení Razor pomocí tilda k odkazování na kořenový adresář aplikace v odkazu na soubor vašeho proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="a9154-171">In an ASP.NET MVC 4 or 5 Razor view, use the tilde to refer to the application root in your proxy file reference:</span></span>

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample5.html)]

<span data-ttu-id="a9154-172">Další informace o použití aplikace SignalR v MVC 5 v tématu [Začínáme s knihovnou SignalR a MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).</span><span class="sxs-lookup"><span data-stu-id="a9154-172">For more information about using SignalR in MVC 5, see [Getting Started with SignalR and MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).</span></span>

<span data-ttu-id="a9154-173">V zobrazení syntaxe Razor rozhraní ASP.NET MVC 3, použijte `Url.Content` pro odkaz na soubor vašeho proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="a9154-173">In an ASP.NET MVC 3 Razor view, use `Url.Content` for your proxy file reference:</span></span>

[!code-cshtml[Main](hubs-api-guide-javascript-client/samples/sample6.cshtml)]

<span data-ttu-id="a9154-174">V aplikaci webových formulářů ASP.NET pomocí `ResolveClientUrl` pro vaše proxy odkaz na soubor nebo ho zaregistrovat pomocí prvku ScriptManager pomocí cesty relativní kořenové aplikace (začíná tildou):</span><span class="sxs-lookup"><span data-stu-id="a9154-174">In an ASP.NET Web Forms application, use `ResolveClientUrl` for your proxies file reference or register it via the ScriptManager using an app root relative path (starting with a tilde):</span></span>

[!code-aspx[Main](hubs-api-guide-javascript-client/samples/sample7.aspx)]

<span data-ttu-id="a9154-175">Obecně platí použijte stejnou metodu pro zadávání adresy URL "/ signalr/centra", který používáte pro soubory CSS a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a9154-175">As a general rule, use the same method for specifying the "/signalr/hubs" URL that you use for CSS or JavaScript files.</span></span> <span data-ttu-id="a9154-176">Pokud zadáte adresu URL bez použití vlnovkou, v některých scénářích aplikace bude fungovat správně při testování v sadě Visual Studio pomocí služby IIS Express, ale selže s chyby 404 při nasazování do úplnou službu IIS.</span><span class="sxs-lookup"><span data-stu-id="a9154-176">If you specify a URL without using a tilde, in some scenarios your application will work correctly when you test in Visual Studio using IIS Express but will fail with a 404 error when you deploy to full IIS.</span></span> <span data-ttu-id="a9154-177">Další informace najdete v tématu **řešení odkazy na prostředky na kořenové úrovni** v [webové servery v sadě Visual Studio pro webové projekty ASP.NET](https://msdn.microsoft.com/library/58wxa9w5.aspx) na webu MSDN.</span><span class="sxs-lookup"><span data-stu-id="a9154-177">For more information, see **Resolving References to Root-Level Resources** in [Web Servers in Visual Studio for ASP.NET Web Projects](https://msdn.microsoft.com/library/58wxa9w5.aspx) on the MSDN site.</span></span>

<span data-ttu-id="a9154-178">Při spuštění webový projekt v sadě Visual Studio 2013 v režimu ladění, a pokud používáte Internet Explorer jako prohlížeč, zobrazí se soubor proxy v **Průzkumníka řešení** pod **dokumenty skriptu**, jak je znázorněno Následující obrázek.</span><span class="sxs-lookup"><span data-stu-id="a9154-178">When you run a web project in Visual Studio 2013 in debug mode, and if you use Internet Explorer as your browser, you can see the proxy file in **Solution Explorer** under **Script Documents**, as shown in the following illustration.</span></span>

![Vygenerovaný proxy soubor jazyka JavaScript v Průzkumníku řešení](hubs-api-guide-javascript-client/_static/image1.png)

<span data-ttu-id="a9154-180">Pokud chcete zobrazit obsah souboru, klikněte dvakrát na **rozbočovače**.</span><span class="sxs-lookup"><span data-stu-id="a9154-180">To see the contents of the file, double-click **hubs**.</span></span> <span data-ttu-id="a9154-181">Pokud nepoužíváte Visual Studio 2012 nebo 2013 a aplikace Internet Explorer, nebo pokud nejste v režimu ladění, můžete také získat obsah souboru tak, že přejdete na adresu "/ signalR/centra".</span><span class="sxs-lookup"><span data-stu-id="a9154-181">If you are not using Visual Studio 2012 or 2013 and Internet Explorer, or if you are not in debug mode, you can also get the contents of the file by browsing to the "/signalR/hubs" URL.</span></span> <span data-ttu-id="a9154-182">Například, pokud vaše lokalita běží v `http://localhost:56699`, přejděte na stránku `http://localhost:56699/SignalR/hubs` v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a9154-182">For example, if your site is running at `http://localhost:56699`, go to `http://localhost:56699/SignalR/hubs` in your browser.</span></span>

<a id="manualproxy"></a>

### <a name="how-to-create-a-physical-file-for-the-signalr-generated-proxy"></a><span data-ttu-id="a9154-183">Vytváření fyzického souboru pro funkci SignalR generované proxy</span><span class="sxs-lookup"><span data-stu-id="a9154-183">How to create a physical file for the SignalR generated proxy</span></span>

<span data-ttu-id="a9154-184">Jako alternativu k dynamicky generované proxy můžete vytvořit fyzický soubor, který má kód proxy serveru a jako reference.</span><span class="sxs-lookup"><span data-stu-id="a9154-184">As an alternative to the dynamically generated proxy, you can create a physical file that has the proxy code and reference that file.</span></span> <span data-ttu-id="a9154-185">Můžete to udělat pro kontrolu nad ukládání do mezipaměti nebo sdružování chování nebo aby technologie IntelliSense při psaní kódu volání metody serveru.</span><span class="sxs-lookup"><span data-stu-id="a9154-185">You might want to do that for control over caching or bundling behavior, or to get IntelliSense when you are coding calls to server methods.</span></span>

<span data-ttu-id="a9154-186">Vytvoření souboru proxy serveru, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="a9154-186">To create a proxy file, perform the following steps:</span></span>

1. <span data-ttu-id="a9154-187">Nainstalujte [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="a9154-187">Install the [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) NuGet package.</span></span>
2. <span data-ttu-id="a9154-188">Otevřete příkazový řádek a přejděte *nástroje* složku obsahující soubor SignalR.exe.</span><span class="sxs-lookup"><span data-stu-id="a9154-188">Open a command prompt and browse to the *tools* folder that contains the SignalR.exe file.</span></span> <span data-ttu-id="a9154-189">Nástroje pro složku je v následujícím umístění:</span><span class="sxs-lookup"><span data-stu-id="a9154-189">The tools folder is at the following location:</span></span>

    `[your solution folder]\packages\Microsoft.AspNet.SignalR.Utils.2.1.0\tools`
3. <span data-ttu-id="a9154-190">Zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a9154-190">Enter the following command:</span></span>

    `signalr ghp /path:[path to the .dll that contains your Hub class]`

    <span data-ttu-id="a9154-191">Cesta k vaší *.dll* je obvykle *bin* ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="a9154-191">The path to your *.dll* is typically the *bin* folder in your project folder.</span></span>

    <span data-ttu-id="a9154-192">Tento příkaz vytvoří soubor s názvem *server.js* ve stejné složce jako *signalr.exe*.</span><span class="sxs-lookup"><span data-stu-id="a9154-192">This command creates a file named *server.js* in the same folder as *signalr.exe*.</span></span>
4. <span data-ttu-id="a9154-193">Vložit *server.js* souborů v příslušné složce ve vašem projektu, přejmenujte ho podle potřeby pro vaši aplikaci a přidejte odkaz na jeho místo odkazu "Center/signalr".</span><span class="sxs-lookup"><span data-stu-id="a9154-193">Put the *server.js* file in an appropriate folder in your project, rename it as appropriate for your application, and add a reference to it in place of the "signalr/hubs" reference.</span></span>

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a><span data-ttu-id="a9154-194">Postup vytvoření připojení</span><span class="sxs-lookup"><span data-stu-id="a9154-194">How to establish a connection</span></span>

<span data-ttu-id="a9154-195">Aby mohla navázat připojení, budete muset vytvořit objekt připojení, proxy server a zaregistrujte obslužné rutiny událostí pro metody, které lze volat ze serveru.</span><span class="sxs-lookup"><span data-stu-id="a9154-195">Before you can establish a connection, you have to create a connection object, create a proxy, and register event handlers for methods that can be called from the server.</span></span> <span data-ttu-id="a9154-196">Po nastavení serveru proxy a událost obslužné rutiny připojení voláním `start` metody.</span><span class="sxs-lookup"><span data-stu-id="a9154-196">When the proxy and event handlers are set up, establish the connection by calling the `start` method.</span></span>

<span data-ttu-id="a9154-197">Pokud používáte vygenerovaný proxy server, není nutné vytvořit objekt připojení ve svém vlastním kódu, protože vygenerovaném kódu proxy to udělá za vás.</span><span class="sxs-lookup"><span data-stu-id="a9154-197">If you are using the generated proxy, you don't have to create the connection object in your own code because the generated proxy code does it for you.</span></span>

<a id="nogenconnection"></a>

<span data-ttu-id="a9154-198">**Naváže připojení (vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-198">**Establish a connection (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample8.js?highlight=5)]

<span data-ttu-id="a9154-199">**Navázání připojení (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-199">**Establish a connection (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample9.js?highlight=1,6)]

<span data-ttu-id="a9154-200">Vzorový kód používá výchozí "/ signalr" adresa URL k připojení do služby SignalR.</span><span class="sxs-lookup"><span data-stu-id="a9154-200">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="a9154-201">Informace o tom, jak určit různé základní adresu URL najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - /signalr URL](hubs-api-guide-server.md#signalrurl).</span><span class="sxs-lookup"><span data-stu-id="a9154-201">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).</span></span>

<span data-ttu-id="a9154-202">Ve výchozím umístění centra je aktuální server; Pokud se připojujete k jinému serveru, zadejte adresu URL před voláním `start` způsob, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a9154-202">By default, the hub location is the current server; if you are connecting to a different server, specify the URL before calling the `start` method, as shown in the following example:</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample10.js)]

> [!NOTE]
> <span data-ttu-id="a9154-203">Obvykle registraci obslužné rutiny událostí před voláním `start` metoda k navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-203">Normally you register event handlers before calling the `start` method to establish the connection.</span></span> <span data-ttu-id="a9154-204">Pokud chcete zaregistrovat několik obslužných rutin událostí po navázání připojení, můžete to udělat, ale je nutné zaregistrovat alespoň jeden z vašich handler(s) událostí před voláním `start` metody.</span><span class="sxs-lookup"><span data-stu-id="a9154-204">If you want to register some event handlers after establishing the connection, you can do that, but you must register at least one of your event handler(s) before calling the `start` method.</span></span> <span data-ttu-id="a9154-205">Jedním z důvodů je, že v aplikaci může být mnoho rozbočovače, ale není vhodné pro aktivaci `OnConnected` událost pro každý rozbočovač, chcete-li pouze pomocí jedné z nich.</span><span class="sxs-lookup"><span data-stu-id="a9154-205">One reason for this is that there can be many Hubs in an application, but you wouldn't want to trigger the `OnConnected` event on every Hub if you are only going to use to one of them.</span></span> <span data-ttu-id="a9154-206">Po vytvoření připojení je existenci metody na proxy server rozbočovače pro co říká SignalR k aktivaci `OnConnected` událostí.</span><span class="sxs-lookup"><span data-stu-id="a9154-206">When the connection is established, the presence of a client method on a Hub's proxy is what tells SignalR to trigger the `OnConnected` event.</span></span> <span data-ttu-id="a9154-207">Pokud nezaregistrujete všechny obslužné rutiny událostí před voláním `start` metody, bude možné volat metody v rozbočovači, ale centra `OnConnected` nebude volána metoda a žádné metody klienta, který bude vyvolán ze serveru.</span><span class="sxs-lookup"><span data-stu-id="a9154-207">If you don't register any event handlers before calling the `start` method, you will be able to invoke methods on the Hub, but the Hub's `OnConnected` method won't be called and no client methods will be invoked from the server.</span></span>


<a id="connequivalence"></a>

### <a name="connectionhub-is-the-same-object-that-hubconnection-creates"></a><span data-ttu-id="a9154-208">$. connection.hub je stejný objekt, vytvoří tento $.hubConnection()</span><span class="sxs-lookup"><span data-stu-id="a9154-208">$.connection.hub is the same object that $.hubConnection() creates</span></span>

<span data-ttu-id="a9154-209">Jak je vidět z příkladů, když použijete vygenerovaný proxy server, `$.connection.hub` odkazuje na objekt připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-209">As you can see from the examples, when you use the generated proxy, `$.connection.hub` refers to the connection object.</span></span> <span data-ttu-id="a9154-210">Toto je stejný objekt, který můžete získat voláním `$.hubConnection()` když nepoužíváte vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="a9154-210">This is the same object that you get by calling `$.hubConnection()` when you aren't using the generated proxy.</span></span> <span data-ttu-id="a9154-211">Vygenerovaný proxy kód vytvoří spojení za vás spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="a9154-211">The generated proxy code creates the connection for you by executing the following statement:</span></span>

![Vytvoření připojení v souboru vygenerovaný proxy server](hubs-api-guide-javascript-client/_static/image3.png)

<span data-ttu-id="a9154-213">Pokud používáte vygenerovaný proxy server, můžete provést cokoli, co se `$.connection.hub` , když nepoužíváte vygenerovaný proxy server, co lze použít s objektem připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-213">When you're using the generated proxy, you can do anything with `$.connection.hub` that you can do with a connection object when you're not using the generated proxy.</span></span>

<a id="asyncstart"></a>

### <a name="asynchronous-execution-of-the-start-method"></a><span data-ttu-id="a9154-214">Asynchronní provádění tohoto start – metoda</span><span class="sxs-lookup"><span data-stu-id="a9154-214">Asynchronous execution of the start method</span></span>

<span data-ttu-id="a9154-215">`start` Metoda provedena asynchronně.</span><span class="sxs-lookup"><span data-stu-id="a9154-215">The `start` method executes asynchronously.</span></span> <span data-ttu-id="a9154-216">Vrátí [jQuery odloženo objekt](http://api.jquery.com/category/deferred-object/), což znamená, že můžete přidat funkce zpětného volání voláním metod `pipe`, `done`, a `fail`.</span><span class="sxs-lookup"><span data-stu-id="a9154-216">It returns a [jQuery Deferred object](http://api.jquery.com/category/deferred-object/), which means that you can add callback functions by calling methods such as `pipe`, `done`, and `fail`.</span></span> <span data-ttu-id="a9154-217">Pokud máte kód, který chcete spustit po připojení se naváže, jako je například volání metody serveru, vložte tento kód ve funkci zpětného volání nebo jeho volání z funkce zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="a9154-217">If you have code that you want to execute after the connection is established, such as a call to a server method, put that code in a callback function or call it from a callback function.</span></span> <span data-ttu-id="a9154-218">`.done` Metoda zpětného volání se spustí po vytvoření připojení a po žádný kód, který máte vaše `OnConnected` metoda obslužné rutiny události na server dokončí provádění.</span><span class="sxs-lookup"><span data-stu-id="a9154-218">The `.done` callback method is executed after the connection has been established, and after any code that you have in your `OnConnected` event handler method on the server finishes executing.</span></span>

<span data-ttu-id="a9154-219">Když vložíte příkaz "Teď připojení" z předchozího příkladu jako další řádek kódu po `start` volání metody (ne v `.done` zpětného volání), `console.log` řádek se spustí předtím, než se naváže připojení, jak je znázorněno v následujícím Příklad:</span><span class="sxs-lookup"><span data-stu-id="a9154-219">If you put the "Now connected" statement from the preceding example as the next line of code after the `start` method call (not in a `.done` callback), the `console.log` line will execute before the connection is established, as shown in the following example:</span></span>

![Ukázka nesprávného způsobu psaní kódu, která se spustí po vytvoření připojení](hubs-api-guide-javascript-client/_static/image5.png)

<a id="crossdomain"></a>

## <a name="how-to-establish-a-cross-domain-connection"></a><span data-ttu-id="a9154-221">Jak vytvořit připojení mezi doménami</span><span class="sxs-lookup"><span data-stu-id="a9154-221">How to establish a cross-domain connection</span></span>

<span data-ttu-id="a9154-222">Obvykle Pokud prohlížeč načte stránku z `http://contoso.com`, připojení SignalR je ve stejné doméně, v `http://contoso.com/signalr`.</span><span class="sxs-lookup"><span data-stu-id="a9154-222">Typically if the browser loads a page from `http://contoso.com`, the SignalR connection is in the same domain, at `http://contoso.com/signalr`.</span></span> <span data-ttu-id="a9154-223">Pokud stránku z `http://contoso.com` vytvoří připojení k `http://fabrikam.com/signalr`, to znamená připojení mezi doménami.</span><span class="sxs-lookup"><span data-stu-id="a9154-223">If the page from `http://contoso.com` makes a connection to `http://fabrikam.com/signalr`, that is a cross-domain connection.</span></span> <span data-ttu-id="a9154-224">Z bezpečnostních důvodů jsou ve výchozím nastavení zakázané připojení mezi doménami.</span><span class="sxs-lookup"><span data-stu-id="a9154-224">For security reasons, cross-domain connections are disabled by default.</span></span>

<span data-ttu-id="a9154-225">V knihovně SignalR 1.x různé požadavky na doménu se řídí jediného příznaku EnableCrossDomain.</span><span class="sxs-lookup"><span data-stu-id="a9154-225">In SignalR 1.x, cross domain requests were controlled by a single EnableCrossDomain flag.</span></span> <span data-ttu-id="a9154-226">Tento příznak řídí požadavky na JSONP a CORS.</span><span class="sxs-lookup"><span data-stu-id="a9154-226">This flag controlled both JSONP and CORS requests.</span></span> <span data-ttu-id="a9154-227">Pro větší flexibilitu a podporu všech CORS je odebraná ze součásti serveru pro funkci SignalR (JavaScript stále používají klienti CORS obvykle Pokud se zjistí, jestli prohlížeč podporuje), a nové middlewaru OWIN, který byl zpřístupněn pro podporu těchto scénářů.</span><span class="sxs-lookup"><span data-stu-id="a9154-227">For greater flexibility, all CORS support has been removed from the server component of SignalR (JavaScript clients still use CORS normally if it is detected that the browser supports it), and new OWIN middleware has been made available to support these scenarios.</span></span>

<span data-ttu-id="a9154-228">Pokud na straně klienta (pro podporu požadavků mezi doménami ve starších prohlížečích) je vyžadováno JSONP, ji budou muset být explicitně povoluje nastavením `EnableJSONP` na `HubConfiguration` objektu `true`, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="a9154-228">If JSONP is required on the client (to support cross-domain requests in older browsers), it will need to be enabled explicitly by setting `EnableJSONP` on the `HubConfiguration` object to `true`, as shown below.</span></span> <span data-ttu-id="a9154-229">JSONP je standardně zakázaná, protože je to méně bezpečné než CORS.</span><span class="sxs-lookup"><span data-stu-id="a9154-229">JSONP is disabled by default, as it is less secure than CORS.</span></span>

<span data-ttu-id="a9154-230">**Přidání Microsoft.Owin.Cors do projektu:** Pokud chcete nainstalovat tuto knihovnu, spusťte následující příkaz v konzole Správce balíčků:</span><span class="sxs-lookup"><span data-stu-id="a9154-230">**Adding Microsoft.Owin.Cors to your project:** To install this library, run the following command in the Package Manager Console:</span></span>

`Install-Package Microsoft.Owin.Cors`

<span data-ttu-id="a9154-231">Tento příkaz přidá 2.1.0 verzi balíčku do projektu.</span><span class="sxs-lookup"><span data-stu-id="a9154-231">This command will add the 2.1.0 version of the package to your project.</span></span>

### <a name="calling-usecors"></a><span data-ttu-id="a9154-232">Volání UseCors</span><span class="sxs-lookup"><span data-stu-id="a9154-232">Calling UseCors</span></span>

 <span data-ttu-id="a9154-233">Následující fragment kódu ukazuje, jak implementovat mezi doménami připojení v SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="a9154-233">The following code snippet demonstrates how to implement cross-domain connections in SignalR 2.</span></span>

<span data-ttu-id="a9154-234">**Implementace žádosti napříč doménami v SignalR 2**</span><span class="sxs-lookup"><span data-stu-id="a9154-234">**Implementing cross-domain requests in SignalR 2**</span></span>

<span data-ttu-id="a9154-235">Následující kód ukazuje, jak povolit CORS a JSONP v projektu funkcí SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="a9154-235">The following code demonstrates how to enable CORS or JSONP in a SignalR 2 project.</span></span> <span data-ttu-id="a9154-236">Tento vzorový kód používá `Map` a `RunSignalR` místo `MapSignalR`, takže middlewarem CORS běží pouze pro funkci SignalR žádosti, které vyžadují podporu CORS (a nikoli pro veškerý provoz na cestě zadané v `MapSignalR`.) Mapu můžete použít také pro veškerý middleware, který je potřeba spustit pro konkrétní předponu adresy URL, a nikoli pro celou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a9154-236">This code sample uses `Map` and `RunSignalR` instead of `MapSignalR`, so that the CORS middleware runs only for the SignalR requests that require CORS support (rather than for all traffic at the path specified in `MapSignalR`.) Map can also be used for any other middleware that needs to run for a specific URL prefix, rather than for the entire application.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample11.cs)]

> [!NOTE]
>
> - <span data-ttu-id="a9154-237">Nenastavujte `jQuery.support.cors` na hodnotu true v kódu.</span><span class="sxs-lookup"><span data-stu-id="a9154-237">Don't set `jQuery.support.cors` to true in your code.</span></span>
>
>     ![JQuery.support.cors nemají nastavený na hodnotu true](hubs-api-guide-javascript-client/_static/image7.png)
>
>     <span data-ttu-id="a9154-239">Funkce SignalR zpracovává použití CORS.</span><span class="sxs-lookup"><span data-stu-id="a9154-239">SignalR handles the use of CORS.</span></span> <span data-ttu-id="a9154-240">Nastavení `jQuery.support.cors` na hodnotu true zakáže JSONP, protože kvůli němu SignalR předpokládat, že prohlížeč podporuje CORS.</span><span class="sxs-lookup"><span data-stu-id="a9154-240">Setting `jQuery.support.cors` to true disables JSONP because it causes SignalR to assume the browser supports CORS.</span></span>
> - <span data-ttu-id="a9154-241">Když se připojujete k adrese URL místního hostitele, Internet Explorer 10 nebude považují za připojení mezi doménami, tak, že aplikace bude fungovat místně pomocí aplikace Internet Explorer 10 i v případě, že jste ještě nepovolili mezi doménami připojení na serveru.</span><span class="sxs-lookup"><span data-stu-id="a9154-241">When you're connecting to a localhost URL, Internet Explorer 10 won't consider it a cross-domain connection, so the application will work locally with IE 10 even if you haven't enabled cross-domain connections on the server.</span></span>
> - <span data-ttu-id="a9154-242">Informace o použití připojení mezi doménami se aplikace Internet Explorer 9, najdete v tématu [toto vlákno na StackOverflow](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).</span><span class="sxs-lookup"><span data-stu-id="a9154-242">For information about using cross-domain connections with Internet Explorer 9, see [this StackOverflow thread](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).</span></span>
> - <span data-ttu-id="a9154-243">Informace o použití připojení mezi doménami s prohlížečem Chrome naleznete v tématu [toto vlákno na StackOverflow](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).</span><span class="sxs-lookup"><span data-stu-id="a9154-243">For information about using cross-domain connections with Chrome, see [this StackOverflow thread](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).</span></span>
> - <span data-ttu-id="a9154-244">Vzorový kód používá výchozí "/ signalr" adresa URL k připojení do služby SignalR.</span><span class="sxs-lookup"><span data-stu-id="a9154-244">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="a9154-245">Informace o tom, jak určit různé základní adresu URL najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - /signalr URL](hubs-api-guide-server.md#signalrurl).</span><span class="sxs-lookup"><span data-stu-id="a9154-245">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).</span></span>


<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a><span data-ttu-id="a9154-246">Postup konfigurace připojení</span><span class="sxs-lookup"><span data-stu-id="a9154-246">How to configure the connection</span></span>

<span data-ttu-id="a9154-247">Než vytvoříte připojení, můžete určit parametry řetězce dotazu nebo zadat metodu přenosu.</span><span class="sxs-lookup"><span data-stu-id="a9154-247">Before you establish a connection, you can specify query string parameters or specify the transport method.</span></span>

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a><span data-ttu-id="a9154-248">Určení parametrů řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="a9154-248">How to specify query string parameters</span></span>

<span data-ttu-id="a9154-249">Pokud chcete odesílat data do serveru, když se klient připojí, můžete přidat parametry řetězce dotazu do objekt připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-249">If you want to send data to the server when the client connects, you can add query string parameters to the connection object.</span></span> <span data-ttu-id="a9154-250">Následující příklady ukazují, jak nastavit parametr řetězce dotazu v klientském kódu.</span><span class="sxs-lookup"><span data-stu-id="a9154-250">The following examples show how to set a query string parameter in client code.</span></span>

<span data-ttu-id="a9154-251">**Nastavte hodnotu řetězce dotazu před voláním metody spuštění (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-251">**Set a query string value before calling the start method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample12.js?highlight=1)]

<span data-ttu-id="a9154-252">**Nastavte hodnotu řetězce dotazu před voláním metody start (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-252">**Set a query string value before calling the start method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample13.js?highlight=2)]

<span data-ttu-id="a9154-253">Následující příklad znázorňuje způsob čtení parametru řetězce dotazu v serverovém kódu.</span><span class="sxs-lookup"><span data-stu-id="a9154-253">The following example shows how to read a query string parameter in server code.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample14.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a><span data-ttu-id="a9154-254">Jak určit metodu přenosu</span><span class="sxs-lookup"><span data-stu-id="a9154-254">How to specify the transport method</span></span>

<span data-ttu-id="a9154-255">Jako součást procesu připojování klienta SignalR obvykle vyjedná se serverem a určit nejlepší přenos, který je podporovaný server i klient.</span><span class="sxs-lookup"><span data-stu-id="a9154-255">As part of the process of connecting, a SignalR client normally negotiates with the server to determine the best transport that is supported by both server and client.</span></span> <span data-ttu-id="a9154-256">Pokud již víte, jaké přenosu, kterou chcete použít, můžete tento proces vyjednávání obejít tak, že určíte metodu přenosu při volání `start` metody.</span><span class="sxs-lookup"><span data-stu-id="a9154-256">If you already know which transport you want to use, you can bypass this negotiation process by specifying the transport method when you call the `start` method.</span></span>

<span data-ttu-id="a9154-257">**Klientský kód, který určuje metodu přenosu (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-257">**Client code that specifies the transport method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample15.js?highlight=1)]

<span data-ttu-id="a9154-258">**Klientský kód, který určuje metodu přenosu (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-258">**Client code that specifies the transport method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample16.js?highlight=2)]

<span data-ttu-id="a9154-259">Jako alternativu můžete zadat několik metod přenosu v pořadí, ve kterém chcete SignalR chcete vyzkoušet:</span><span class="sxs-lookup"><span data-stu-id="a9154-259">As an alternative, you can specify multiple transport methods in the order in which you want SignalR to try them:</span></span>

<span data-ttu-id="a9154-260">**Klientský kód, který určuje schéma záložního vlastní přenosu (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-260">**Client code that specifies a custom transport fallback scheme (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample17.js?highlight=1)]

<span data-ttu-id="a9154-261">**Klientský kód, který určuje záložní schématu vlastní přenosu (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-261">**Client code that specifies a custom transport fallback scheme (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample18.js?highlight=2)]

<span data-ttu-id="a9154-262">Pro zadání metodu přenosu můžete použít následující hodnoty:</span><span class="sxs-lookup"><span data-stu-id="a9154-262">You can use the following values for specifying the transport method:</span></span>

- <span data-ttu-id="a9154-263">"webSockets"</span><span class="sxs-lookup"><span data-stu-id="a9154-263">"webSockets"</span></span>
- <span data-ttu-id="a9154-264">"foreverFrame"</span><span class="sxs-lookup"><span data-stu-id="a9154-264">"foreverFrame"</span></span>
- <span data-ttu-id="a9154-265">"serverSentEvents"</span><span class="sxs-lookup"><span data-stu-id="a9154-265">"serverSentEvents"</span></span>
- <span data-ttu-id="a9154-266">"longPolling"</span><span class="sxs-lookup"><span data-stu-id="a9154-266">"longPolling"</span></span>

<span data-ttu-id="a9154-267">Následující příklady ukazují, jak zjistit, jakou metodu přenosu používá připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-267">The following examples show how to find out which transport method is being used by a connection.</span></span>

<span data-ttu-id="a9154-268">**Klientský kód, který se zobrazí přenos metodu používanou pro připojení (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-268">**Client code that displays the transport method used by a connection (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample19.js?highlight=2)]

<span data-ttu-id="a9154-269">**Klientský kód, který se zobrazí přenos metodu používanou pro připojení (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-269">**Client code that displays the transport method used by a connection (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample20.js?highlight=3)]

<span data-ttu-id="a9154-270">Informace o tom, jak zkontrolovat metodu přenosu v serverovém kódu najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - jak získat informace o klientovi z kontextové vlastnosti](hubs-api-guide-server.md#contextproperty).</span><span class="sxs-lookup"><span data-stu-id="a9154-270">For information about how to check the transport method in server code, see [ASP.NET SignalR Hubs API Guide - Server - How to get information about the client from the Context property](hubs-api-guide-server.md#contextproperty).</span></span> <span data-ttu-id="a9154-271">Další informace o přenosy a náhrad najdete v tématu [Úvod ke knihovně SignalR – přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="a9154-271">For more information about transports and fallbacks, see [Introduction to SignalR - Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="getproxy"></a>

## <a name="how-to-get-a-proxy-for-a-hub-class"></a><span data-ttu-id="a9154-272">Získání proxy serveru pro rozbočovač třídy</span><span class="sxs-lookup"><span data-stu-id="a9154-272">How to get a proxy for a Hub class</span></span>

<span data-ttu-id="a9154-273">Každý objekt připojení, který vytvoříte zapouzdřuje informace o připojení ke službě SignalR, která obsahuje jednu nebo více tříd rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a9154-273">Each connection object that you create encapsulates information about a connection to a SignalR service that contains one or more Hub classes.</span></span> <span data-ttu-id="a9154-274">Ke komunikaci s třídou rozbočovače, použijte objekt proxy které sami vytvoříte (Pokud nepoužíváte vygenerovaný proxy server) nebo který je generován za vás.</span><span class="sxs-lookup"><span data-stu-id="a9154-274">To communicate with a Hub class, you use a proxy object which you create yourself (if you're not using the generated proxy) or which is generated for you.</span></span>

<span data-ttu-id="a9154-275">Název proxy serveru na straně klienta je verze ve formátu camelCase názvu třídy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a9154-275">On the client the proxy name is a camel-cased version of the Hub class name.</span></span> <span data-ttu-id="a9154-276">Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a9154-276">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="a9154-277">**Třída rozbočovače na serveru**</span><span class="sxs-lookup"><span data-stu-id="a9154-277">**Hub class on server**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample21.cs?highlight=1)]

<span data-ttu-id="a9154-278">**Získání odkazu na proxy serveru generovaného klienta pro rozbočovač**</span><span class="sxs-lookup"><span data-stu-id="a9154-278">**Get a reference to the generated client proxy for the Hub**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample22.js?highlight=1)]

<span data-ttu-id="a9154-279">**Vytvořit proxy klienta pro rozbočovač třídu (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-279">**Create client proxy for the Hub class (without generated proxy)**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample23.cs?highlight=1)]

<span data-ttu-id="a9154-280">Pokud uspořádání vaší třídy centra s `HubName` atribut, použít přesný název bez Změna velikosti písmen.</span><span class="sxs-lookup"><span data-stu-id="a9154-280">If you decorate your Hub class with a `HubName` attribute, use the exact name without changing case.</span></span>

<span data-ttu-id="a9154-281">**Třída rozbočovače na serveru s atributem HubName**</span><span class="sxs-lookup"><span data-stu-id="a9154-281">**Hub class on server with HubName attribute**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample24.cs?highlight=1)]

<span data-ttu-id="a9154-282">**Získání odkazu na proxy serveru generovaného klienta pro rozbočovač**</span><span class="sxs-lookup"><span data-stu-id="a9154-282">**Get a reference to the generated client proxy for the Hub**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample25.js?highlight=1)]

<span data-ttu-id="a9154-283">**Vytvořit proxy klienta pro rozbočovač třídu (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-283">**Create client proxy for the Hub class (without generated proxy)**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample26.cs?highlight=1)]

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a><span data-ttu-id="a9154-284">Definování metody na straně klienta, která může volat na serveru</span><span class="sxs-lookup"><span data-stu-id="a9154-284">How to define methods on the client that the server can call</span></span>

<span data-ttu-id="a9154-285">Chcete-li definovat metodu, která serveru můžete volat z rozbočovač, přidejte obslužnou rutinu události pro proxy server rozbočovače pomocí `client` vlastnost vygenerovaný proxy server nebo volání `on` metody, pokud nepoužíváte vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="a9154-285">To define a method that the server can call from a Hub, add an event handler to the Hub proxy by using the `client` property of the generated proxy, or call the `on` method if you aren't using the generated proxy.</span></span> <span data-ttu-id="a9154-286">Parametry lze komplexních objektů.</span><span class="sxs-lookup"><span data-stu-id="a9154-286">The parameters can be complex objects.</span></span>

<span data-ttu-id="a9154-287">Přidat obslužnou rutinu události před voláním `start` metoda k navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-287">Add the event handler before you call the `start` method to establish the connection.</span></span> <span data-ttu-id="a9154-288">(Pokud chcete přidat obslužné rutiny událostí po volání `start` metodu, přečtěte si poznámku v [jak k navázání připojení](#establishconnection) dříve v tomto dokumentu a pomocí syntaxe pro definování metody bez použití vygenerovaný proxy server.)</span><span class="sxs-lookup"><span data-stu-id="a9154-288">(If you want to add event handlers after calling the `start` method, see the note in [How to establish a connection](#establishconnection) earlier in this document, and use the syntax shown for defining a method without using the generated proxy.)</span></span>

<span data-ttu-id="a9154-289">Shoda názvu metody je velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="a9154-289">Method name matching is case-insensitive.</span></span> <span data-ttu-id="a9154-290">Například `Clients.All.addContosoChatMessageToPage` na serveru spustí `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, nebo `addcontosochatmessagetopage` na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a9154-290">For example, `Clients.All.addContosoChatMessageToPage` on the server will execute `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, or `addcontosochatmessagetopage` on the client.</span></span>

<span data-ttu-id="a9154-291">**Definujte metodu na klientovi (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-291">**Define method on client (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample27.js?highlight=2)]

<span data-ttu-id="a9154-292">**Alternativní způsob, jak definovat metodu na klientovi (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-292">**Alternate way to define method on client (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample28.js?highlight=1-2)]

<span data-ttu-id="a9154-293">**Definujte metodu na klientovi (bez vygenerovaný proxy server, nebo při přidávání po volání metody start)**</span><span class="sxs-lookup"><span data-stu-id="a9154-293">**Define method on client (without the generated proxy, or when adding after calling the start method)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample29.js?highlight=3)]

<span data-ttu-id="a9154-294">**Kód serveru, který volá metodu klienta**</span><span class="sxs-lookup"><span data-stu-id="a9154-294">**Server code that calls the client method**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample30.cs?highlight=5)]

<span data-ttu-id="a9154-295">Následující příklady zahrnují komplexního objektu jako parametr metody.</span><span class="sxs-lookup"><span data-stu-id="a9154-295">The following examples include a complex object as a method parameter.</span></span>

<span data-ttu-id="a9154-296">**Definujte metodu na klienta, který používá komplexního objektu (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-296">**Define method on client that takes a complex object (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample31.js?highlight=2-3)]

<span data-ttu-id="a9154-297">**Definujte metodu na klienta, který používá komplexního objektu (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-297">**Define method on client that takes a complex object (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample32.js?highlight=3-4)]

<span data-ttu-id="a9154-298">**Kód serveru, který definuje komplexní objekt**</span><span class="sxs-lookup"><span data-stu-id="a9154-298">**Server code that defines the complex object**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample33.cs?highlight=1)]

<span data-ttu-id="a9154-299">**Kód serveru, který volá metodu klienta s použitím komplexní objekt**</span><span class="sxs-lookup"><span data-stu-id="a9154-299">**Server code that calls the client method using a complex object**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample34.cs?highlight=3)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a><span data-ttu-id="a9154-300">Volání metody serveru z klienta</span><span class="sxs-lookup"><span data-stu-id="a9154-300">How to call server methods from the client</span></span>

<span data-ttu-id="a9154-301">Chcete-li volat metodu serveru z klienta, použijte `server` vlastnost vygenerovaný proxy server nebo `invoke` metodu na proxy server rozbočovače, pokud nepoužíváte vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="a9154-301">To call a server method from the client, use the `server` property of the generated proxy or the `invoke` method on the Hub proxy if you aren't using the generated proxy.</span></span> <span data-ttu-id="a9154-302">Návratová hodnota nebo parametrů může být složité objekty.</span><span class="sxs-lookup"><span data-stu-id="a9154-302">The return value or parameters can be complex objects.</span></span>

<span data-ttu-id="a9154-303">Předat ve stylu camelCase verzi název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a9154-303">Pass in a camel-case version of the method name on the Hub.</span></span> <span data-ttu-id="a9154-304">Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a9154-304">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="a9154-305">Následující příklady ukazují, jak volat metodu serveru, který nemá návratovou hodnotu a volání metody serveru, který nemá návratovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a9154-305">The following examples show how to call a server method that doesn't have a return value and how to call a server method that does have a return value.</span></span>

<span data-ttu-id="a9154-306">**Metoda serveru se žádný atribut HubMethodName**</span><span class="sxs-lookup"><span data-stu-id="a9154-306">**Server method with no HubMethodName attribute**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample35.cs?highlight=3)]

<span data-ttu-id="a9154-307">**Kód serveru, který definuje komplexní objekt předaný v parametru**</span><span class="sxs-lookup"><span data-stu-id="a9154-307">**Server code that defines the complex object passed in a parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample36.cs)]

<span data-ttu-id="a9154-308">**Klientský kód, který vyvolá metodu serveru (se vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-308">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample37.js?highlight=1)]

<span data-ttu-id="a9154-309">**Klientský kód, který vyvolá metodu serveru (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-309">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample38.js?highlight=1)]

<span data-ttu-id="a9154-310">Pokud upravena pomocí metody rozbočovače `HubMethodName` atribut, použijte tento název bez Změna velikosti písmen.</span><span class="sxs-lookup"><span data-stu-id="a9154-310">If you decorated the Hub method with a `HubMethodName` attribute, use that name without changing case.</span></span>

<span data-ttu-id="a9154-311">**Metoda serveru** s atributem HubMethodName</span><span class="sxs-lookup"><span data-stu-id="a9154-311">**Server method** with a HubMethodName attribute</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample39.cs?highlight=3)]

<span data-ttu-id="a9154-312">**Klientský kód, který vyvolá metodu serveru (se vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-312">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample40.js?highlight=1)]

<span data-ttu-id="a9154-313">**Klientský kód, který vyvolá metodu serveru (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-313">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample41.js?highlight=1)]

<span data-ttu-id="a9154-314">Předchozí příklady ukazují, jak volat metodu serveru, která nemá žádnou návratovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a9154-314">The preceding examples show how to call a server method that has no return value.</span></span> <span data-ttu-id="a9154-315">Následující příklady znázorňují způsob volání metody serveru, který má návratovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a9154-315">The following examples show how to call a server method that has a return value.</span></span>

<span data-ttu-id="a9154-316">**Serverový kód pro metodu, která nemá návratovou hodnotu**</span><span class="sxs-lookup"><span data-stu-id="a9154-316">**Server code for a method that has a return value**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample42.cs?highlight=3)]

<span data-ttu-id="a9154-317">**Třída akcie pro** návratová hodnota</span><span class="sxs-lookup"><span data-stu-id="a9154-317">**The Stock class used for the** return value</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample43.cs?highlight=1)]

<span data-ttu-id="a9154-318">**Klientský kód, který vyvolá metodu serveru (se vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-318">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample44.js?highlight=2,4-5)]

<span data-ttu-id="a9154-319">**Klientský kód, který vyvolá metodu serveru (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-319">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample45.js?highlight=2,4-5)]

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a><span data-ttu-id="a9154-320">Zpracování událostí doby platnosti</span><span class="sxs-lookup"><span data-stu-id="a9154-320">How to handle connection lifetime events</span></span>

<span data-ttu-id="a9154-321">Funkce SignalR poskytuje následující připojení události doby života, které dokáže zpracovat:</span><span class="sxs-lookup"><span data-stu-id="a9154-321">SignalR provides the following connection lifetime events that you can handle:</span></span>

- <span data-ttu-id="a9154-322">`starting`: Vyvoláno před odesláním žádná data přes dané připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-322">`starting`: Raised before any data is sent over the connection.</span></span>
- <span data-ttu-id="a9154-323">`received`: Vyvoláno, když je veškerá data přijatá v připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-323">`received`: Raised when any data is received on the connection.</span></span> <span data-ttu-id="a9154-324">Poskytuje přijatá data.</span><span class="sxs-lookup"><span data-stu-id="a9154-324">Provides the received data.</span></span>
- <span data-ttu-id="a9154-325">`connectionSlow`: Vyvoláno, když klient zjistí pomalý nebo často vyřazení připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-325">`connectionSlow`: Raised when the client detects a slow or frequently dropping connection.</span></span>
- <span data-ttu-id="a9154-326">`reconnecting`: Vyvolá se při přenosu začne znovu obnovovat.</span><span class="sxs-lookup"><span data-stu-id="a9154-326">`reconnecting`: Raised when the underlying transport begins reconnecting.</span></span>
- <span data-ttu-id="a9154-327">`reconnected`: Vyvolá se při přenosu má připojen.</span><span class="sxs-lookup"><span data-stu-id="a9154-327">`reconnected`: Raised when the underlying transport has reconnected.</span></span>
- <span data-ttu-id="a9154-328">`stateChanged`: Vyvolá se při změně stavu připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-328">`stateChanged`: Raised when the connection state changes.</span></span> <span data-ttu-id="a9154-329">Poskytuje starý stav a nový stav (připojování, připojeno, znovu připojíte nebo odpojeno).</span><span class="sxs-lookup"><span data-stu-id="a9154-329">Provides the old state and the new state (Connecting, Connected, Reconnecting, or Disconnected).</span></span>
- <span data-ttu-id="a9154-330">`disconnected`: Vyvolá se při připojení se odpojil.</span><span class="sxs-lookup"><span data-stu-id="a9154-330">`disconnected`: Raised when the connection has disconnected.</span></span>

<span data-ttu-id="a9154-331">Například, pokud chcete zobrazit upozornění, pokud existují problémy s připojením, které může způsobit znatelné zpoždění při, zpracovat `connectionSlow` událostí.</span><span class="sxs-lookup"><span data-stu-id="a9154-331">For example, if you want to display warning messages when there are connection problems that might cause noticeable delays, handle the `connectionSlow` event.</span></span>

<span data-ttu-id="a9154-332">**Zpracování události connectionSlow (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-332">**Handle the connectionSlow event (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample46.js?highlight=1)]

<span data-ttu-id="a9154-333">**Zpracování události connectionSlow (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-333">**Handle the connectionSlow event (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample47.js?highlight=2)]

<span data-ttu-id="a9154-334">Další informace najdete v tématu [principy a zpracování událostí doby platnosti v knihovně SignalR](handling-connection-lifetime-events.md).</span><span class="sxs-lookup"><span data-stu-id="a9154-334">For more information, see [Understanding and Handling Connection Lifetime Events in SignalR](handling-connection-lifetime-events.md).</span></span>

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a><span data-ttu-id="a9154-335">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="a9154-335">How to handle errors</span></span>

<span data-ttu-id="a9154-336">Poskytuje klientovi SignalR JavaScript `error` události, které můžete přidat obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="a9154-336">The SignalR JavaScript client provides an `error` event that you can add a handler for.</span></span> <span data-ttu-id="a9154-337">Také vám pomůže metodu selhání přidejte obslužnou rutinu chyby, které jsou výsledkem volání metody serveru.</span><span class="sxs-lookup"><span data-stu-id="a9154-337">You can also use the fail method to add a handler for errors that result from a server method invocation.</span></span>

<span data-ttu-id="a9154-338">Pokud není explicitně povolit podrobné chybové zprávy na serveru, obsahuje objekt výjimky, která vrací SignalR po chybě minimální informace o této chybě.</span><span class="sxs-lookup"><span data-stu-id="a9154-338">If you don't explicitly enable detailed error messages on the server, the exception object that SignalR returns after an error contains minimal information about the error.</span></span> <span data-ttu-id="a9154-339">Například, pokud je volání `newContosoChatMessage` selže, chybová zpráva v objektu chyba obsahuje "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" podrobné chybové zprávy pro klienty v produkčním prostředí se nedoporučuje z bezpečnostních důvodů, ale pokud chcete povolit podrobné chybové zprávy pro odesílání na serveru pro účely odstraňování potíží, použijte následující kód.</span><span class="sxs-lookup"><span data-stu-id="a9154-339">For example, if a call to `newContosoChatMessage` fails, the error message in the error object contains "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Sending detailed error messages to clients in production is not recommended for security reasons, but if you want to enable detailed error messages for troubleshooting purposes, use the following code on the server.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample48.cs?highlight=2)]

<span data-ttu-id="a9154-340">Následující příklad ukazuje, jak přidat obslužnou rutinu pro událost chyby.</span><span class="sxs-lookup"><span data-stu-id="a9154-340">The following example shows how to add a handler for the error event.</span></span>

<span data-ttu-id="a9154-341">**Přidat obslužnou rutinu chyby (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-341">**Add an error handler (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample49.js?highlight=1)]

<span data-ttu-id="a9154-342">**Přidat obslužnou rutinu chyby (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-342">**Add an error handler (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample50.js?highlight=2)]

<span data-ttu-id="a9154-343">Následující příklad ukazuje, jak zpracovat chybu z volání metody.</span><span class="sxs-lookup"><span data-stu-id="a9154-343">The following example shows how to handle an error from a method invocation.</span></span>

<span data-ttu-id="a9154-344">**Zpracování chyb z volání metody (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-344">**Handle an error from a method invocation (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample51.js?highlight=2)]

<span data-ttu-id="a9154-345">**Zpracování chyb z volání metody (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-345">**Handle an error from a method invocation (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample52.js?highlight=2)]

<span data-ttu-id="a9154-346">Pokud selže volání metody, `error` událost se vyvolá také, takže svůj kód v `error` metoda obslužné rutiny a `.fail` by provedení metody zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="a9154-346">If a method invocation fails, the `error` event is also raised, so your code in the `error` method handler and in the `.fail` method callback would execute.</span></span>

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a><span data-ttu-id="a9154-347">Jak povolit protokolování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="a9154-347">How to enable client-side logging</span></span>

<span data-ttu-id="a9154-348">Chcete-li povolit protokolování na straně klienta na připojení, nastavte `logging` vlastnost u objektu připojení před voláním `start` metoda k navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="a9154-348">To enable client-side logging on a connection, set the `logging` property on the connection object before you call the `start` method to establish the connection.</span></span>

<span data-ttu-id="a9154-349">**Povolit protokolování (pomocí vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-349">**Enable logging (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample53.js?highlight=1)]

<span data-ttu-id="a9154-350">**Povolení protokolování (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="a9154-350">**Enable logging (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample54.js?highlight=2)]

<span data-ttu-id="a9154-351">Pokud chcete zobrazit protokoly, otevřete vývojářské nástroje v prohlížeči a přejděte na kartu konzoly. Kurz, který zobrazuje podrobné pokyny a obrazovky snímky, které ukazují, jak to provést, najdete v tématu [serverové vysílání s knihovnou ASP.NET Signalr – povolit protokolování](../getting-started/tutorial-server-broadcast-with-signalr.md#enablelogging).</span><span class="sxs-lookup"><span data-stu-id="a9154-351">To see the logs, open your browser's developer tools and go to the Console tab. For a tutorial that shows step-by-step instructions and screen shots that show how to do this, see [Server Broadcast with ASP.NET Signalr - Enable Logging](../getting-started/tutorial-server-broadcast-with-signalr.md#enablelogging).</span></span>
