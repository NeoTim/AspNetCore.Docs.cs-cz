---
uid: signalr/overview/older-versions/signalr-1x-hubs-api-guide-net-client
title: Funkce SignalR technologie ASP.NET pokyny k rozhraní API Center – klient .NET (SignalR 1.x) | Dokumentace Microsoftu
author: bradygaster
description: Tento dokument obsahuje úvod k používání rozhraní API rozbočovače pro funkci SignalR verze 2 v rozhraní .NET klientů, jako jsou Windows Store (WinRT), WPF, Silverlight a nevýhody...
ms.author: bradyg
ms.date: 04/17/2013
ms.assetid: c334adc3-d6dc-44f3-9f06-f7634475aad3
msc.legacyurl: /signalr/overview/older-versions/signalr-1x-hubs-api-guide-net-client
msc.type: authoredcontent
ms.openlocfilehash: dbf62b2f9851e3612885aa5375cd2c3432570643
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836193"
---
<a name="aspnet-signalr-hubs-api-guide---net-client-signalr-1x"></a><span data-ttu-id="a56fb-103">Funkce SignalR technologie ASP.NET pokyny k rozhraní API Center – klient .NET (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="a56fb-103">ASP.NET SignalR Hubs API Guide - .NET Client (SignalR 1.x)</span></span>
====================
<span data-ttu-id="a56fb-104">podle [Patrick Fletcher](https://github.com/pfletcher), [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="a56fb-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="a56fb-105">Tento dokument obsahuje úvod k používání rozhraní API rozbočovače pro funkci SignalR verze 2 v rozhraní .NET klientů, jako jsou Windows Store (WinRT), WPF, Silverlight a konzolové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a56fb-105">This document provides an introduction to using the Hubs API for SignalR version 2 in .NET clients, such as Windows Store (WinRT), WPF, Silverlight, and console applications.</span></span>
> 
> <span data-ttu-id="a56fb-106">Rozhraní API pro rozbočovače SignalR umožňuje vytvářet vzdálených volání procedur (RPC) ze serveru pro připojené klienty a z klientů k serveru.</span><span class="sxs-lookup"><span data-stu-id="a56fb-106">The SignalR Hubs API enables you to make remote procedure calls (RPCs) from a server to connected clients and from clients to the server.</span></span> <span data-ttu-id="a56fb-107">V serverovém kódu můžete definovat metody, které mohou být volány klientů a volat metody, které běží na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a56fb-107">In server code, you define methods that can be called by clients, and you call methods that run on the client.</span></span> <span data-ttu-id="a56fb-108">V klientském kódu můžete definovat metody, které lze volat ze serveru a volání metody, které běží na serveru.</span><span class="sxs-lookup"><span data-stu-id="a56fb-108">In client code, you define methods that can be called from the server, and you call methods that run on the server.</span></span> <span data-ttu-id="a56fb-109">Funkce SignalR postará za vás zajistí funkčnost systému klient server.</span><span class="sxs-lookup"><span data-stu-id="a56fb-109">SignalR takes care of all of the client-to-server plumbing for you.</span></span>
> 
> <span data-ttu-id="a56fb-110">Funkce SignalR také nabízí nižší úrovně rozhraní API volá trvalé připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-110">SignalR also offers a lower-level API called Persistent Connections.</span></span> <span data-ttu-id="a56fb-111">Úvod do SignalR, rozbočovačů a trvalá připojení, nebo kurz, který ukazuje, jak sestavit kompletní aplikace SignalR, přečtěte si téma [SignalR – Začínáme](../getting-started/index.md).</span><span class="sxs-lookup"><span data-stu-id="a56fb-111">For an introduction to SignalR, Hubs, and Persistent Connections, or for a tutorial that shows how to build a complete SignalR application, see [SignalR - Getting Started](../getting-started/index.md).</span></span>


## <a name="overview"></a><span data-ttu-id="a56fb-112">Přehled</span><span class="sxs-lookup"><span data-stu-id="a56fb-112">Overview</span></span>

<span data-ttu-id="a56fb-113">Tento dokument obsahuje následující části:</span><span class="sxs-lookup"><span data-stu-id="a56fb-113">This document contains the following sections:</span></span>

- [<span data-ttu-id="a56fb-114">Instalace klienta</span><span class="sxs-lookup"><span data-stu-id="a56fb-114">Client Setup</span></span>](#clientsetup)
- [<span data-ttu-id="a56fb-115">Postup vytvoření připojení</span><span class="sxs-lookup"><span data-stu-id="a56fb-115">How to establish a connection</span></span>](#establishconnection)

    - [<span data-ttu-id="a56fb-116">Připojení mezi doménami z klienty prostředí Silverlight</span><span class="sxs-lookup"><span data-stu-id="a56fb-116">Cross-domain connections from Silverlight clients</span></span>](#slcrossdomain)
- [<span data-ttu-id="a56fb-117">Postup konfigurace připojení</span><span class="sxs-lookup"><span data-stu-id="a56fb-117">How to configure the connection</span></span>](#configureconnection)

    - [<span data-ttu-id="a56fb-118">Jak nastavit maximální počet souběžných připojení v klientech WPF</span><span class="sxs-lookup"><span data-stu-id="a56fb-118">How to set the maximum number of concurrent connections in WPF clients</span></span>](#maxconnections)
    - [<span data-ttu-id="a56fb-119">Určení parametrů řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="a56fb-119">How to specify query string parameters</span></span>](#querystring)
    - [<span data-ttu-id="a56fb-120">Jak určit metodu přenosu</span><span class="sxs-lookup"><span data-stu-id="a56fb-120">How to specify the transport method</span></span>](#transport)
    - [<span data-ttu-id="a56fb-121">Jak zadat hlavičky protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="a56fb-121">How to specify HTTP headers</span></span>](#httpheaders)
    - [<span data-ttu-id="a56fb-122">Určení klientských certifikátů</span><span class="sxs-lookup"><span data-stu-id="a56fb-122">How to specify client certificates</span></span>](#clientcertificate)
- [<span data-ttu-id="a56fb-123">Jak vytvořit proxy server rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a56fb-123">How to create the Hub proxy</span></span>](#proxy)
- [<span data-ttu-id="a56fb-124">Definování metody na straně klienta, která může volat na serveru</span><span class="sxs-lookup"><span data-stu-id="a56fb-124">How to define methods on the client that the server can call</span></span>](#callclient)

    - [<span data-ttu-id="a56fb-125">Metody bez parametrů</span><span class="sxs-lookup"><span data-stu-id="a56fb-125">Methods without parameters</span></span>](#clientmethodswithoutparms)
    - [<span data-ttu-id="a56fb-126">Metody s parametry, určující typy parametrů</span><span class="sxs-lookup"><span data-stu-id="a56fb-126">Methods with parameters, specifying parameter types</span></span>](#clientmethodswithparmtypes)
    - [<span data-ttu-id="a56fb-127">Metody s parametry, dynamických objektů pro parametry</span><span class="sxs-lookup"><span data-stu-id="a56fb-127">Methods with parameters, specifying dynamic objects for the parameters</span></span>](#clientmethodswithdynamparms)
    - [<span data-ttu-id="a56fb-128">Jak odebrat obslužnou rutinu</span><span class="sxs-lookup"><span data-stu-id="a56fb-128">How to remove a handler</span></span>](#removehandler)
- [<span data-ttu-id="a56fb-129">Volání metody serveru z klienta</span><span class="sxs-lookup"><span data-stu-id="a56fb-129">How to call server methods from the client</span></span>](#callserver)
- [<span data-ttu-id="a56fb-130">Zpracování událostí doby platnosti</span><span class="sxs-lookup"><span data-stu-id="a56fb-130">How to handle connection lifetime events</span></span>](#connectionlifetime)
- [<span data-ttu-id="a56fb-131">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="a56fb-131">How to handle errors</span></span>](#handleerrors)
- [<span data-ttu-id="a56fb-132">Jak povolit protokolování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="a56fb-132">How to enable client-side logging</span></span>](#logging)
- [<span data-ttu-id="a56fb-133">WPF, Silverlight a Konzolová aplikace ukázky kódu pro metody klienta, které můžete volat na serveru</span><span class="sxs-lookup"><span data-stu-id="a56fb-133">WPF, Silverlight, and console application code samples for client methods that the server can call</span></span>](#wpfsl)

<span data-ttu-id="a56fb-134">Ukázka .NET klientské projekty naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="a56fb-134">For a sample .NET client projects, see the following resources:</span></span>

- <span data-ttu-id="a56fb-135">[gustavo armenta / SignalR-Samples](https://github.com/gustavo-armenta/SignalR-Samples) na webu GitHub.com (příklady WinRT, Silverlight, konzoly aplikace).</span><span class="sxs-lookup"><span data-stu-id="a56fb-135">[gustavo-armenta / SignalR-Samples](https://github.com/gustavo-armenta/SignalR-Samples) on GitHub.com (WinRT, Silverlight, console app examples).</span></span>
- <span data-ttu-id="a56fb-136">[DamianEdwards / SignalR MoveShapeDemo / MoveShape.Desktop](https://github.com/DamianEdwards/SignalR-MoveShapeDemo/tree/master/MoveShape/MoveShape.Desktop) na webu GitHub.com (např. WPF).</span><span class="sxs-lookup"><span data-stu-id="a56fb-136">[DamianEdwards / SignalR-MoveShapeDemo / MoveShape.Desktop](https://github.com/DamianEdwards/SignalR-MoveShapeDemo/tree/master/MoveShape/MoveShape.Desktop) on GitHub.com (WPF example).</span></span>
- <span data-ttu-id="a56fb-137">[Funkce SignalR / Microsoft.AspNet.SignalR.Client.Samples](https://github.com/SignalR/SignalR/tree/master/samples/Microsoft.AspNet.SignalR.Client.Samples) na webu GitHub.com (např. aplikace konzoly).</span><span class="sxs-lookup"><span data-stu-id="a56fb-137">[SignalR / Microsoft.AspNet.SignalR.Client.Samples](https://github.com/SignalR/SignalR/tree/master/samples/Microsoft.AspNet.SignalR.Client.Samples) on GitHub.com (Console app example).</span></span>

<span data-ttu-id="a56fb-138">Dokumentace o tom, jak program na serveru nebo klientů JavaScript naleznete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="a56fb-138">For documentation on how to program the server or JavaScript clients, see the following resources:</span></span>

- [<span data-ttu-id="a56fb-139">Pokyny k rozhraní API Center SignalR – Server</span><span class="sxs-lookup"><span data-stu-id="a56fb-139">SignalR Hubs API Guide - Server</span></span>](../guide-to-the-api/hubs-api-guide-server.md)
- [<span data-ttu-id="a56fb-140">Pokyny k rozhraní API Center SignalR – javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="a56fb-140">SignalR Hubs API Guide - JavaScript Client</span></span>](../guide-to-the-api/hubs-api-guide-javascript-client.md)

<span data-ttu-id="a56fb-141">Odkazy na témata, Reference k rozhraní API se API verze rozhraní .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="a56fb-141">Links to API Reference topics are to the .NET 4.5 version of the API.</span></span> <span data-ttu-id="a56fb-142">Pokud používáte .NET 4, přečtěte si téma [verze .NET 4 témat API](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).</span><span class="sxs-lookup"><span data-stu-id="a56fb-142">If you're using .NET 4, see [the .NET 4 version of the API topics](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).</span></span>

<a id="clientsetup"></a>

## <a name="client-setup"></a><span data-ttu-id="a56fb-143">Instalace klienta</span><span class="sxs-lookup"><span data-stu-id="a56fb-143">Client setup</span></span>

<span data-ttu-id="a56fb-144">Nainstalujte [Microsoft.AspNet.SignalR.Client](http://nuget.org/packages/Microsoft.AspNet.SignalR.Client) balíčku NuGet (ne [Microsoft.AspNet.SignalR](http://nuget.org/packages/microsoft.aspnet.signalr) balíček).</span><span class="sxs-lookup"><span data-stu-id="a56fb-144">Install the [Microsoft.AspNet.SignalR.Client](http://nuget.org/packages/Microsoft.AspNet.SignalR.Client) NuGet package (not the [Microsoft.AspNet.SignalR](http://nuget.org/packages/microsoft.aspnet.signalr) package).</span></span> <span data-ttu-id="a56fb-145">Tento balíček podporuje WinRT, Silverlight, WPF, konzolovou aplikaci a klienti Windows Phone, .NET 4 a .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="a56fb-145">This package supports WinRT, Silverlight, WPF, console application, and Windows Phone clients, for both .NET 4 and .NET 4.5.</span></span>

<span data-ttu-id="a56fb-146">Pokud verze funkce signalr, která máte na straně klienta se liší od verze, které máte na serveru, SignalR často je možné přizpůsobit pro rozdíl.</span><span class="sxs-lookup"><span data-stu-id="a56fb-146">If the version of SignalR that you have on the client is different from the version that you have on the server, SignalR is often able to adapt to the difference.</span></span> <span data-ttu-id="a56fb-147">Například při vydání SignalR verze 2.0 a, který nainstalujete na server, server bude podporovat klienty, kteří mají 1.1.x, stejně jako klienti, kteří mají nainstalovaný 2.0 nainstalované.</span><span class="sxs-lookup"><span data-stu-id="a56fb-147">For example, when SignalR version 2.0 is released and you install that on the server, the server will support clients that have 1.1.x installed as well as clients that have 2.0 installed.</span></span> <span data-ttu-id="a56fb-148">Pokud je příliš velký rozdíl mezi verzí na serveru a verze na klientovi, vyvolá funkce SignalR `InvalidOperationException` výjimky, když se klient pokusí navázat připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-148">If the difference between the version on the server and the version on the client is too great, SignalR throws an `InvalidOperationException` exception when the client tries to establish a connection.</span></span> <span data-ttu-id="a56fb-149">Chybová zpráva "`You are using a version of the client that isn't compatible with the server. Client version X.X, server version X.X`".</span><span class="sxs-lookup"><span data-stu-id="a56fb-149">The error message is "`You are using a version of the client that isn't compatible with the server. Client version X.X, server version X.X`".</span></span>

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a><span data-ttu-id="a56fb-150">Postup vytvoření připojení</span><span class="sxs-lookup"><span data-stu-id="a56fb-150">How to establish a connection</span></span>

<span data-ttu-id="a56fb-151">Předtím, než můžete navázat spojení, je nutné vytvořit `HubConnection` objektů a vytvořit proxy.</span><span class="sxs-lookup"><span data-stu-id="a56fb-151">Before you can establish a connection, you have to create a `HubConnection` object and create a proxy.</span></span> <span data-ttu-id="a56fb-152">K navázání připojení, zavolejte `Start` metodu `HubConnection` objektu.</span><span class="sxs-lookup"><span data-stu-id="a56fb-152">To establish the connection, call the `Start` method on the `HubConnection` object.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample1.cs?highlight=1,4)]

> [!NOTE]
> <span data-ttu-id="a56fb-153">Pro klienty jazyka JavaScript, je nutné provést registraci aspoň jednu obslužnou rutinu události před voláním `Start` metoda k navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-153">For JavaScript clients you have to register at least one event handler before calling the `Start` method to establish the connection.</span></span> <span data-ttu-id="a56fb-154">To není nutné pro klienty .NET.</span><span class="sxs-lookup"><span data-stu-id="a56fb-154">This is not necessary for .NET clients.</span></span> <span data-ttu-id="a56fb-155">Pro klienty jazyka JavaScript, vygenerovaném kódu proxy automaticky vytvoří proxy pro všechna centra, které existují na serveru a registraci obslužné rutiny je způsob, jak naznačit které rozbočovače klienta chce využít.</span><span class="sxs-lookup"><span data-stu-id="a56fb-155">For JavaScript clients, the generated proxy code automatically creates proxies for all Hubs that exist on the server, and registering a handler is how you indicate which Hubs your client intends to use.</span></span> <span data-ttu-id="a56fb-156">Ale pro klienta .NET vytvořit proxy servery Hub ručně, tak SignalR předpokládá, že budete používat libovolné centrum, kterou vytvoříte pro proxy server.</span><span class="sxs-lookup"><span data-stu-id="a56fb-156">But for a .NET client you create Hub proxies manually, so SignalR assumes that you will be using any Hub that you create a proxy for.</span></span>


<span data-ttu-id="a56fb-157">Vzorový kód používá výchozí "/ signalr" adresa URL k připojení do služby SignalR.</span><span class="sxs-lookup"><span data-stu-id="a56fb-157">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="a56fb-158">Informace o tom, jak určit různé základní adresu URL najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - /signalr URL](../guide-to-the-api/hubs-api-guide-server.md#signalrurl).</span><span class="sxs-lookup"><span data-stu-id="a56fb-158">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](../guide-to-the-api/hubs-api-guide-server.md#signalrurl).</span></span>

<span data-ttu-id="a56fb-159">`Start` Metoda provedena asynchronně.</span><span class="sxs-lookup"><span data-stu-id="a56fb-159">The `Start` method executes asynchronously.</span></span> <span data-ttu-id="a56fb-160">Ujistěte se, že následující řádky kódu není můžete spustit až po vytvoření připojení, použijte `await` v technologii ASP.NET 4.5 asynchronní metodu nebo `.Wait()` v synchronní metodě.</span><span class="sxs-lookup"><span data-stu-id="a56fb-160">To make sure that subsequent lines of code don't execute until after the connection is established, use `await` in an ASP.NET 4.5 asynchronous method or `.Wait()` in a synchronous method.</span></span> <span data-ttu-id="a56fb-161">Nepoužívejte `.Wait()` v klientovi WinRT.</span><span class="sxs-lookup"><span data-stu-id="a56fb-161">Don't use `.Wait()` in a WinRT client.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample2.cs?highlight=1)]

[!code-css[Main](signalr-1x-hubs-api-guide-net-client/samples/sample3.css?highlight=1)]

<span data-ttu-id="a56fb-162">`HubConnection` Třída je bezpečná pro vlákno.</span><span class="sxs-lookup"><span data-stu-id="a56fb-162">The `HubConnection` class is thread-safe.</span></span>

<a id="slcrossdomain"></a>

### <a name="cross-domain-connections-from-silverlight-clients"></a><span data-ttu-id="a56fb-163">Připojení mezi doménami z klienty prostředí Silverlight</span><span class="sxs-lookup"><span data-stu-id="a56fb-163">Cross-domain connections from Silverlight clients</span></span>

<span data-ttu-id="a56fb-164">Informace o tom, jak povolit připojení mezi doménami z klienty prostředí Silverlight naleznete v tématu [zpřístupnění služby k dispozici napříč hranicemi domén](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).</span><span class="sxs-lookup"><span data-stu-id="a56fb-164">For information about how to enable cross-domain connections from Silverlight clients, see [Making a Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).</span></span>

<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a><span data-ttu-id="a56fb-165">Postup konfigurace připojení</span><span class="sxs-lookup"><span data-stu-id="a56fb-165">How to configure the connection</span></span>

<span data-ttu-id="a56fb-166">Než vytvoříte připojení, můžete určit kterékoli z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="a56fb-166">Before you establish a connection, you can specify any of the following options:</span></span>

- <span data-ttu-id="a56fb-167">Limit souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-167">Concurrent connections limit.</span></span>
- <span data-ttu-id="a56fb-168">Parametry řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="a56fb-168">Query string parameters.</span></span>
- <span data-ttu-id="a56fb-169">Metoda přenosu.</span><span class="sxs-lookup"><span data-stu-id="a56fb-169">The transport method.</span></span>
- <span data-ttu-id="a56fb-170">Hlavičky protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a56fb-170">HTTP headers.</span></span>
- <span data-ttu-id="a56fb-171">Klientské certifikáty.</span><span class="sxs-lookup"><span data-stu-id="a56fb-171">Client certificates.</span></span>

<a id="maxconnections"></a>

### <a name="how-to-set-the-maximum-number-of-concurrent-connections-in-wpf-clients"></a><span data-ttu-id="a56fb-172">Jak nastavit maximální počet souběžných připojení v klientech WPF</span><span class="sxs-lookup"><span data-stu-id="a56fb-172">How to set the maximum number of concurrent connections in WPF clients</span></span>

<span data-ttu-id="a56fb-173">V WPF klienty bude pravděpodobně zvýšit maximální počet souběžných připojení z jeho výchozí hodnotu 2.</span><span class="sxs-lookup"><span data-stu-id="a56fb-173">In WPF clients, you might have to increase the maximum number of concurrent connections from its default value of 2.</span></span> <span data-ttu-id="a56fb-174">Doporučená hodnota je 10.</span><span class="sxs-lookup"><span data-stu-id="a56fb-174">The recommended value is 10.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample4.cs?highlight=4)]

<span data-ttu-id="a56fb-175">Další informace najdete v tématu [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx).</span><span class="sxs-lookup"><span data-stu-id="a56fb-175">For more information, see [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx).</span></span>

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a><span data-ttu-id="a56fb-176">Určení parametrů řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="a56fb-176">How to specify query string parameters</span></span>

<span data-ttu-id="a56fb-177">Pokud chcete odesílat data do serveru, když se klient připojí, můžete přidat parametry řetězce dotazu do objekt připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-177">If you want to send data to the server when the client connects, you can add query string parameters to the connection object.</span></span> <span data-ttu-id="a56fb-178">Následující příklad ukazuje, jak nastavit parametr řetězce dotazu v klientském kódu.</span><span class="sxs-lookup"><span data-stu-id="a56fb-178">The following example shows how to set a query string parameter in client code.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample5.cs)]

<span data-ttu-id="a56fb-179">Následující příklad znázorňuje způsob čtení parametru řetězce dotazu v serverovém kódu.</span><span class="sxs-lookup"><span data-stu-id="a56fb-179">The following example shows how to read a query string parameter in server code.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample6.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a><span data-ttu-id="a56fb-180">Jak určit metodu přenosu</span><span class="sxs-lookup"><span data-stu-id="a56fb-180">How to specify the transport method</span></span>

<span data-ttu-id="a56fb-181">Jako součást procesu připojování klienta SignalR obvykle vyjedná se serverem a určit nejlepší přenos, který je podporovaný server i klient.</span><span class="sxs-lookup"><span data-stu-id="a56fb-181">As part of the process of connecting, a SignalR client normally negotiates with the server to determine the best transport that is supported by both server and client.</span></span> <span data-ttu-id="a56fb-182">Pokud již víte, jaké přenosu, kterou chcete použít, můžete obejít tento proces vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="a56fb-182">If you already know which transport you want to use, you can bypass this negotiation process.</span></span> <span data-ttu-id="a56fb-183">K určení metodu přenosu, předejte objekt transport metodě Start.</span><span class="sxs-lookup"><span data-stu-id="a56fb-183">To specify the transport method, pass in a transport object to the Start method.</span></span> <span data-ttu-id="a56fb-184">Následující příklad ukazuje, jak zadat metodu přenosu v klientském kódu.</span><span class="sxs-lookup"><span data-stu-id="a56fb-184">The following example shows how to specify the transport method in client code.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample7.cs?highlight=4)]

<span data-ttu-id="a56fb-185">[Microsoft.AspNet.SignalR.Client.Transports](https://msdn.microsoft.com/library/jj918090(v=vs.111).aspx) obor názvů obsahuje následující třídy, které můžete použít k určení přenos.</span><span class="sxs-lookup"><span data-stu-id="a56fb-185">The [Microsoft.AspNet.SignalR.Client.Transports](https://msdn.microsoft.com/library/jj918090(v=vs.111).aspx) namespace includes the following classes that you can use to specify the transport.</span></span>

- <span data-ttu-id="a56fb-186">[LongPollingTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.longpollingtransport(v=vs.111).aspx)</span><span class="sxs-lookup"><span data-stu-id="a56fb-186">[LongPollingTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.longpollingtransport(v=vs.111).aspx)</span></span>
- <span data-ttu-id="a56fb-187">[ServerSentEventsTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.serversenteventstransport(v=vs.111).aspx)</span><span class="sxs-lookup"><span data-stu-id="a56fb-187">[ServerSentEventsTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.serversenteventstransport(v=vs.111).aspx)</span></span>
- <span data-ttu-id="a56fb-188">[WebSocketTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.websockettransport(v=vs.111).aspx) (dostupné pouze v případě serverů i klientů pomocí rozhraní .NET 4.5.)</span><span class="sxs-lookup"><span data-stu-id="a56fb-188">[WebSocketTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.websockettransport(v=vs.111).aspx) (Available only when both server and client use .NET 4.5.)</span></span>
- <span data-ttu-id="a56fb-189">[AutoTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.autotransport(v=vs.111).aspx) (automaticky vybere nejlepší přenos, který podporuje klient i server.</span><span class="sxs-lookup"><span data-stu-id="a56fb-189">[AutoTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.autotransport(v=vs.111).aspx) (Automatically chooses the best transport that is supported by both the client and the server.</span></span> <span data-ttu-id="a56fb-190">Toto je výchozí přenos.</span><span class="sxs-lookup"><span data-stu-id="a56fb-190">This is the default transport.</span></span> <span data-ttu-id="a56fb-191">Tuto hodnotu na předání `Start` metoda má stejný účinek jako ne vyhovující co.)</span><span class="sxs-lookup"><span data-stu-id="a56fb-191">Passing this in to the `Start` method has the same effect as not passing in anything.)</span></span>

<span data-ttu-id="a56fb-192">Přenos ForeverFrame není zahrnuta v tomto seznamu, protože se používá pouze pomocí prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a56fb-192">The ForeverFrame transport is not included in this list because it is used only by browsers.</span></span>

<span data-ttu-id="a56fb-193">Informace o tom, jak zkontrolovat metodu přenosu v serverovém kódu najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - jak získat informace o klientovi z kontextové vlastnosti](../guide-to-the-api/hubs-api-guide-server.md#contextproperty).</span><span class="sxs-lookup"><span data-stu-id="a56fb-193">For information about how to check the transport method in server code, see [ASP.NET SignalR Hubs API Guide - Server - How to get information about the client from the Context property](../guide-to-the-api/hubs-api-guide-server.md#contextproperty).</span></span> <span data-ttu-id="a56fb-194">Další informace o přenosy a náhrad najdete v tématu [Úvod ke knihovně SignalR – přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="a56fb-194">For more information about transports and fallbacks, see [Introduction to SignalR - Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="httpheaders"></a>

### <a name="how-to-specify-http-headers"></a><span data-ttu-id="a56fb-195">Jak zadat hlavičky protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="a56fb-195">How to specify HTTP headers</span></span>

<span data-ttu-id="a56fb-196">K nastavení hlavičky protokolu HTTP, použijte `Headers` vlastnost na objekt připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-196">To set HTTP headers, use the `Headers` property on the connection object.</span></span> <span data-ttu-id="a56fb-197">Následující příklad ukazuje, jak přidat hlavičku protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a56fb-197">The following example shows how to add an HTTP header.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample8.cs?highlight=2)]

<a id="clientcertificate"></a>

### <a name="how-to-specify-client-certificates"></a><span data-ttu-id="a56fb-198">Určení klientských certifikátů</span><span class="sxs-lookup"><span data-stu-id="a56fb-198">How to specify client certificates</span></span>

<span data-ttu-id="a56fb-199">Chcete-li přidat klientské certifikáty, použijte `AddClientCertificate` metodu na objekt připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-199">To add client certificates, use the `AddClientCertificate` method on the connection object.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample9.cs?highlight=2)]

<a id="proxy"></a>

## <a name="how-to-create-the-hub-proxy"></a><span data-ttu-id="a56fb-200">Jak vytvořit proxy server rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a56fb-200">How to create the Hub proxy</span></span>

<span data-ttu-id="a56fb-201">Aby bylo možné definovat metody na straně klienta, která centrum může volat ze serveru a volání metod rozbočovače na serveru, vytvořit proxy server rozbočovače voláním `CreateHubProxy` na objekt připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-201">In order to define methods on the client that a Hub can call from the server, and to invoke methods on a Hub at the server, create a proxy for the Hub by calling `CreateHubProxy` on the connection object.</span></span> <span data-ttu-id="a56fb-202">Řetězec předáním `CreateHubProxy` je název třídy rozbočovače nebo název určený `HubName` atribut, pokud bylo použito na serveru.</span><span class="sxs-lookup"><span data-stu-id="a56fb-202">The string you pass in to `CreateHubProxy` is the name of your Hub class, or the name specified by the `HubName` attribute if one was used on the server.</span></span> <span data-ttu-id="a56fb-203">Shoda názvů nerozlišuje.</span><span class="sxs-lookup"><span data-stu-id="a56fb-203">Name matching is case-insensitive.</span></span>

<span data-ttu-id="a56fb-204">**Třída rozbočovače na serveru**</span><span class="sxs-lookup"><span data-stu-id="a56fb-204">**Hub class on server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample10.cs?highlight=1)]

<span data-ttu-id="a56fb-205">**Vytvořit proxy klienta pro rozbočovač třídu**</span><span class="sxs-lookup"><span data-stu-id="a56fb-205">**Create client proxy for the Hub class**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample11.cs?highlight=2)]

<span data-ttu-id="a56fb-206">Pokud uspořádání vaší třídy centra s `HubName` atribut, použijte tento název.</span><span class="sxs-lookup"><span data-stu-id="a56fb-206">If you decorate your Hub class with a `HubName` attribute, use that name.</span></span>

<span data-ttu-id="a56fb-207">**Třída rozbočovače na serveru**</span><span class="sxs-lookup"><span data-stu-id="a56fb-207">**Hub class on server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample12.cs)]

<span data-ttu-id="a56fb-208">**Vytvořit proxy klienta pro rozbočovač třídu**</span><span class="sxs-lookup"><span data-stu-id="a56fb-208">**Create client proxy for the Hub class**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample13.cs?highlight=2)]

<span data-ttu-id="a56fb-209">Objekt proxy je bezpečná pro vlákno.</span><span class="sxs-lookup"><span data-stu-id="a56fb-209">The proxy object is thread-safe.</span></span> <span data-ttu-id="a56fb-210">Ve skutečnosti, pokud zavoláte `HubConnection.CreateHubProxy` víckrát se stejným `hubName`, získáte stejné mezipaměti `IHubProxy` objektu.</span><span class="sxs-lookup"><span data-stu-id="a56fb-210">In fact, if you call `HubConnection.CreateHubProxy` multiple times with the same `hubName`, you get the same cached `IHubProxy` object.</span></span>

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a><span data-ttu-id="a56fb-211">Definování metody na straně klienta, která může volat na serveru</span><span class="sxs-lookup"><span data-stu-id="a56fb-211">How to define methods on the client that the server can call</span></span>

<span data-ttu-id="a56fb-212">Chcete-li definovat metodu, která může volat na serveru, používat proxy server, na `On` metody pro registraci obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="a56fb-212">To define a method that the server can call, use the proxy's `On` method to register an event handler.</span></span>

<span data-ttu-id="a56fb-213">Shoda názvu metody je velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="a56fb-213">Method name matching is case-insensitive.</span></span> <span data-ttu-id="a56fb-214">Například `Clients.All.UpdateStockPrice` na serveru spustí `updateStockPrice`, `updatestockprice`, nebo `UpdateStockPrice` na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a56fb-214">For example, `Clients.All.UpdateStockPrice` on the server will execute `updateStockPrice`, `updatestockprice`, or `UpdateStockPrice` on the client.</span></span>

<span data-ttu-id="a56fb-215">Různé klientské platformy mají různé požadavky na jak psát kód, metoda pro aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a56fb-215">Different client platforms have different requirements for how you write method code to update the UI.</span></span> <span data-ttu-id="a56fb-216">Příklady uvedené jsou pro klienty WinRT (Windows Store .NET).</span><span class="sxs-lookup"><span data-stu-id="a56fb-216">The examples shown are for WinRT (Windows Store .NET) clients.</span></span> <span data-ttu-id="a56fb-217">WPF, Silverlight a příklady aplikací konzoly jsou k dispozici v [samostatné části dále v tomto tématu](#wpfsl).</span><span class="sxs-lookup"><span data-stu-id="a56fb-217">WPF, Silverlight, and console application examples are provided in [a separate section later in this topic](#wpfsl).</span></span>

<a id="clientmethodswithoutparms"></a>

### <a name="methods-without-parameters"></a><span data-ttu-id="a56fb-218">Metody bez parametrů</span><span class="sxs-lookup"><span data-stu-id="a56fb-218">Methods without parameters</span></span>

<span data-ttu-id="a56fb-219">Pokud metoda, že zpracování nemá žádné parametry, použijte přetížení obecné `On` metody:</span><span class="sxs-lookup"><span data-stu-id="a56fb-219">If the method you're handling does not have parameters, use the non-generic overload of the `On` method:</span></span>

<span data-ttu-id="a56fb-220">**Volání metody bez parametrů kódu serveru**</span><span class="sxs-lookup"><span data-stu-id="a56fb-220">**Server code calling client method without parameters**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample14.cs?highlight=5)]

<span data-ttu-id="a56fb-221">**WinRT klientský kód pro metodu volat ze serveru bez parametrů ([WPF a Silverlight příklady dále v tomto tématu](#wpfsl))**</span><span class="sxs-lookup"><span data-stu-id="a56fb-221">**WinRT Client code for method called from server without parameters ([see WPF and Silverlight examples later in this topic](#wpfsl))**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample15.cs)]

<a id="clientmethodswithparmtypes"></a>

### <a name="methods-with-parameters-specifying-the-parameter-types"></a><span data-ttu-id="a56fb-222">Metody s parametry, určující typy parametrů</span><span class="sxs-lookup"><span data-stu-id="a56fb-222">Methods with parameters, specifying the parameter types</span></span>

<span data-ttu-id="a56fb-223">Pokud jste zpracování metody obsahuje parametry, zadejte typy parametrů jako obecné typy `On` metody.</span><span class="sxs-lookup"><span data-stu-id="a56fb-223">If the method you're handling has parameters, specify the types of the parameters as the generic types of the `On` method.</span></span> <span data-ttu-id="a56fb-224">Existují přetížení obecné `On` metoda vám umožní určit až na 8 parametry (4 ve Windows Phone 7).</span><span class="sxs-lookup"><span data-stu-id="a56fb-224">There are generic overloads of the `On` method to enable you to specify up to 8 parameters (4 on Windows Phone 7).</span></span> <span data-ttu-id="a56fb-225">V následujícím příkladu je jeden parametr odeslán `UpdateStockPrice` metody.</span><span class="sxs-lookup"><span data-stu-id="a56fb-225">In the following example, one parameter is sent to the `UpdateStockPrice` method.</span></span>

<span data-ttu-id="a56fb-226">**Volání metody s parametrem kódu serveru**</span><span class="sxs-lookup"><span data-stu-id="a56fb-226">**Server code calling client method with a parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample16.cs?highlight=3)]

<span data-ttu-id="a56fb-227">**Třída akcie použitý pro parametr**</span><span class="sxs-lookup"><span data-stu-id="a56fb-227">**The Stock class used for the parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample17.cs)]

<span data-ttu-id="a56fb-228">**WinRT klientský kód pro metodu volat ze serveru s parametrem ([WPF a Silverlight příklady dále v tomto tématu](#wpfsl))**</span><span class="sxs-lookup"><span data-stu-id="a56fb-228">**WinRT Client code for a method called from server with a parameter ([see WPF and Silverlight examples later in this topic](#wpfsl))**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample18.cs?highlight=1,5)]

<a id="clientmethodswithdynamparms"></a>

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a><span data-ttu-id="a56fb-229">Metody s parametry, dynamických objektů pro parametry</span><span class="sxs-lookup"><span data-stu-id="a56fb-229">Methods with parameters, specifying dynamic objects for the parameters</span></span>

<span data-ttu-id="a56fb-230">Jako alternativu k zadání parametrů jako obecné typy `On` metodu, můžete zadat parametry jako dynamické objekty:</span><span class="sxs-lookup"><span data-stu-id="a56fb-230">As an alternative to specifying parameters as generic types of the `On` method, you can specify parameters as dynamic objects:</span></span>

<span data-ttu-id="a56fb-231">**Volání metody s parametrem kódu serveru**</span><span class="sxs-lookup"><span data-stu-id="a56fb-231">**Server code calling client method with a parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample19.cs?highlight=3)]

<span data-ttu-id="a56fb-232">**Třída akcie použitý pro parametr**</span><span class="sxs-lookup"><span data-stu-id="a56fb-232">**The Stock class used for the parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample20.cs)]

<span data-ttu-id="a56fb-233">**WinRT klientský kód pro metodu volat ze serveru s parametrem, pomocí dynamického objektu pro parametr ([WPF a Silverlight příklady dále v tomto tématu](#wpfsl))**</span><span class="sxs-lookup"><span data-stu-id="a56fb-233">**WinRT Client code for a method called from server with a parameter, using a dynamic object for the parameter ([see WPF and Silverlight examples later in this topic](#wpfsl))**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample21.cs?highlight=1,5)]

<a id="removehandler"></a>

### <a name="how-to-remove-a-handler"></a><span data-ttu-id="a56fb-234">Jak odebrat obslužnou rutinu</span><span class="sxs-lookup"><span data-stu-id="a56fb-234">How to remove a handler</span></span>

<span data-ttu-id="a56fb-235">Chcete-li odebrat obslužnou rutinu, zavolejte jeho `Dispose` metoda.</span><span class="sxs-lookup"><span data-stu-id="a56fb-235">To remove a handler, call its `Dispose` method.</span></span>

<span data-ttu-id="a56fb-236">**Klientský kód pro metodu volat ze serveru**</span><span class="sxs-lookup"><span data-stu-id="a56fb-236">**Client code for a method called from server**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample22.cs?highlight=1)]

<span data-ttu-id="a56fb-237">**Klientský kód odebrat obslužnou rutinu**</span><span class="sxs-lookup"><span data-stu-id="a56fb-237">**Client code to remove the handler**</span></span>

[!code-css[Main](signalr-1x-hubs-api-guide-net-client/samples/sample23.css?highlight=1)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a><span data-ttu-id="a56fb-238">Volání metody serveru z klienta</span><span class="sxs-lookup"><span data-stu-id="a56fb-238">How to call server methods from the client</span></span>

<span data-ttu-id="a56fb-239">Chcete-li volat metodu na serveru, použijte `Invoke` metodu na proxy server rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a56fb-239">To call a method on the server, use the `Invoke` method on the Hub proxy.</span></span>

<span data-ttu-id="a56fb-240">Pokud metoda server nemá žádnou návratovou hodnotu, použijte přetížení obecné `Invoke` metody.</span><span class="sxs-lookup"><span data-stu-id="a56fb-240">If the server method has no return value, use the non-generic overload of the `Invoke` method.</span></span>

<span data-ttu-id="a56fb-241">**Serverový kód pro metodu, která nemá žádnou návratovou hodnotu**</span><span class="sxs-lookup"><span data-stu-id="a56fb-241">**Server code for a method that has no return value**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample24.cs?highlight=3)]

<span data-ttu-id="a56fb-242">**Klientský kód volá metodu, která nemá žádnou návratovou hodnotu**</span><span class="sxs-lookup"><span data-stu-id="a56fb-242">**Client code calling a method that has no return value**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample25.cs?highlight=1)]

<span data-ttu-id="a56fb-243">Pokud metoda serveru má návratovou hodnotu, určit návratový typ jako generický typ `Invoke` metody.</span><span class="sxs-lookup"><span data-stu-id="a56fb-243">If the server method has a return value, specify the return type as the generic type of the `Invoke` method.</span></span>

<span data-ttu-id="a56fb-244">**Serverový kód pro metodu, nemá návratovou hodnotu, která přebírá parametr komplexního typu**</span><span class="sxs-lookup"><span data-stu-id="a56fb-244">**Server code for a method that has a return value and takes a complex type parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample26.cs?highlight=1)]

<span data-ttu-id="a56fb-245">**Třída akcie použitý pro parametr a vrátí hodnotu**</span><span class="sxs-lookup"><span data-stu-id="a56fb-245">**The Stock class used for the parameter and return value**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample27.cs)]

<span data-ttu-id="a56fb-246">**Klientský kód volá metodu, nemá návratovou hodnotu, která přebírá parametr komplexního typu, v asynchronní metodě technologie ASP.NET 4.5**</span><span class="sxs-lookup"><span data-stu-id="a56fb-246">**Client code calling a method that has a return value and takes a complex type parameter, in an ASP.NET 4.5 async method**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample28.cs?highlight=1-2)]

<span data-ttu-id="a56fb-247">**Klientský kód volá metodu, nemá návratovou hodnotu, která přebírá parametr komplexního typu, v synchronní metodě**</span><span class="sxs-lookup"><span data-stu-id="a56fb-247">**Client code calling a method that has a return value and takes a complex type parameter, in a synchronous method**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample29.cs?highlight=1-2)]

<span data-ttu-id="a56fb-248">`Invoke` Metoda asynchronně provede a vrátí `Task` objektu.</span><span class="sxs-lookup"><span data-stu-id="a56fb-248">The `Invoke` method executes asynchronously and returns a `Task` object.</span></span> <span data-ttu-id="a56fb-249">Pokud nezadáte `await` nebo `.Wait()`, dalším řádku kódu se provedou předtím, než metody, která je zapotřebí vyvolat neskončí.</span><span class="sxs-lookup"><span data-stu-id="a56fb-249">If you don't specify `await` or `.Wait()`, the next line of code will execute before the method that you invoke has finished executing.</span></span>

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a><span data-ttu-id="a56fb-250">Zpracování událostí doby platnosti</span><span class="sxs-lookup"><span data-stu-id="a56fb-250">How to handle connection lifetime events</span></span>

<span data-ttu-id="a56fb-251">Funkce SignalR poskytuje následující připojení události doby života, které dokáže zpracovat:</span><span class="sxs-lookup"><span data-stu-id="a56fb-251">SignalR provides the following connection lifetime events that you can handle:</span></span>

- <span data-ttu-id="a56fb-252">`Received`: Vyvoláno, když je veškerá data přijatá v připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-252">`Received`: Raised when any data is received on the connection.</span></span> <span data-ttu-id="a56fb-253">Poskytuje přijatá data.</span><span class="sxs-lookup"><span data-stu-id="a56fb-253">Provides the received data.</span></span>
- <span data-ttu-id="a56fb-254">`ConnectionSlow`: Vyvoláno, když klient zjistí pomalý nebo často vyřazení připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-254">`ConnectionSlow`: Raised when the client detects a slow or frequently dropping connection.</span></span>
- <span data-ttu-id="a56fb-255">`Reconnecting`: Vyvolá se při přenosu začne znovu obnovovat.</span><span class="sxs-lookup"><span data-stu-id="a56fb-255">`Reconnecting`: Raised when the underlying transport begins reconnecting.</span></span>
- <span data-ttu-id="a56fb-256">`Reconnected`: Vyvolá se při přenosu má připojen.</span><span class="sxs-lookup"><span data-stu-id="a56fb-256">`Reconnected`: Raised when the underlying transport has reconnected.</span></span>
- <span data-ttu-id="a56fb-257">`StateChanged`: Vyvolá se při změně stavu připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-257">`StateChanged`: Raised when the connection state changes.</span></span> <span data-ttu-id="a56fb-258">Poskytuje stav starý a nový stav.</span><span class="sxs-lookup"><span data-stu-id="a56fb-258">Provides the old state and the new state.</span></span> <span data-ttu-id="a56fb-259">Informace o připojení najdete v části hodnoty stavu [ConnectionState výčet](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connectionstate(v=vs.111).aspx).</span><span class="sxs-lookup"><span data-stu-id="a56fb-259">For information about connection state values see [ConnectionState Enumeration](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connectionstate(v=vs.111).aspx).</span></span>
- <span data-ttu-id="a56fb-260">`Closed`: Vyvolá se při připojení se odpojil.</span><span class="sxs-lookup"><span data-stu-id="a56fb-260">`Closed`: Raised when the connection has disconnected.</span></span>

<span data-ttu-id="a56fb-261">Například pokud chcete zobrazit varovné zprávy o chybách, které nejsou závažné, ale způsobit problémy s přerušovaným připojením, například jako pomalost nebo častému odstranění připojení, zpracování `ConnectionSlow` událostí.</span><span class="sxs-lookup"><span data-stu-id="a56fb-261">For example, if you want to display warning messages for errors that are not fatal but cause intermittent connection problems, such as slowness or frequent dropping of the connection, handle the `ConnectionSlow` event.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample30.cs)]

<span data-ttu-id="a56fb-262">Další informace najdete v tématu [principy a zpracování událostí doby platnosti v knihovně SignalR](../guide-to-the-api/handling-connection-lifetime-events.md).</span><span class="sxs-lookup"><span data-stu-id="a56fb-262">For more information, see [Understanding and Handling Connection Lifetime Events in SignalR](../guide-to-the-api/handling-connection-lifetime-events.md).</span></span>

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a><span data-ttu-id="a56fb-263">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="a56fb-263">How to handle errors</span></span>

<span data-ttu-id="a56fb-264">Pokud není explicitně povolit podrobné chybové zprávy na serveru, obsahuje objekt výjimky, která vrací SignalR po chybě minimální informace o této chybě.</span><span class="sxs-lookup"><span data-stu-id="a56fb-264">If you don't explicitly enable detailed error messages on the server, the exception object that SignalR returns after an error contains minimal information about the error.</span></span> <span data-ttu-id="a56fb-265">Například, pokud je volání `newContosoChatMessage` selže, chybová zpráva v objektu chyba obsahuje "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" podrobné chybové zprávy pro klienty v produkčním prostředí se nedoporučuje z bezpečnostních důvodů, ale pokud chcete povolit podrobné chybové zprávy pro odesílání na serveru pro účely odstraňování potíží, použijte následující kód.</span><span class="sxs-lookup"><span data-stu-id="a56fb-265">For example, if a call to `newContosoChatMessage` fails, the error message in the error object contains "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Sending detailed error messages to clients in production is not recommended for security reasons, but if you want to enable detailed error messages for troubleshooting purposes, use the following code on the server.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample31.cs?highlight=2)]

<a id="handleerrors"></a>

<span data-ttu-id="a56fb-266">Zpracování chyb, které vyvolává SignalR, můžete přidat obslužnou rutinu pro `Error` události u objektu připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-266">To handle errors that SignalR raises, you can add a handler for the `Error` event on the connection object.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample32.cs)]

<span data-ttu-id="a56fb-267">Zpracování chyb z volání metod, zabalte kód v bloku try-catch.</span><span class="sxs-lookup"><span data-stu-id="a56fb-267">To handle errors from method invocations, wrap the code in a try-catch block.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample33.cs)]

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a><span data-ttu-id="a56fb-268">Jak povolit protokolování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="a56fb-268">How to enable client-side logging</span></span>

<span data-ttu-id="a56fb-269">Chcete-li povolit protokolování na straně klienta, nastavte `TraceLevel` a `TraceWriter` vlastnosti objektu připojení.</span><span class="sxs-lookup"><span data-stu-id="a56fb-269">To enable client-side logging, set the `TraceLevel` and `TraceWriter` properties on the connection object.</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample34.cs?highlight=2-3)]

<a id="wpfsl"></a>

## <a name="wpf-silverlight-and-console-application-code-samples-for-client-methods-that-the-server-can-call"></a><span data-ttu-id="a56fb-270">WPF, Silverlight a Konzolová aplikace ukázky kódu pro metody klienta, které můžete volat na serveru</span><span class="sxs-lookup"><span data-stu-id="a56fb-270">WPF, Silverlight, and console application code samples for client methods that the server can call</span></span>

<span data-ttu-id="a56fb-271">Ukázky kódu je uvedeno výše pro definování metody klienta, které můžete volat serveru platí pro klienty WinRT.</span><span class="sxs-lookup"><span data-stu-id="a56fb-271">The code samples shown earlier for defining client methods that the server can call apply to WinRT clients.</span></span> <span data-ttu-id="a56fb-272">Následující ukázky ukazují ekvivalentní kód pro WPF, Silverlight a klienty aplikace konzoly.</span><span class="sxs-lookup"><span data-stu-id="a56fb-272">The following samples show the equivalent code for WPF, Silverlight, and console application clients.</span></span>

### <a name="methods-without-parameters"></a><span data-ttu-id="a56fb-273">Metody bez parametrů</span><span class="sxs-lookup"><span data-stu-id="a56fb-273">Methods without parameters</span></span>

<span data-ttu-id="a56fb-274">**WPF klientský kód pro metodu s názvem ze serveru bez parametrů**</span><span class="sxs-lookup"><span data-stu-id="a56fb-274">**WPF client code for method called from server without parameters**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample35.cs?highlight=1)]

<span data-ttu-id="a56fb-275">**Kód klienta programu Silverlight pro metodu s názvem ze serveru bez parametrů**</span><span class="sxs-lookup"><span data-stu-id="a56fb-275">**Silverlight client code for method called from server without parameters**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample36.cs?highlight=1)]

<span data-ttu-id="a56fb-276">**Konzole application klientský kód pro metodu s názvem ze serveru bez parametrů**</span><span class="sxs-lookup"><span data-stu-id="a56fb-276">**Console application client code for method called from server without parameters**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample37.cs?highlight=1)]

### <a name="methods-with-parameters-specifying-the-parameter-types"></a><span data-ttu-id="a56fb-277">Metody s parametry, určující typy parametrů</span><span class="sxs-lookup"><span data-stu-id="a56fb-277">Methods with parameters, specifying the parameter types</span></span>

<span data-ttu-id="a56fb-278">**WPF klientský kód pro metodu volat ze serveru s parametrem**</span><span class="sxs-lookup"><span data-stu-id="a56fb-278">**WPF client code for a method called from server with a parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample38.cs?highlight=1,4)]

<span data-ttu-id="a56fb-279">**Kód klienta programu Silverlight pro metodu volat ze serveru s parametrem**</span><span class="sxs-lookup"><span data-stu-id="a56fb-279">**Silverlight client code for a method called from server with a parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample39.cs?highlight=1,5)]

<span data-ttu-id="a56fb-280">**Kód klienta konzolové aplikace pro metodu volat ze serveru s parametrem**</span><span class="sxs-lookup"><span data-stu-id="a56fb-280">**Console application client code for a method called from server with a parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample40.cs?highlight=1-2)]

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a><span data-ttu-id="a56fb-281">Metody s parametry, dynamických objektů pro parametry</span><span class="sxs-lookup"><span data-stu-id="a56fb-281">Methods with parameters, specifying dynamic objects for the parameters</span></span>

<span data-ttu-id="a56fb-282">**WPF klientský kód pro metodu volat ze serveru s parametrem, pomocí dynamického objektu pro parametr**</span><span class="sxs-lookup"><span data-stu-id="a56fb-282">**WPF client code for a method called from server with a parameter, using a dynamic object for the parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample41.cs?highlight=1,4)]

<span data-ttu-id="a56fb-283">**Kód klienta programu Silverlight pro metodu volat ze serveru s parametrem, pomocí dynamického objektu pro parametr**</span><span class="sxs-lookup"><span data-stu-id="a56fb-283">**Silverlight client code for a method called from server with a parameter, using a dynamic object for the parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample42.cs?highlight=1,5)]

<span data-ttu-id="a56fb-284">**Kód klienta konzolové aplikace pro metodu volat ze serveru s parametrem, pomocí dynamického objektu pro parametr**</span><span class="sxs-lookup"><span data-stu-id="a56fb-284">**Console application client code for a method called from server with a parameter, using a dynamic object for the parameter**</span></span>

[!code-csharp[Main](signalr-1x-hubs-api-guide-net-client/samples/sample43.cs?highlight=1-2)]
