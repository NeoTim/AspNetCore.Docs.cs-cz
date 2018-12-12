---
uid: signalr/overview/older-versions/signalr-1x-hubs-api-guide-javascript-client
title: Pokyny rozhraní API Center SignalR 1.x – javascriptový klient | Dokumentace Microsoftu
author: pfletcher
description: Tento dokument obsahuje úvod k používání rozhraní API rozbočovače pro funkci SignalR verze 1.1 v klientech jazyka JavaScript, například s prohlížeči a Windows Store (WinJS) applic...
ms.author: riande
ms.date: 04/17/2013
ms.assetid: dcd4593b-1118-418a-af71-d12ff33fb36d
msc.legacyurl: /signalr/overview/older-versions/signalr-1x-hubs-api-guide-javascript-client
msc.type: authoredcontent
ms.openlocfilehash: 2d50a92cff96be5c5c60105bba6682d38f9666b6
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53288089"
---
<a name="signalr-1x-hubs-api-guide---javascript-client"></a><span data-ttu-id="79020-103">Pokyny k rozhraní API center SignalR 1.x – javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="79020-103">SignalR 1.x Hubs API Guide - JavaScript Client</span></span>
====================
<span data-ttu-id="79020-104">podle [Patrick Fletcher](https://github.com/pfletcher), [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="79020-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="79020-105">Tento dokument obsahuje úvod k používání rozhraní API rozbočovače pro funkci SignalR v klientech jazyka JavaScript, například s prohlížeči a aplikace Windows Store (WinJS) verze 1.1.</span><span class="sxs-lookup"><span data-stu-id="79020-105">This document provides an introduction to using the Hubs API for SignalR version 1.1 in JavaScript clients, such as browsers and Windows Store (WinJS) applications.</span></span>
> 
> <span data-ttu-id="79020-106">Rozhraní API pro rozbočovače SignalR umožňuje vytvářet vzdálených volání procedur (RPC) ze serveru pro připojené klienty a z klientů k serveru.</span><span class="sxs-lookup"><span data-stu-id="79020-106">The SignalR Hubs API enables you to make remote procedure calls (RPCs) from a server to connected clients and from clients to the server.</span></span> <span data-ttu-id="79020-107">V serverovém kódu můžete definovat metody, které mohou být volány klientů a volat metody, které běží na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="79020-107">In server code, you define methods that can be called by clients, and you call methods that run on the client.</span></span> <span data-ttu-id="79020-108">V klientském kódu můžete definovat metody, které lze volat ze serveru a volání metody, které běží na serveru.</span><span class="sxs-lookup"><span data-stu-id="79020-108">In client code, you define methods that can be called from the server, and you call methods that run on the server.</span></span> <span data-ttu-id="79020-109">Funkce SignalR postará za vás zajistí funkčnost systému klient server.</span><span class="sxs-lookup"><span data-stu-id="79020-109">SignalR takes care of all of the client-to-server plumbing for you.</span></span>
> 
> <span data-ttu-id="79020-110">Funkce SignalR také nabízí nižší úrovně rozhraní API volá trvalé připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-110">SignalR also offers a lower-level API called Persistent Connections.</span></span> <span data-ttu-id="79020-111">Úvod do SignalR, rozbočovačů a trvalá připojení, nebo kurz, který ukazuje, jak sestavit kompletní aplikace SignalR, přečtěte si téma [SignalR – Začínáme](../getting-started/index.md).</span><span class="sxs-lookup"><span data-stu-id="79020-111">For an introduction to SignalR, Hubs, and Persistent Connections, or for a tutorial that shows how to build a complete SignalR application, see [SignalR - Getting Started](../getting-started/index.md).</span></span>


## <a name="overview"></a><span data-ttu-id="79020-112">Přehled</span><span class="sxs-lookup"><span data-stu-id="79020-112">Overview</span></span>

<span data-ttu-id="79020-113">Tento dokument obsahuje následující části:</span><span class="sxs-lookup"><span data-stu-id="79020-113">This document contains the following sections:</span></span>

- [<span data-ttu-id="79020-114">Vygenerovaný proxy server a co to dělá za vás</span><span class="sxs-lookup"><span data-stu-id="79020-114">The generated proxy and what it does for you</span></span>](#genproxy)

    - [<span data-ttu-id="79020-115">Kdy použít vygenerovaný proxy server</span><span class="sxs-lookup"><span data-stu-id="79020-115">When to use the generated proxy</span></span>](#cantusegenproxy)
- [<span data-ttu-id="79020-116">Instalace klienta</span><span class="sxs-lookup"><span data-stu-id="79020-116">Client Setup</span></span>](#clientsetup)

    - [<span data-ttu-id="79020-117">Způsob vytvoření odkazu dynamicky generované proxy</span><span class="sxs-lookup"><span data-stu-id="79020-117">How to reference the dynamically generated proxy</span></span>](#dynamicproxy)
    - [<span data-ttu-id="79020-118">Vytváření fyzického souboru pro funkci SignalR generované proxy</span><span class="sxs-lookup"><span data-stu-id="79020-118">How to create a physical file for the SignalR generated proxy</span></span>](#manualproxy)
- [<span data-ttu-id="79020-119">Postup vytvoření připojení</span><span class="sxs-lookup"><span data-stu-id="79020-119">How to establish a connection</span></span>](#establishconnection)

    - [<span data-ttu-id="79020-120">$. connection.hub je stejný objekt, vytvoří tento $.hubConnection()</span><span class="sxs-lookup"><span data-stu-id="79020-120">$.connection.hub is the same object that $.hubConnection() creates</span></span>](#connequivalence)
    - [<span data-ttu-id="79020-121">Asynchronní provádění tohoto start – metoda</span><span class="sxs-lookup"><span data-stu-id="79020-121">Asynchronous execution of the start method</span></span>](#asyncstart)
- [<span data-ttu-id="79020-122">Jak vytvořit připojení mezi doménami</span><span class="sxs-lookup"><span data-stu-id="79020-122">How to establish a cross-domain connection</span></span>](#crossdomain)
- [<span data-ttu-id="79020-123">Postup konfigurace připojení</span><span class="sxs-lookup"><span data-stu-id="79020-123">How to configure the connection</span></span>](#configureconnection)

    - [<span data-ttu-id="79020-124">Určení parametrů řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="79020-124">How to specify query string parameters</span></span>](#querystring)
    - [<span data-ttu-id="79020-125">Jak určit metodu přenosu</span><span class="sxs-lookup"><span data-stu-id="79020-125">How to specify the transport method</span></span>](#transport)
- [<span data-ttu-id="79020-126">Získání proxy serveru pro rozbočovač třídy</span><span class="sxs-lookup"><span data-stu-id="79020-126">How to get a proxy for a Hub class</span></span>](#getproxy)
- [<span data-ttu-id="79020-127">Definování metody na straně klienta, která může volat na serveru</span><span class="sxs-lookup"><span data-stu-id="79020-127">How to define methods on the client that the server can call</span></span>](#callclient)
- [<span data-ttu-id="79020-128">Volání metody serveru z klienta</span><span class="sxs-lookup"><span data-stu-id="79020-128">How to call server methods from the client</span></span>](#callserver)
- [<span data-ttu-id="79020-129">Zpracování událostí doby platnosti</span><span class="sxs-lookup"><span data-stu-id="79020-129">How to handle connection lifetime events</span></span>](#connectionlifetime)
- [<span data-ttu-id="79020-130">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="79020-130">How to handle errors</span></span>](#handleerrors)
- [<span data-ttu-id="79020-131">Jak povolit protokolování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="79020-131">How to enable client-side logging</span></span>](#logging)

<span data-ttu-id="79020-132">Dokumentace o tom, jak program na serveru nebo klientů .NET, naleznete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="79020-132">For documentation on how to program the server or .NET clients, see the following resources:</span></span>

- [<span data-ttu-id="79020-133">Pokyny k rozhraní API Center SignalR – Server</span><span class="sxs-lookup"><span data-stu-id="79020-133">SignalR Hubs API Guide - Server</span></span>](../guide-to-the-api/hubs-api-guide-server.md)
- [<span data-ttu-id="79020-134">Pokyny k rozhraní API Center SignalR – klient .NET</span><span class="sxs-lookup"><span data-stu-id="79020-134">SignalR Hubs API Guide - .NET Client</span></span>](../guide-to-the-api/hubs-api-guide-net-client.md)

<span data-ttu-id="79020-135">Odkazy na témata, Reference k rozhraní API se API verze rozhraní .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="79020-135">Links to API Reference topics are to the .NET 4.5 version of the API.</span></span> <span data-ttu-id="79020-136">Pokud používáte .NET 4, přečtěte si téma [verze .NET 4 témat API](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).</span><span class="sxs-lookup"><span data-stu-id="79020-136">If you're using .NET 4, see [the .NET 4 version of the API topics](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).</span></span>

<a id="genproxy"></a>

## <a name="the-generated-proxy-and-what-it-does-for-you"></a><span data-ttu-id="79020-137">Vygenerovaný proxy server a co to dělá za vás</span><span class="sxs-lookup"><span data-stu-id="79020-137">The generated proxy and what it does for you</span></span>

<span data-ttu-id="79020-138">Můžete naprogramovat javascriptový klient komunikovat se službou SignalR s nebo bez proxy serveru, který generuje SignalR za vás.</span><span class="sxs-lookup"><span data-stu-id="79020-138">You can program a JavaScript client to communicate with a SignalR service with or without a proxy that SignalR generates for you.</span></span> <span data-ttu-id="79020-139">Proxy vykonává pro vás je zjednodušení syntaxe kódu slouží k připojení metod zápisu, která volá na server, a volání metod na serveru.</span><span class="sxs-lookup"><span data-stu-id="79020-139">What the proxy does for you is simplify the syntax of the code you use to connect, write methods that the server calls, and call methods on the server.</span></span>

<span data-ttu-id="79020-140">Při psaní kódu pro volání metody serveru vygenerovaný proxy server vám umožní použít syntaxi, která vypadá jako by byly provádění lokální funkce: můžete napsat `serverMethod(arg1, arg2)` místo `invoke('serverMethod', arg1, arg2)`.</span><span class="sxs-lookup"><span data-stu-id="79020-140">When you write code to call server methods, the generated proxy enables you to use syntax that looks as though you were executing a local function: you can write `serverMethod(arg1, arg2)` instead of `invoke('serverMethod', arg1, arg2)`.</span></span> <span data-ttu-id="79020-141">Syntaxe vygenerovaný proxy server také umožňuje okamžité a srozumitelné chybě na straně klienta, pokud napíšete metodu název serveru.</span><span class="sxs-lookup"><span data-stu-id="79020-141">The generated proxy syntax also enables an immediate and intelligible client-side error if you mistype a server method name.</span></span> <span data-ttu-id="79020-142">A pokud ručně vytvořit soubor, který definuje náhledy, můžete také získat podporu technologie IntelliSense pro psaní kódu, který volá metody serveru.</span><span class="sxs-lookup"><span data-stu-id="79020-142">And if you manually create the file that defines the proxies, you can also get IntelliSense support for writing code that calls server methods.</span></span>

<span data-ttu-id="79020-143">Předpokládejme například, že máte následující třídy rozbočovače na serveru:</span><span class="sxs-lookup"><span data-stu-id="79020-143">For example, suppose you have the following Hub class on the server:</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample1.cs?highlight=1,3,5)]

<span data-ttu-id="79020-144">Následující příklady kódu ukazují, co vypadá kódu JavaScript jako pro volání `NewContosoChatMessage` metodu na serveru a přijímají volání `addContosoChatMessageToPage` metoda ze serveru.</span><span class="sxs-lookup"><span data-stu-id="79020-144">The following code examples show what JavaScript code looks like for invoking the `NewContosoChatMessage` method on the server and receiving invocations of the `addContosoChatMessageToPage` method from the server.</span></span>

<span data-ttu-id="79020-145">**Vygenerovaný proxy**</span><span class="sxs-lookup"><span data-stu-id="79020-145">**With the generated proxy**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample2.js?highlight=1-2,8)]

<span data-ttu-id="79020-146">**Bez vygenerovaný proxy server**</span><span class="sxs-lookup"><span data-stu-id="79020-146">**Without the generated proxy**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample3.js?highlight=2-3,9)]

<a id="cantusegenproxy"></a>

### <a name="when-to-use-the-generated-proxy"></a><span data-ttu-id="79020-147">Kdy použít vygenerovaný proxy server</span><span class="sxs-lookup"><span data-stu-id="79020-147">When to use the generated proxy</span></span>

<span data-ttu-id="79020-148">Pokud chcete registrovat více obslužných rutin události pro metody, která volá na server, nemůžete použít vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="79020-148">If you want to register multiple event handlers for a client method that the server calls, you can't use the generated proxy.</span></span> <span data-ttu-id="79020-149">V opačném případě můžete použít vygenerovaný proxy server nebo není založen na předvolbu kódování.</span><span class="sxs-lookup"><span data-stu-id="79020-149">Otherwise, you can choose to use the generated proxy or not based on your coding preference.</span></span> <span data-ttu-id="79020-150">Pokud se rozhodnete nepoužít, není nutné odkazovat na adresu URL "Center/signalr" v `script` elementu v kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="79020-150">If you choose not to use it, you don't have to reference the "signalr/hubs" URL in a `script` element in your client code.</span></span>

<a id="clientsetup"></a>

## <a name="client-setup"></a><span data-ttu-id="79020-151">Instalace klienta</span><span class="sxs-lookup"><span data-stu-id="79020-151">Client setup</span></span>

<span data-ttu-id="79020-152">Klient JavaScriptu vyžaduje odkazy na knihovny jQuery a soubor JavaScript základní funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="79020-152">A JavaScript client requires references to jQuery and the SignalR core JavaScript file.</span></span> <span data-ttu-id="79020-153">1.6.4 nebo hlavní novější verze, jako je například 1.7.2, 1.8.2 nebo 1.9.1 musí být verze jQuery.</span><span class="sxs-lookup"><span data-stu-id="79020-153">The jQuery version must be 1.6.4 or major later versions, such as 1.7.2, 1.8.2, or 1.9.1.</span></span> <span data-ttu-id="79020-154">Pokud se rozhodnete použít vygenerovaný proxy server, potřebujete také odkaz k proxy serveru SignalR vygeneruje soubor jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="79020-154">If you decide to use the generated proxy, you also need a reference to the SignalR generated proxy JavaScript file.</span></span> <span data-ttu-id="79020-155">Následující příklad ukazuje, jak může odkazy vypadat na stránku HTML, který používá vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="79020-155">The following example shows what the references might look like in an HTML page that uses the generated proxy.</span></span>

[!code-html[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample4.html)]

<span data-ttu-id="79020-156">Tyto odkazy musí být zahrnuty v tomto pořadí: jQuery nejprve naposledy SignalR jádra po tomto a proxy servery SignalR.</span><span class="sxs-lookup"><span data-stu-id="79020-156">These references must be included in this order: jQuery first, SignalR core after that, and SignalR proxies last.</span></span>

<a id="dynamicproxy"></a>

### <a name="how-to-reference-the-dynamically-generated-proxy"></a><span data-ttu-id="79020-157">Způsob vytvoření odkazu dynamicky generované proxy</span><span class="sxs-lookup"><span data-stu-id="79020-157">How to reference the dynamically generated proxy</span></span>

<span data-ttu-id="79020-158">V předchozím příkladu je odkaz na proxy serveru SignalR vygeneruje dynamicky generovaném kódu jazyka JavaScript, není k fyzickému souboru.</span><span class="sxs-lookup"><span data-stu-id="79020-158">In the preceding example, the reference to the SignalR generated proxy is to dynamically generated JavaScript code, not to a physical file.</span></span> <span data-ttu-id="79020-159">Funkce SignalR vytvoří kód jazyka JavaScript pro proxy server v reálném čase a slouží ke klientovi jako odpověď na adresu "/ signalr/centra".</span><span class="sxs-lookup"><span data-stu-id="79020-159">SignalR creates the JavaScript code for the proxy on the fly and serves it to the client in response to the "/signalr/hubs" URL.</span></span> <span data-ttu-id="79020-160">Pokud jste zadali jiné základní adresu URL pro připojení SignalR na serveru ve vaší `MapHubs` metoda, adresu URL k souboru dynamicky generované proxy je vaše vlastní adresu URL s "/ hubs" připojí k němu.</span><span class="sxs-lookup"><span data-stu-id="79020-160">If you specified a different base URL for SignalR connections on the server in your `MapHubs` method, the URL for the dynamically generated proxy file is your custom URL with "/hubs" appended to it.</span></span>

> [!NOTE]
> <span data-ttu-id="79020-161">Pro klienty systému Windows 8 (Windows Store) jazyka JavaScript použijte soubor fyzické proxy místo dynamicky vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="79020-161">For Windows 8 (Windows Store) JavaScript clients, use the physical proxy file instead of the dynamically generated one.</span></span> <span data-ttu-id="79020-162">Další informace najdete v tématu [vytváření fyzického souboru pro funkci SignalR generované proxy](#manualproxy) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="79020-162">For more information, see [How to create a physical file for the SignalR generated proxy](#manualproxy) later in this topic.</span></span>


<span data-ttu-id="79020-163">V zobrazení syntaxe Razor rozhraní ASP.NET MVC 4 použijte tilda k odkazování na kořenový adresář aplikace v odkazu na soubor vašeho proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="79020-163">In an ASP.NET MVC 4 Razor view, use the tilde to refer to the application root in your proxy file reference:</span></span>

[!code-html[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample5.html)]

<span data-ttu-id="79020-164">Další informace o použití aplikace SignalR v MVC 4, najdete v části [Začínáme s knihovnou SignalR a MVC 4](tutorial-getting-started-with-signalr-and-mvc-4.md).</span><span class="sxs-lookup"><span data-stu-id="79020-164">For more information about using SignalR in MVC 4, see [Getting Started with SignalR and MVC 4](tutorial-getting-started-with-signalr-and-mvc-4.md).</span></span>

<span data-ttu-id="79020-165">V zobrazení syntaxe Razor rozhraní ASP.NET MVC 3, použijte `Url.Content` pro odkaz na soubor vašeho proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="79020-165">In an ASP.NET MVC 3 Razor view, use `Url.Content` for your proxy file reference:</span></span>

[!code-cshtml[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample6.cshtml)]

<span data-ttu-id="79020-166">V aplikaci webových formulářů ASP.NET pomocí `ResolveClientUrl` pro vaše proxy odkaz na soubor nebo ho zaregistrovat pomocí prvku ScriptManager pomocí cesty relativní kořenové aplikace (začíná tildou):</span><span class="sxs-lookup"><span data-stu-id="79020-166">In an ASP.NET Web Forms application, use `ResolveClientUrl` for your proxies file reference or register it via the ScriptManager using an app root relative path (starting with a tilde):</span></span>

[!code-aspx[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample7.aspx)]

<span data-ttu-id="79020-167">Obecně platí použijte stejnou metodu pro zadávání adresy URL "/ signalr/centra", který používáte pro soubory CSS a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="79020-167">As a general rule, use the same method for specifying the "/signalr/hubs" URL that you use for CSS or JavaScript files.</span></span> <span data-ttu-id="79020-168">Pokud zadáte adresu URL bez použití vlnovkou, v některých scénářích aplikace bude fungovat správně při testování v sadě Visual Studio pomocí služby IIS Express, ale selže s chyby 404 při nasazování do úplnou službu IIS.</span><span class="sxs-lookup"><span data-stu-id="79020-168">If you specify a URL without using a tilde, in some scenarios your application will work correctly when you test in Visual Studio using IIS Express but will fail with a 404 error when you deploy to full IIS.</span></span> <span data-ttu-id="79020-169">Další informace najdete v tématu **řešení odkazy na prostředky na kořenové úrovni** v [webové servery v sadě Visual Studio pro webové projekty ASP.NET](https://msdn.microsoft.com/library/58wxa9w5.aspx) na webu MSDN.</span><span class="sxs-lookup"><span data-stu-id="79020-169">For more information, see **Resolving References to Root-Level Resources** in [Web Servers in Visual Studio for ASP.NET Web Projects](https://msdn.microsoft.com/library/58wxa9w5.aspx) on the MSDN site.</span></span>

<span data-ttu-id="79020-170">Při spuštění webový projekt v sadě Visual Studio 2012 v režimu ladění, a pokud používáte Internet Explorer jako prohlížeč, zobrazí se soubor proxy v **Průzkumníka řešení** pod **dokumenty skriptu**, jak je znázorněno Následující obrázek.</span><span class="sxs-lookup"><span data-stu-id="79020-170">When you run a web project in Visual Studio 2012 in debug mode, and if you use Internet Explorer as your browser, you can see the proxy file in **Solution Explorer** under **Script Documents**, as shown in the following illustration.</span></span>

![Vygenerovaný proxy soubor jazyka JavaScript v Průzkumníku řešení](signalr-1x-hubs-api-guide-javascript-client/_static/image1.png)

<span data-ttu-id="79020-172">Pokud chcete zobrazit obsah souboru, klikněte dvakrát na **rozbočovače**.</span><span class="sxs-lookup"><span data-stu-id="79020-172">To see the contents of the file, double-click **hubs**.</span></span> <span data-ttu-id="79020-173">Pokud nepoužíváte Visual Studio 2012 a prohlížeče Internet Explorer, nebo pokud nejste v režimu ladění, můžete také získat obsah souboru tak, že přejdete na adresu "/ signalR/centra".</span><span class="sxs-lookup"><span data-stu-id="79020-173">If you are not using Visual Studio 2012 and Internet Explorer, or if you are not in debug mode, you can also get the contents of the file by browsing to the "/signalR/hubs" URL.</span></span> <span data-ttu-id="79020-174">Například, pokud vaše lokalita běží v `http://localhost:56699`, přejděte na stránku `http://localhost:56699/SignalR/hubs` v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="79020-174">For example, if your site is running at `http://localhost:56699`, go to `http://localhost:56699/SignalR/hubs` in your browser.</span></span>

<a id="manualproxy"></a>

### <a name="how-to-create-a-physical-file-for-the-signalr-generated-proxy"></a><span data-ttu-id="79020-175">Vytváření fyzického souboru pro funkci SignalR generované proxy</span><span class="sxs-lookup"><span data-stu-id="79020-175">How to create a physical file for the SignalR generated proxy</span></span>

<span data-ttu-id="79020-176">Jako alternativu k dynamicky generované proxy můžete vytvořit fyzický soubor, který má kód proxy serveru a jako reference.</span><span class="sxs-lookup"><span data-stu-id="79020-176">As an alternative to the dynamically generated proxy, you can create a physical file that has the proxy code and reference that file.</span></span> <span data-ttu-id="79020-177">Můžete to udělat pro kontrolu nad ukládání do mezipaměti nebo sdružování chování nebo aby technologie IntelliSense při psaní kódu volání metody serveru.</span><span class="sxs-lookup"><span data-stu-id="79020-177">You might want to do that for control over caching or bundling behavior, or to get IntelliSense when you are coding calls to server methods.</span></span>

<span data-ttu-id="79020-178">Vytvoření souboru proxy serveru, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="79020-178">To create a proxy file, perform the following steps:</span></span>

1. <span data-ttu-id="79020-179">Nainstalujte [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="79020-179">Install the [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) NuGet package.</span></span>
2. <span data-ttu-id="79020-180">Otevřete příkazový řádek a přejděte *nástroje* složku obsahující soubor SignalR.exe.</span><span class="sxs-lookup"><span data-stu-id="79020-180">Open a command prompt and browse to the *tools* folder that contains the SignalR.exe file.</span></span> <span data-ttu-id="79020-181">Nástroje pro složku je v následujícím umístění:</span><span class="sxs-lookup"><span data-stu-id="79020-181">The tools folder is at the following location:</span></span>

    `[your solution folder]\packages\Microsoft.AspNet.SignalR.Utils.1.0.1\tools`
3. <span data-ttu-id="79020-182">Zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="79020-182">Enter the following command:</span></span>

    `signalr ghp /path:[path to the .dll that contains your Hub class]`

    <span data-ttu-id="79020-183">Cesta k vaší *.dll* je obvykle *bin* ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="79020-183">The path to your *.dll* is typically the *bin* folder in your project folder.</span></span>

    <span data-ttu-id="79020-184">Tento příkaz vytvoří soubor s názvem *server.js* ve stejné složce jako *signalr.exe*.</span><span class="sxs-lookup"><span data-stu-id="79020-184">This command creates a file named *server.js* in the same folder as *signalr.exe*.</span></span>
4. <span data-ttu-id="79020-185">Vložit *server.js* souborů v příslušné složce ve vašem projektu, přejmenujte ho podle potřeby pro vaši aplikaci a přidejte odkaz na jeho místo odkazu "Center/signalr".</span><span class="sxs-lookup"><span data-stu-id="79020-185">Put the *server.js* file in an appropriate folder in your project, rename it as appropriate for your application, and add a reference to it in place of the "signalr/hubs" reference.</span></span>

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a><span data-ttu-id="79020-186">Postup vytvoření připojení</span><span class="sxs-lookup"><span data-stu-id="79020-186">How to establish a connection</span></span>

<span data-ttu-id="79020-187">Aby mohla navázat připojení, budete muset vytvořit objekt připojení, proxy server a zaregistrujte obslužné rutiny událostí pro metody, které lze volat ze serveru.</span><span class="sxs-lookup"><span data-stu-id="79020-187">Before you can establish a connection, you have to create a connection object, create a proxy, and register event handlers for methods that can be called from the server.</span></span> <span data-ttu-id="79020-188">Po nastavení serveru proxy a událost obslužné rutiny připojení voláním `start` metody.</span><span class="sxs-lookup"><span data-stu-id="79020-188">When the proxy and event handlers are set up, establish the connection by calling the `start` method.</span></span>

<span data-ttu-id="79020-189">Pokud používáte vygenerovaný proxy server, není nutné vytvořit objekt připojení ve svém vlastním kódu, protože vygenerovaném kódu proxy to udělá za vás.</span><span class="sxs-lookup"><span data-stu-id="79020-189">If you are using the generated proxy, you don't have to create the connection object in your own code because the generated proxy code does it for you.</span></span>

<a id="nogenconnection"></a>

<span data-ttu-id="79020-190">**Naváže připojení (vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-190">**Establish a connection (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample8.js?highlight=5)]

<span data-ttu-id="79020-191">**Navázání připojení (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-191">**Establish a connection (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample9.js?highlight=1,6)]

<span data-ttu-id="79020-192">Vzorový kód používá výchozí "/ signalr" adresa URL k připojení do služby SignalR.</span><span class="sxs-lookup"><span data-stu-id="79020-192">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="79020-193">Informace o tom, jak určit různé základní adresu URL najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - /signalr URL](../guide-to-the-api/hubs-api-guide-server.md#signalrurl).</span><span class="sxs-lookup"><span data-stu-id="79020-193">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](../guide-to-the-api/hubs-api-guide-server.md#signalrurl).</span></span>

> [!NOTE]
> <span data-ttu-id="79020-194">Obvykle registraci obslužné rutiny událostí před voláním `start` metoda k navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-194">Normally you register event handlers before calling the `start` method to establish the connection.</span></span> <span data-ttu-id="79020-195">Pokud chcete zaregistrovat několik obslužných rutin událostí po navázání připojení, můžete to udělat, ale je nutné zaregistrovat alespoň jeden z vašich handler(s) událostí před voláním `start` metody.</span><span class="sxs-lookup"><span data-stu-id="79020-195">If you want to register some event handlers after establishing the connection, you can do that, but you must register at least one of your event handler(s) before calling the `start` method.</span></span> <span data-ttu-id="79020-196">Jedním z důvodů je, že v aplikaci může být mnoho rozbočovače, ale není vhodné pro aktivaci `OnConnected` událost pro každý rozbočovač, chcete-li pouze pomocí jedné z nich.</span><span class="sxs-lookup"><span data-stu-id="79020-196">One reason for this is that there can be many Hubs in an application, but you wouldn't want to trigger the `OnConnected` event on every Hub if you are only going to use to one of them.</span></span> <span data-ttu-id="79020-197">Po vytvoření připojení je existenci metody na proxy server rozbočovače pro co říká SignalR k aktivaci `OnConnected` událostí.</span><span class="sxs-lookup"><span data-stu-id="79020-197">When the connection is established, the presence of a client method on a Hub's proxy is what tells SignalR to trigger the `OnConnected` event.</span></span> <span data-ttu-id="79020-198">Pokud nezaregistrujete všechny obslužné rutiny událostí před voláním `start` metody, bude možné volat metody v rozbočovači, ale centra `OnConnected` nebude volána metoda a žádné metody klienta, který bude vyvolán ze serveru.</span><span class="sxs-lookup"><span data-stu-id="79020-198">If you don't register any event handlers before calling the `start` method, you will be able to invoke methods on the Hub, but the Hub's `OnConnected` method won't be called and no client methods will be invoked from the server.</span></span>


<a id="connequivalence"></a>

### <a name="connectionhub-is-the-same-object-that-hubconnection-creates"></a><span data-ttu-id="79020-199">$. connection.hub je stejný objekt, vytvoří tento $.hubConnection()</span><span class="sxs-lookup"><span data-stu-id="79020-199">$.connection.hub is the same object that $.hubConnection() creates</span></span>

<span data-ttu-id="79020-200">Jak je vidět z příkladů, když použijete vygenerovaný proxy server, `$.connection.hub` odkazuje na objekt připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-200">As you can see from the examples, when you use the generated proxy, `$.connection.hub` refers to the connection object.</span></span> <span data-ttu-id="79020-201">Toto je stejný objekt, který můžete získat voláním `$.hubConnection()` když nepoužíváte vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="79020-201">This is the same object that you get by calling `$.hubConnection()` when you aren't using the generated proxy.</span></span> <span data-ttu-id="79020-202">Vygenerovaný proxy kód vytvoří spojení za vás spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="79020-202">The generated proxy code creates the connection for you by executing the following statement:</span></span>

![Vytvoření připojení v souboru vygenerovaný proxy server](signalr-1x-hubs-api-guide-javascript-client/_static/image3.png)

<span data-ttu-id="79020-204">Pokud používáte vygenerovaný proxy server, můžete provést cokoli, co se `$.connection.hub` , když nepoužíváte vygenerovaný proxy server, co lze použít s objektem připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-204">When you're using the generated proxy, you can do anything with `$.connection.hub` that you can do with a connection object when you're not using the generated proxy.</span></span>

<a id="asyncstart"></a>

### <a name="asynchronous-execution-of-the-start-method"></a><span data-ttu-id="79020-205">Asynchronní provádění tohoto start – metoda</span><span class="sxs-lookup"><span data-stu-id="79020-205">Asynchronous execution of the start method</span></span>

<span data-ttu-id="79020-206">`start` Metoda provedena asynchronně.</span><span class="sxs-lookup"><span data-stu-id="79020-206">The `start` method executes asynchronously.</span></span> <span data-ttu-id="79020-207">Vrátí [jQuery odloženo objekt](http://api.jquery.com/category/deferred-object/), což znamená, že můžete přidat funkce zpětného volání voláním metod `pipe`, `done`, a `fail`.</span><span class="sxs-lookup"><span data-stu-id="79020-207">It returns a [jQuery Deferred object](http://api.jquery.com/category/deferred-object/), which means that you can add callback functions by calling methods such as `pipe`, `done`, and `fail`.</span></span> <span data-ttu-id="79020-208">Pokud máte kód, který chcete spustit po připojení se naváže, jako je například volání metody serveru, vložte tento kód ve funkci zpětného volání nebo jeho volání z funkce zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="79020-208">If you have code that you want to execute after the connection is established, such as a call to a server method, put that code in a callback function or call it from a callback function.</span></span> <span data-ttu-id="79020-209">`.done` Metoda zpětného volání se spustí po vytvoření připojení a po žádný kód, který máte vaše `OnConnected` metoda obslužné rutiny události na server dokončí provádění.</span><span class="sxs-lookup"><span data-stu-id="79020-209">The `.done` callback method is executed after the connection has been established, and after any code that you have in your `OnConnected` event handler method on the server finishes executing.</span></span>

<span data-ttu-id="79020-210">Když vložíte příkaz "Teď připojení" z předchozího příkladu jako další řádek kódu po `start` volání metody (ne v `.done` zpětného volání), `console.log` řádek se spustí předtím, než se naváže připojení, jak je znázorněno v následujícím Příklad:</span><span class="sxs-lookup"><span data-stu-id="79020-210">If you put the "Now connected" statement from the preceding example as the next line of code after the `start` method call (not in a `.done` callback), the `console.log` line will execute before the connection is established, as shown in the following example:</span></span>

![Ukázka nesprávného způsobu psaní kódu, která se spustí po vytvoření připojení](signalr-1x-hubs-api-guide-javascript-client/_static/image5.png)

<a id="crossdomain"></a>

## <a name="how-to-establish-a-cross-domain-connection"></a><span data-ttu-id="79020-212">Jak vytvořit připojení mezi doménami</span><span class="sxs-lookup"><span data-stu-id="79020-212">How to establish a cross-domain connection</span></span>

<span data-ttu-id="79020-213">Obvykle Pokud prohlížeč načte stránku z `http://contoso.com`, připojení SignalR je ve stejné doméně, v `http://contoso.com/signalr`.</span><span class="sxs-lookup"><span data-stu-id="79020-213">Typically if the browser loads a page from `http://contoso.com`, the SignalR connection is in the same domain, at `http://contoso.com/signalr`.</span></span> <span data-ttu-id="79020-214">Pokud stránku z `http://contoso.com` vytvoří připojení k `http://fabrikam.com/signalr`, to znamená připojení mezi doménami.</span><span class="sxs-lookup"><span data-stu-id="79020-214">If the page from `http://contoso.com` makes a connection to `http://fabrikam.com/signalr`, that is a cross-domain connection.</span></span> <span data-ttu-id="79020-215">Z bezpečnostních důvodů jsou ve výchozím nastavení zakázané připojení mezi doménami.</span><span class="sxs-lookup"><span data-stu-id="79020-215">For security reasons, cross-domain connections are disabled by default.</span></span> <span data-ttu-id="79020-216">K navázání připojení mezi doménami, ujistěte se, že jsou na serveru povoleno připojení mezi doménami a zadejte adresu URL připojení, když vytvoříte objekt připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-216">To establish a cross-domain connection, make sure that cross-domain connections are enabled on the server, and specify the connection URL when you create the connection object.</span></span> <span data-ttu-id="79020-217">SignalR použije příslušné technologie pro připojení mezi doménami, například [JSONP](http://en.wikipedia.org/wiki/JSONP) nebo [CORS](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing).</span><span class="sxs-lookup"><span data-stu-id="79020-217">SignalR will use the appropriate technology for cross-domain connections, such as [JSONP](http://en.wikipedia.org/wiki/JSONP) or [CORS](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing).</span></span>

<span data-ttu-id="79020-218">Na serveru, povolit připojení mezi doménami tak, že vyberete tuto možnost, při volání `MapHubs` metody.</span><span class="sxs-lookup"><span data-stu-id="79020-218">On the server, enable cross-domain connections by selecting that option when you call the `MapHubs` method.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample10.cs?highlight=2)]

<span data-ttu-id="79020-219">V klientském počítači zadejte adresu URL, když vytvoříte objekt připojení (bez vygenerovaný proxy server) nebo před voláním metody start (s vygenerovaný proxy server).</span><span class="sxs-lookup"><span data-stu-id="79020-219">On the client, specify the URL when you create the connection object (without the generated proxy) or before you call the start method (with the generated proxy).</span></span>

<span data-ttu-id="79020-220">**Klientský kód, který určuje připojení mezi doménami (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-220">**Client code that specifies a cross-domain connection (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample11.js?highlight=1)]

<span data-ttu-id="79020-221">**Klientský kód, který určuje připojení mezi doménami (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-221">**Client code that specifies a cross-domain connection (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample12.js?highlight=1)]

<span data-ttu-id="79020-222">Při použití `$.hubConnection` konstruktoru, není nutné zahrnout `signalr` v adrese URL je automaticky přidán (Pokud nezadáte `useDefaultUrl` jako `false`).</span><span class="sxs-lookup"><span data-stu-id="79020-222">When you use the `$.hubConnection` constructor, you don't have to include `signalr` in the URL because it is added automatically (unless you specify `useDefaultUrl` as `false`).</span></span>

<span data-ttu-id="79020-223">Můžete vytvořit více připojení do různých koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="79020-223">You can create multiple connections to different endpoints.</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample13.js)]

> [!NOTE] 
> 
> - <span data-ttu-id="79020-224">Nenastavujte `jQuery.support.cors` na hodnotu true v kódu.</span><span class="sxs-lookup"><span data-stu-id="79020-224">Don't set `jQuery.support.cors` to true in your code.</span></span>
> 
>     ![JQuery.support.cors nemají nastavený na hodnotu true](signalr-1x-hubs-api-guide-javascript-client/_static/image7.png)
> 
>     <span data-ttu-id="79020-226">Funkce SignalR zpracovává používání JSONP neboli CORS.</span><span class="sxs-lookup"><span data-stu-id="79020-226">SignalR handles the use of JSONP or CORS.</span></span> <span data-ttu-id="79020-227">Nastavení `jQuery.support.cors` na hodnotu true zakáže JSONP, protože kvůli němu SignalR předpokládat, že prohlížeč podporuje CORS.</span><span class="sxs-lookup"><span data-stu-id="79020-227">Setting `jQuery.support.cors` to true disables JSONP because it causes SignalR to assume the browser supports CORS.</span></span>
> - <span data-ttu-id="79020-228">Když se připojujete k adrese URL místního hostitele, Internet Explorer 10 nebude považují za připojení mezi doménami, tak, že aplikace bude fungovat místně pomocí aplikace Internet Explorer 10 i v případě, že jste ještě nepovolili mezi doménami připojení na serveru.</span><span class="sxs-lookup"><span data-stu-id="79020-228">When you're connecting to a localhost URL, Internet Explorer 10 won't consider it a cross-domain connection, so the application will work locally with IE 10 even if you haven't enabled cross-domain connections on the server.</span></span>
> - <span data-ttu-id="79020-229">Informace o použití připojení mezi doménami se aplikace Internet Explorer 9, najdete v tématu [toto vlákno na StackOverflow](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).</span><span class="sxs-lookup"><span data-stu-id="79020-229">For information about using cross-domain connections with Internet Explorer 9, see [this StackOverflow thread](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).</span></span>
> - <span data-ttu-id="79020-230">Informace o použití připojení mezi doménami s prohlížečem Chrome naleznete v tématu [toto vlákno na StackOverflow](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).</span><span class="sxs-lookup"><span data-stu-id="79020-230">For information about using cross-domain connections with Chrome, see [this StackOverflow thread](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).</span></span>
> - <span data-ttu-id="79020-231">Vzorový kód používá výchozí "/ signalr" adresa URL k připojení do služby SignalR.</span><span class="sxs-lookup"><span data-stu-id="79020-231">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="79020-232">Informace o tom, jak určit různé základní adresu URL najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - /signalr URL](../guide-to-the-api/hubs-api-guide-server.md#signalrurl).</span><span class="sxs-lookup"><span data-stu-id="79020-232">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](../guide-to-the-api/hubs-api-guide-server.md#signalrurl).</span></span>


<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a><span data-ttu-id="79020-233">Postup konfigurace připojení</span><span class="sxs-lookup"><span data-stu-id="79020-233">How to configure the connection</span></span>

<span data-ttu-id="79020-234">Než vytvoříte připojení, můžete určit parametry řetězce dotazu nebo zadat metodu přenosu.</span><span class="sxs-lookup"><span data-stu-id="79020-234">Before you establish a connection, you can specify query string parameters or specify the transport method.</span></span>

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a><span data-ttu-id="79020-235">Určení parametrů řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="79020-235">How to specify query string parameters</span></span>

<span data-ttu-id="79020-236">Pokud chcete odesílat data do serveru, když se klient připojí, můžete přidat parametry řetězce dotazu do objekt připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-236">If you want to send data to the server when the client connects, you can add query string parameters to the connection object.</span></span> <span data-ttu-id="79020-237">Následující příklady ukazují, jak nastavit parametr řetězce dotazu v klientském kódu.</span><span class="sxs-lookup"><span data-stu-id="79020-237">The following examples show how to set a query string parameter in client code.</span></span>

<span data-ttu-id="79020-238">**Nastavte hodnotu řetězce dotazu před voláním metody spuštění (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-238">**Set a query string value before calling the start method (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample14.js?highlight=1)]

<span data-ttu-id="79020-239">**Nastavte hodnotu řetězce dotazu před voláním metody start (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-239">**Set a query string value before calling the start method (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample15.js?highlight=2)]

<span data-ttu-id="79020-240">Následující příklad znázorňuje způsob čtení parametru řetězce dotazu v serverovém kódu.</span><span class="sxs-lookup"><span data-stu-id="79020-240">The following example shows how to read a query string parameter in server code.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample16.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a><span data-ttu-id="79020-241">Jak určit metodu přenosu</span><span class="sxs-lookup"><span data-stu-id="79020-241">How to specify the transport method</span></span>

<span data-ttu-id="79020-242">Jako součást procesu připojování klienta SignalR obvykle vyjedná se serverem a určit nejlepší přenos, který je podporovaný server i klient.</span><span class="sxs-lookup"><span data-stu-id="79020-242">As part of the process of connecting, a SignalR client normally negotiates with the server to determine the best transport that is supported by both server and client.</span></span> <span data-ttu-id="79020-243">Pokud již víte, jaké přenosu, kterou chcete použít, můžete tento proces vyjednávání obejít tak, že určíte metodu přenosu při volání `start` metody.</span><span class="sxs-lookup"><span data-stu-id="79020-243">If you already know which transport you want to use, you can bypass this negotiation process by specifying the transport method when you call the `start` method.</span></span>

<span data-ttu-id="79020-244">**Klientský kód, který určuje metodu přenosu (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-244">**Client code that specifies the transport method (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample17.js?highlight=1)]

<span data-ttu-id="79020-245">**Klientský kód, který určuje metodu přenosu (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-245">**Client code that specifies the transport method (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample18.js?highlight=2)]

<span data-ttu-id="79020-246">Jako alternativu můžete zadat několik metod přenosu v pořadí, ve kterém chcete SignalR chcete vyzkoušet:</span><span class="sxs-lookup"><span data-stu-id="79020-246">As an alternative, you can specify multiple transport methods in the order in which you want SignalR to try them:</span></span>

<span data-ttu-id="79020-247">**Klientský kód, který určuje schéma záložního vlastní přenosu (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-247">**Client code that specifies a custom transport fallback scheme (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample19.js?highlight=1)]

<span data-ttu-id="79020-248">**Klientský kód, který určuje záložní schématu vlastní přenosu (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-248">**Client code that specifies a custom transport fallback scheme (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample20.js?highlight=2)]

<span data-ttu-id="79020-249">Pro zadání metodu přenosu můžete použít následující hodnoty:</span><span class="sxs-lookup"><span data-stu-id="79020-249">You can use the following values for specifying the transport method:</span></span>

- <span data-ttu-id="79020-250">"webSockets"</span><span class="sxs-lookup"><span data-stu-id="79020-250">"webSockets"</span></span>
- <span data-ttu-id="79020-251">"foreverFrame"</span><span class="sxs-lookup"><span data-stu-id="79020-251">"foreverFrame"</span></span>
- <span data-ttu-id="79020-252">"serverSentEvents"</span><span class="sxs-lookup"><span data-stu-id="79020-252">"serverSentEvents"</span></span>
- <span data-ttu-id="79020-253">"longPolling"</span><span class="sxs-lookup"><span data-stu-id="79020-253">"longPolling"</span></span>

<span data-ttu-id="79020-254">Následující příklady ukazují, jak zjistit, jakou metodu přenosu používá připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-254">The following examples show how to find out which transport method is being used by a connection.</span></span>

<span data-ttu-id="79020-255">**Klientský kód, který se zobrazí přenos metodu používanou pro připojení (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-255">**Client code that displays the transport method used by a connection (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample21.js?highlight=2)]

<span data-ttu-id="79020-256">**Klientský kód, který se zobrazí přenos metodu používanou pro připojení (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-256">**Client code that displays the transport method used by a connection (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample22.js?highlight=3)]

<span data-ttu-id="79020-257">Informace o tom, jak zkontrolovat metodu přenosu v serverovém kódu najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - jak získat informace o klientovi z kontextové vlastnosti](../guide-to-the-api/hubs-api-guide-server.md#contextproperty).</span><span class="sxs-lookup"><span data-stu-id="79020-257">For information about how to check the transport method in server code, see [ASP.NET SignalR Hubs API Guide - Server - How to get information about the client from the Context property](../guide-to-the-api/hubs-api-guide-server.md#contextproperty).</span></span> <span data-ttu-id="79020-258">Další informace o přenosy a náhrad najdete v tématu [Úvod ke knihovně SignalR – přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="79020-258">For more information about transports and fallbacks, see [Introduction to SignalR - Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="getproxy"></a>

## <a name="how-to-get-a-proxy-for-a-hub-class"></a><span data-ttu-id="79020-259">Získání proxy serveru pro rozbočovač třídy</span><span class="sxs-lookup"><span data-stu-id="79020-259">How to get a proxy for a Hub class</span></span>

<span data-ttu-id="79020-260">Každý objekt připojení, který vytvoříte zapouzdřuje informace o připojení ke službě SignalR, která obsahuje jednu nebo více tříd rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="79020-260">Each connection object that you create encapsulates information about a connection to a SignalR service that contains one or more Hub classes.</span></span> <span data-ttu-id="79020-261">Ke komunikaci s třídou rozbočovače, použijte objekt proxy které sami vytvoříte (Pokud nepoužíváte vygenerovaný proxy server) nebo který je generován za vás.</span><span class="sxs-lookup"><span data-stu-id="79020-261">To communicate with a Hub class, you use a proxy object which you create yourself (if you're not using the generated proxy) or which is generated for you.</span></span>

<span data-ttu-id="79020-262">Název proxy serveru na straně klienta je verze ve formátu camelCase názvu třídy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="79020-262">On the client the proxy name is a camel-cased version of the Hub class name.</span></span> <span data-ttu-id="79020-263">Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="79020-263">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="79020-264">**Třída rozbočovače na serveru**</span><span class="sxs-lookup"><span data-stu-id="79020-264">**Hub class on server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample23.cs?highlight=1)]

<span data-ttu-id="79020-265">**Získání odkazu na proxy serveru generovaného klienta pro rozbočovač**</span><span class="sxs-lookup"><span data-stu-id="79020-265">**Get a reference to the generated client proxy for the Hub**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample24.js?highlight=1)]

<span data-ttu-id="79020-266">**Vytvořit proxy klienta pro rozbočovač třídu (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-266">**Create client proxy for the Hub class (without generated proxy)**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample25.cs?highlight=1)]

<span data-ttu-id="79020-267">Pokud uspořádání vaší třídy centra s `HubName` atribut, použít přesný název bez Změna velikosti písmen.</span><span class="sxs-lookup"><span data-stu-id="79020-267">If you decorate your Hub class with a `HubName` attribute, use the exact name without changing case.</span></span>

<span data-ttu-id="79020-268">**Třída rozbočovače na serveru s atributem HubName**</span><span class="sxs-lookup"><span data-stu-id="79020-268">**Hub class on server with HubName attribute**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample26.cs?highlight=1)]

<span data-ttu-id="79020-269">**Získání odkazu na proxy serveru generovaného klienta pro rozbočovač**</span><span class="sxs-lookup"><span data-stu-id="79020-269">**Get a reference to the generated client proxy for the Hub**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample27.js?highlight=1)]

<span data-ttu-id="79020-270">**Vytvořit proxy klienta pro rozbočovač třídu (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-270">**Create client proxy for the Hub class (without generated proxy)**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample28.cs?highlight=1)]

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a><span data-ttu-id="79020-271">Definování metody na straně klienta, která může volat na serveru</span><span class="sxs-lookup"><span data-stu-id="79020-271">How to define methods on the client that the server can call</span></span>

<span data-ttu-id="79020-272">Chcete-li definovat metodu, která serveru můžete volat z rozbočovač, přidejte obslužnou rutinu události pro proxy server rozbočovače pomocí `client` vlastnost vygenerovaný proxy server nebo volání `on` metody, pokud nepoužíváte vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="79020-272">To define a method that the server can call from a Hub, add an event handler to the Hub proxy by using the `client` property of the generated proxy, or call the `on` method if you aren't using the generated proxy.</span></span> <span data-ttu-id="79020-273">Parametry lze komplexních objektů.</span><span class="sxs-lookup"><span data-stu-id="79020-273">The parameters can be complex objects.</span></span>

<span data-ttu-id="79020-274">Přidat obslužnou rutinu události před voláním `start` metoda k navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-274">Add the event handler before you call the `start` method to establish the connection.</span></span> <span data-ttu-id="79020-275">(Pokud chcete přidat obslužné rutiny událostí po volání `start` metodu, přečtěte si poznámku v [jak k navázání připojení](#establishconnection) dříve v tomto dokumentu a pomocí syntaxe pro definování metody bez použití vygenerovaný proxy server.)</span><span class="sxs-lookup"><span data-stu-id="79020-275">(If you want to add event handlers after calling the `start` method, see the note in [How to establish a connection](#establishconnection) earlier in this document, and use the syntax shown for defining a method without using the generated proxy.)</span></span>

<span data-ttu-id="79020-276">Shoda názvu metody je velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="79020-276">Method name matching is case-insensitive.</span></span> <span data-ttu-id="79020-277">Například `Clients.All.addContosoChatMessageToPage` na serveru spustí `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, nebo `addcontosochatmessagetopage` na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="79020-277">For example, `Clients.All.addContosoChatMessageToPage` on the server will execute `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, or `addcontosochatmessagetopage` on the client.</span></span>

<span data-ttu-id="79020-278">**Definujte metodu na klientovi (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-278">**Define method on client (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample29.js?highlight=2)]

<span data-ttu-id="79020-279">**Alternativní způsob, jak definovat metodu na klientovi (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-279">**Alternate way to define method on client (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample30.js?highlight=1-2)]

<span data-ttu-id="79020-280">**Definujte metodu na klientovi (bez vygenerovaný proxy server, nebo při přidávání po volání metody start)**</span><span class="sxs-lookup"><span data-stu-id="79020-280">**Define method on client (without the generated proxy, or when adding after calling the start method)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample31.js?highlight=3)]

<span data-ttu-id="79020-281">**Kód serveru, který volá metodu klienta**</span><span class="sxs-lookup"><span data-stu-id="79020-281">**Server code that calls the client method**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample32.cs?highlight=5)]

<span data-ttu-id="79020-282">Následující příklady zahrnují komplexního objektu jako parametr metody.</span><span class="sxs-lookup"><span data-stu-id="79020-282">The following examples include a complex object as a method parameter.</span></span>

<span data-ttu-id="79020-283">**Definujte metodu na klienta, který používá komplexního objektu (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-283">**Define method on client that takes a complex object (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample33.js?highlight=2-3)]

<span data-ttu-id="79020-284">**Definujte metodu na klienta, který používá komplexního objektu (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-284">**Define method on client that takes a complex object (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample34.js?highlight=3-4)]

<span data-ttu-id="79020-285">**Kód serveru, který definuje komplexní objekt**</span><span class="sxs-lookup"><span data-stu-id="79020-285">**Server code that defines the complex object**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample35.cs?highlight=1)]

<span data-ttu-id="79020-286">**Kód serveru, který volá metodu klienta s použitím komplexní objekt**</span><span class="sxs-lookup"><span data-stu-id="79020-286">**Server code that calls the client method using a complex object**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample36.cs?highlight=3)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a><span data-ttu-id="79020-287">Volání metody serveru z klienta</span><span class="sxs-lookup"><span data-stu-id="79020-287">How to call server methods from the client</span></span>

<span data-ttu-id="79020-288">Chcete-li volat metodu serveru z klienta, použijte `server` vlastnost vygenerovaný proxy server nebo `invoke` metodu na proxy server rozbočovače, pokud nepoužíváte vygenerovaný proxy server.</span><span class="sxs-lookup"><span data-stu-id="79020-288">To call a server method from the client, use the `server` property of the generated proxy or the `invoke` method on the Hub proxy if you aren't using the generated proxy.</span></span> <span data-ttu-id="79020-289">Návratová hodnota nebo parametrů může být složité objekty.</span><span class="sxs-lookup"><span data-stu-id="79020-289">The return value or parameters can be complex objects.</span></span>

<span data-ttu-id="79020-290">Předat ve stylu camelCase verzi název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="79020-290">Pass in a camel-case version of the method name on the Hub.</span></span> <span data-ttu-id="79020-291">Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="79020-291">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="79020-292">Následující příklady ukazují, jak volat metodu serveru, který nemá návratovou hodnotu a volání metody serveru, který nemá návratovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="79020-292">The following examples show how to call a server method that doesn't have a return value and how to call a server method that does have a return value.</span></span>

<span data-ttu-id="79020-293">**Metoda serveru se žádný atribut HubMethodName**</span><span class="sxs-lookup"><span data-stu-id="79020-293">**Server method with no HubMethodName attribute**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample37.cs?highlight=3)]

<span data-ttu-id="79020-294">**Kód serveru, který definuje komplexní objekt předaný v parametru**</span><span class="sxs-lookup"><span data-stu-id="79020-294">**Server code that defines the complex object passed in a parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample38.cs)]

<span data-ttu-id="79020-295">**Klientský kód, který vyvolá metodu serveru (se vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-295">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample39.js?highlight=1)]

<span data-ttu-id="79020-296">**Klientský kód, který vyvolá metodu serveru (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-296">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample40.js?highlight=1)]

<span data-ttu-id="79020-297">Pokud upravena pomocí metody rozbočovače `HubMethodName` atribut, použijte tento název bez Změna velikosti písmen.</span><span class="sxs-lookup"><span data-stu-id="79020-297">If you decorated the Hub method with a `HubMethodName` attribute, use that name without changing case.</span></span>

<span data-ttu-id="79020-298">**Metoda serveru** s atributem HubMethodName</span><span class="sxs-lookup"><span data-stu-id="79020-298">**Server method** with a HubMethodName attribute</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample41.cs?highlight=3)]

<span data-ttu-id="79020-299">**Klientský kód, který vyvolá metodu serveru (se vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-299">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample42.js?highlight=1)]

<span data-ttu-id="79020-300">**Klientský kód, který vyvolá metodu serveru (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-300">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample43.js?highlight=1)]

<span data-ttu-id="79020-301">Předchozí příklady ukazují, jak volat metodu serveru, která nemá žádnou návratovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="79020-301">The preceding examples show how to call a server method that has no return value.</span></span> <span data-ttu-id="79020-302">Následující příklady znázorňují způsob volání metody serveru, který má návratovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="79020-302">The following examples show how to call a server method that has a return value.</span></span>

<span data-ttu-id="79020-303">**Serverový kód pro metodu, která nemá návratovou hodnotu**</span><span class="sxs-lookup"><span data-stu-id="79020-303">**Server code for a method that has a return value**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample44.cs?highlight=3)]

<span data-ttu-id="79020-304">**Třída akcie pro** návratová hodnota</span><span class="sxs-lookup"><span data-stu-id="79020-304">**The Stock class used for the** return value</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample45.cs?highlight=1)]

<span data-ttu-id="79020-305">**Klientský kód, který vyvolá metodu serveru (se vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-305">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample46.js?highlight=2,4-5)]

<span data-ttu-id="79020-306">**Klientský kód, který vyvolá metodu serveru (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-306">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample47.js?highlight=2,4-5)]

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a><span data-ttu-id="79020-307">Zpracování událostí doby platnosti</span><span class="sxs-lookup"><span data-stu-id="79020-307">How to handle connection lifetime events</span></span>

<span data-ttu-id="79020-308">Funkce SignalR poskytuje následující připojení události doby života, které dokáže zpracovat:</span><span class="sxs-lookup"><span data-stu-id="79020-308">SignalR provides the following connection lifetime events that you can handle:</span></span>

- <span data-ttu-id="79020-309">`starting`: Vyvoláno před odesláním žádná data přes dané připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-309">`starting`: Raised before any data is sent over the connection.</span></span>
- <span data-ttu-id="79020-310">`received`: Vyvoláno, když je veškerá data přijatá v připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-310">`received`: Raised when any data is received on the connection.</span></span> <span data-ttu-id="79020-311">Poskytuje přijatá data.</span><span class="sxs-lookup"><span data-stu-id="79020-311">Provides the received data.</span></span>
- <span data-ttu-id="79020-312">`connectionSlow`: Vyvoláno, když klient zjistí pomalý nebo často vyřazení připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-312">`connectionSlow`: Raised when the client detects a slow or frequently dropping connection.</span></span>
- <span data-ttu-id="79020-313">`reconnecting`: Vyvolá se při přenosu začne znovu obnovovat.</span><span class="sxs-lookup"><span data-stu-id="79020-313">`reconnecting`: Raised when the underlying transport begins reconnecting.</span></span>
- <span data-ttu-id="79020-314">`reconnected`: Vyvolá se při přenosu má připojen.</span><span class="sxs-lookup"><span data-stu-id="79020-314">`reconnected`: Raised when the underlying transport has reconnected.</span></span>
- <span data-ttu-id="79020-315">`stateChanged`: Vyvolá se při změně stavu připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-315">`stateChanged`: Raised when the connection state changes.</span></span> <span data-ttu-id="79020-316">Poskytuje starý stav a nový stav (připojování, připojeno, znovu připojíte nebo odpojeno).</span><span class="sxs-lookup"><span data-stu-id="79020-316">Provides the old state and the new state (Connecting, Connected, Reconnecting, or Disconnected).</span></span>
- <span data-ttu-id="79020-317">`disconnected`: Vyvolá se při připojení se odpojil.</span><span class="sxs-lookup"><span data-stu-id="79020-317">`disconnected`: Raised when the connection has disconnected.</span></span>

<span data-ttu-id="79020-318">Například, pokud chcete zobrazit upozornění, pokud existují problémy s připojením, které může způsobit znatelné zpoždění při, zpracovat `connectionSlow` událostí.</span><span class="sxs-lookup"><span data-stu-id="79020-318">For example, if you want to display warning messages when there are connection problems that might cause noticeable delays, handle the `connectionSlow` event.</span></span>

<span data-ttu-id="79020-319">**Zpracování události connectionSlow (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-319">**Handle the connectionSlow event (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample48.js?highlight=1)]

<span data-ttu-id="79020-320">**Zpracování události connectionSlow (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-320">**Handle the connectionSlow event (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample49.js?highlight=2)]

<span data-ttu-id="79020-321">Další informace najdete v tématu [principy a zpracování událostí doby platnosti v knihovně SignalR](index.md).</span><span class="sxs-lookup"><span data-stu-id="79020-321">For more information, see [Understanding and Handling Connection Lifetime Events in SignalR](index.md).</span></span>

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a><span data-ttu-id="79020-322">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="79020-322">How to handle errors</span></span>

<span data-ttu-id="79020-323">Poskytuje klientovi SignalR JavaScript `error` události, které můžete přidat obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="79020-323">The SignalR JavaScript client provides an `error` event that you can add a handler for.</span></span> <span data-ttu-id="79020-324">Také vám pomůže metodu selhání přidejte obslužnou rutinu chyby, které jsou výsledkem volání metody serveru.</span><span class="sxs-lookup"><span data-stu-id="79020-324">You can also use the fail method to add a handler for errors that result from a server method invocation.</span></span>

<span data-ttu-id="79020-325">Pokud není explicitně povolit podrobné chybové zprávy na serveru, obsahuje objekt výjimky, která vrací SignalR po chybě minimální informace o této chybě.</span><span class="sxs-lookup"><span data-stu-id="79020-325">If you don't explicitly enable detailed error messages on the server, the exception object that SignalR returns after an error contains minimal information about the error.</span></span> <span data-ttu-id="79020-326">Například, pokud je volání `newContosoChatMessage` selže, chybová zpráva v objektu chyba obsahuje "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" podrobné chybové zprávy pro klienty v produkčním prostředí se nedoporučuje z bezpečnostních důvodů, ale pokud chcete povolit podrobné chybové zprávy pro odesílání na serveru pro účely odstraňování potíží, použijte následující kód.</span><span class="sxs-lookup"><span data-stu-id="79020-326">For example, if a call to `newContosoChatMessage` fails, the error message in the error object contains "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Sending detailed error messages to clients in production is not recommended for security reasons, but if you want to enable detailed error messages for troubleshooting purposes, use the following code on the server.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample50.cs?highlight=2)]

<span data-ttu-id="79020-327">Následující příklad ukazuje, jak přidat obslužnou rutinu pro událost chyby.</span><span class="sxs-lookup"><span data-stu-id="79020-327">The following example shows how to add a handler for the error event.</span></span>

<span data-ttu-id="79020-328">**Přidat obslužnou rutinu chyby (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-328">**Add an error handler (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample51.js?highlight=1)]

<span data-ttu-id="79020-329">**Přidat obslužnou rutinu chyby (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-329">**Add an error handler (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample52.js?highlight=2)]

<span data-ttu-id="79020-330">Následující příklad ukazuje, jak zpracovat chybu z volání metody.</span><span class="sxs-lookup"><span data-stu-id="79020-330">The following example shows how to handle an error from a method invocation.</span></span>

<span data-ttu-id="79020-331">**Zpracování chyb z volání metody (s vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-331">**Handle an error from a method invocation (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample53.js?highlight=2)]

<span data-ttu-id="79020-332">**Zpracování chyb z volání metody (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-332">**Handle an error from a method invocation (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample54.js?highlight=2)]

<span data-ttu-id="79020-333">Pokud selže volání metody, `error` událost se vyvolá také, takže svůj kód v `error` metoda obslužné rutiny a `.fail` by provedení metody zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="79020-333">If a method invocation fails, the `error` event is also raised, so your code in the `error` method handler and in the `.fail` method callback would execute.</span></span>

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a><span data-ttu-id="79020-334">Jak povolit protokolování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="79020-334">How to enable client-side logging</span></span>

<span data-ttu-id="79020-335">Chcete-li povolit protokolování na straně klienta na připojení, nastavte `logging` vlastnost u objektu připojení před voláním `start` metoda k navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="79020-335">To enable client-side logging on a connection, set the `logging` property on the connection object before you call the `start` method to establish the connection.</span></span>

<span data-ttu-id="79020-336">**Povolit protokolování (pomocí vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-336">**Enable logging (with the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample55.js?highlight=1)]

<span data-ttu-id="79020-337">**Povolení protokolování (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="79020-337">**Enable logging (without the generated proxy)**</span></span>

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample56.js?highlight=2)]

<span data-ttu-id="79020-338">Pokud chcete zobrazit protokoly, otevřete vývojářské nástroje v prohlížeči a přejděte na kartu konzoly. Kurz, který zobrazuje podrobné pokyny a obrazovky snímky, které ukazují, jak to provést, najdete v tématu [serverové vysílání s knihovnou ASP.NET Signalr – povolit protokolování](index.md).</span><span class="sxs-lookup"><span data-stu-id="79020-338">To see the logs, open your browser's developer tools and go to the Console tab. For a tutorial that shows step-by-step instructions and screen shots that show how to do this, see [Server Broadcast with ASP.NET Signalr - Enable Logging](index.md).</span></span>
