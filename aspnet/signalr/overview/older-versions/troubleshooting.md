---
uid: signalr/overview/older-versions/troubleshooting
title: Řešení potíží s knihovnou SignalR (SignalR 1.x) | Dokumentace Microsoftu
author: pfletcher
description: Tento článek popisuje běžné problémy s vývojem aplikací SignalR.
ms.author: riande
ms.date: 06/05/2013
ms.assetid: 347210ba-c452-4feb-886f-b51d89f58971
msc.legacyurl: /signalr/overview/older-versions/troubleshooting
msc.type: authoredcontent
ms.openlocfilehash: 6c2a8e72959c9370ff46084ca135c2b2977f4f42
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287680"
---
<a name="signalr-troubleshooting-signalr-1x"></a><span data-ttu-id="8f9ab-103">Řešení potíží s knihovnou SignalR (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="8f9ab-103">SignalR Troubleshooting (SignalR 1.x)</span></span>
====================
<span data-ttu-id="8f9ab-104">podle [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="8f9ab-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="8f9ab-105">Tento dokument popisuje řešení běžných problémů s knihovnou SignalR.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-105">This document describes common troubleshooting issues with SignalR.</span></span>


<span data-ttu-id="8f9ab-106">Tento dokument obsahuje následující části.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-106">This document contains the following sections.</span></span>

- [<span data-ttu-id="8f9ab-107">Volání metod mezi klientem a serverem bez upozornění selže</span><span class="sxs-lookup"><span data-stu-id="8f9ab-107">Calling methods between the client and server silently fails</span></span>](#connection)
- [<span data-ttu-id="8f9ab-108">Další problémy s připojením</span><span class="sxs-lookup"><span data-stu-id="8f9ab-108">Other connection issues</span></span>](#other)
- [<span data-ttu-id="8f9ab-109">Chyby kompilace a na straně serveru</span><span class="sxs-lookup"><span data-stu-id="8f9ab-109">Compilation and server-side errors</span></span>](#server)
- [<span data-ttu-id="8f9ab-110">Problémy v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f9ab-110">Visual Studio issues</span></span>](#vs)
- [<span data-ttu-id="8f9ab-111">Internetová informační služba problémy</span><span class="sxs-lookup"><span data-stu-id="8f9ab-111">Internet Information Services issues</span></span>](#iis)
- [<span data-ttu-id="8f9ab-112">Problémy s Azure</span><span class="sxs-lookup"><span data-stu-id="8f9ab-112">Azure issues</span></span>](#azure)

<a id="connection"></a>

## <a name="calling-methods-between-the-client-and-server-silently-fails"></a><span data-ttu-id="8f9ab-113">Volání metod mezi klientem a serverem bez upozornění selže</span><span class="sxs-lookup"><span data-stu-id="8f9ab-113">Calling methods between the client and server silently fails</span></span>

<span data-ttu-id="8f9ab-114">Tato část popisuje možné příčiny pro volání metody mezi klientem a serverem smysluplné chybovou zprávu v případě selhání.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-114">This section describes possible causes for a method call between client and server to fail without a meaningful error message.</span></span> <span data-ttu-id="8f9ab-115">V případě aplikace SignalR server nemá žádné informace o metody, které klient implementuje; Když server volá metodu klienta, metoda název a parametru data se odesílají do klienta a metoda provádí pouze v případě, že existuje ve formátu, který je zadaný server.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-115">In a SignalR application, the server has no information about the methods that the client implements; when the server invokes a client method, the method name and parameter data are sent to the client, and the method is executed only if it exists in the format that the server specified.</span></span> <span data-ttu-id="8f9ab-116">Pokud je nenašla žádná odpovídající metoda na straně klienta, nic se nestane, a je vyvolána žádná chybová zpráva na serveru.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-116">If no matching method is found on the client, nothing happens, and no error message is raised on the server.</span></span>

<span data-ttu-id="8f9ab-117">Aby to prověřili aplikace volá metody klienta, můžete zapnout protokolování před voláním metody start na IOT hub a zobrazit jaké volání pocházejí ze serveru.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-117">To further investigate client methods not getting called, you can turn on logging before the calling the start method on the hub to see what calls are coming from the server.</span></span> <span data-ttu-id="8f9ab-118">Povolit protokolování aplikace v jazyce JavaScript, najdete v článku [jak povolit protokolování na straně klienta (verze jazyka JavaScript klienta)](../guide-to-the-api/hubs-api-guide-javascript-client.md#logging).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-118">To enable logging in a JavaScript application, see [How to enable client-side logging (JavaScript client version)](../guide-to-the-api/hubs-api-guide-javascript-client.md#logging).</span></span> <span data-ttu-id="8f9ab-119">Povolení protokolování v klientské aplikaci .NET, naleznete v tématu [jak povolit protokolování na straně klienta (verze .NET klienta)](../guide-to-the-api/hubs-api-guide-net-client.md#logging).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-119">To enable logging in a .NET client application, see [How to enable client-side logging (.NET Client version)](../guide-to-the-api/hubs-api-guide-net-client.md#logging).</span></span>

### <a name="misspelled-method-incorrect-method-signature-or-incorrect-hub-name"></a><span data-ttu-id="8f9ab-120">Chybně napsaná metody, podpis metody nesprávné nebo název nesprávné centra</span><span class="sxs-lookup"><span data-stu-id="8f9ab-120">Misspelled method, incorrect method signature, or incorrect hub name</span></span>

<span data-ttu-id="8f9ab-121">Pokud název nebo podpis metody volané metody přesně neshoduje s odpovídající metody na straně klienta, volání se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-121">If the name or signature of a called method does not exactly match an appropriate method on the client, the call will fail.</span></span> <span data-ttu-id="8f9ab-122">Ověřte, jestli název metody, který volá server odpovídá názvu metody na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-122">Verify that the method name called by the server matches the name of the method on the client.</span></span> <span data-ttu-id="8f9ab-123">Navíc SignalR vytvoří proxy server rozbočovače-ve formátu camelCase metody, jako je vhodné v jazyce JavaScript, tedy volána metoda `SendMessage` na serveru by byla volána `sendMessage` v klientovi proxy.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-123">Also, SignalR creates the hub proxy using camel-cased methods, as is appropriate in JavaScript, so a method called `SendMessage` on the server would be called `sendMessage` in the client proxy.</span></span> <span data-ttu-id="8f9ab-124">Pokud používáte `HubName` atribut v kódu na straně serveru, zkontrolujte, zda název, pomocí kterého odpovídá název používaný k vytvoření rozbočovače na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-124">If you use the `HubName` attribute in your server-side code, verify that the name used matches the name used to create the hub on the client.</span></span> <span data-ttu-id="8f9ab-125">Pokud použijete `HubName` atribut, ověřte, zda je název rozbočovače v klientovi JavaScript – ve formátu camelCase, jako je například chatHub místo ChatHub.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-125">If you do not use the `HubName` attribute, verify that the name of the hub in a JavaScript client is camel-cased, such as chatHub instead of ChatHub.</span></span>

### <a name="duplicate-method-name-on-client"></a><span data-ttu-id="8f9ab-126">Duplicitní název metody v klientovi</span><span class="sxs-lookup"><span data-stu-id="8f9ab-126">Duplicate method name on client</span></span>

<span data-ttu-id="8f9ab-127">Ověřte, že nemáte duplicitní metoda na straně klienta, který se liší pouze velikostí písma.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-127">Verify that you do not have a duplicate method on the client that differs only by case.</span></span> <span data-ttu-id="8f9ab-128">Pokud vaše klientská aplikace má metodu nazvanou `sendMessage`, ověřte, že není k dispozici také metodu nazvanou `SendMessage` také.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-128">If your client application has a method called `sendMessage`, verify that there isn't also a method called `SendMessage` as well.</span></span>

### <a name="missing-json-parser-on-the-client"></a><span data-ttu-id="8f9ab-129">Chybějící analyzátor JSON na straně klienta</span><span class="sxs-lookup"><span data-stu-id="8f9ab-129">Missing JSON parser on the client</span></span>

<span data-ttu-id="8f9ab-130">Funkce SignalR vyžaduje JSON analyzátor, aby byly k serializaci volání mezi serverem a klientem.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-130">SignalR requires a JSON parser to be present to serialize calls between the server and the client.</span></span> <span data-ttu-id="8f9ab-131">Pokud váš klient nemá vestavěné analyzátor JSON (jako je například Internet Explorer 7), budete muset jeden ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-131">If your client doesn't have a built-in JSON parser (such as Internet Explorer 7), you'll need to include one in your application.</span></span> <span data-ttu-id="8f9ab-132">Můžete si stáhnout analyzátor JSON [tady](http://nuget.org/packages/json2).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-132">You can download the JSON parser [here](http://nuget.org/packages/json2).</span></span>

### <a name="mixing-hub-and-persistentconnection-syntax"></a><span data-ttu-id="8f9ab-133">Kombinování centra a PersistentConnection syntaxe</span><span class="sxs-lookup"><span data-stu-id="8f9ab-133">Mixing Hub and PersistentConnection syntax</span></span>

<span data-ttu-id="8f9ab-134">Funkce SignalR používá dva modely komunikace: Rozbočovače a PersistentConnections.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-134">SignalR uses two communication models: Hubs and PersistentConnections.</span></span> <span data-ttu-id="8f9ab-135">Syntaxe pro volání těchto dvou komunikačních modelů se liší v kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-135">The syntax for calling these two communication models is different in the client code.</span></span> <span data-ttu-id="8f9ab-136">Pokud jste přidali rozbočovač v serverovém kódu, ověřte, že veškerý kód klienta používá syntaxi správné centra.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-136">If you have added a hub in your server code, verify that all of your client code uses the proper hub syntax.</span></span>

<span data-ttu-id="8f9ab-137">**JavaScript klientský kód, který vytvoří PersistentConnection v javascriptový klient**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-137">**JavaScript client code that creates a PersistentConnection in a JavaScript client**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample1.js)]

<span data-ttu-id="8f9ab-138">**Klientský kód jazyka JavaScript, který vytvoří proxy server rozbočovače v klientovi Javascript**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-138">**JavaScript client code that creates a Hub Proxy in a Javascript client**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample2.js)]

<span data-ttu-id="8f9ab-139">**Kód jazyka C# serveru, který se mapuje PersistentConnection trasy**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-139">**C# server code that maps a route to a PersistentConnection**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample3.cs)]

<span data-ttu-id="8f9ab-140">**Kód jazyka C# serveru, která mapuje trasu k rozbočovači, nebo více rozbočovače, pokud máte více aplikací**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-140">**C# server code that maps a route to a Hub, or to mulitple hubs if you have multiple applications**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample4.cs)]

### <a name="connection-started-before-subscriptions-are-added"></a><span data-ttu-id="8f9ab-141">Připojení, které jsou spuštěny před předplatná se přidají</span><span class="sxs-lookup"><span data-stu-id="8f9ab-141">Connection started before subscriptions are added</span></span>

<span data-ttu-id="8f9ab-142">Pokud připojení rozbočovače pro spuštění před metody, které lze volat ze serveru se přidají k proxy serveru, nebude přijímat zprávy.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-142">If the Hub's connection is started before methods that can be called from the server are added to the proxy, messages will not be received.</span></span> <span data-ttu-id="8f9ab-143">Následující kód jazyka JavaScript se nespustí centra správně:</span><span class="sxs-lookup"><span data-stu-id="8f9ab-143">The following JavaScript code will not start the hub properly:</span></span>

<span data-ttu-id="8f9ab-144">**Nesprávné klientský kód jazyka JavaScript, který neumožní mohlo přijímat zprávy rozbočovače**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-144">**Incorrect JavaScript client code that will not allow Hubs messages to be received**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample5.js)]

<span data-ttu-id="8f9ab-145">Místo toho přidáte předplatná metoda před voláním spuštění:</span><span class="sxs-lookup"><span data-stu-id="8f9ab-145">Instead, add the method subscriptions before calling Start:</span></span>

<span data-ttu-id="8f9ab-146">**Klientský kód jazyka JavaScript, který správně přidá předplatných pro Centrum**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-146">**JavaScript client code that correctly adds subscriptions to a hub**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample6.js)]

### <a name="missing-method-name-on-the-hub-proxy"></a><span data-ttu-id="8f9ab-147">Chybí název metody na proxy server rozbočovače</span><span class="sxs-lookup"><span data-stu-id="8f9ab-147">Missing method name on the hub proxy</span></span>

<span data-ttu-id="8f9ab-148">Ověřte, že je metoda, definována na serveru na straně klienta přihlášen k odběru.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-148">Verify that the method defined on the server is subscribed to on the client.</span></span> <span data-ttu-id="8f9ab-149">I když server definuje metodu, musí pořád přidané k proxy serveru klienta.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-149">Even though the server defines the method, it must still be added to the client proxy.</span></span> <span data-ttu-id="8f9ab-150">Metody mohou být přidány do proxy serveru klienta takto (Všimněte si, že metoda je přidána do `client` člen centra centra není přímo):</span><span class="sxs-lookup"><span data-stu-id="8f9ab-150">Methods can be added to the client proxy in the following ways (Note that the method is added to the `client` member of the hub, not the hub directly):</span></span>

<span data-ttu-id="8f9ab-151">**Klientský kód jazyka JavaScript, který přidá metody proxy server rozbočovače**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-151">**JavaScript client code that adds methods to a hub proxy**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample7.js)]

### <a name="hub-or-hub-methods-not-declared-as-public"></a><span data-ttu-id="8f9ab-152">Rozbočovač nebo není deklarovaný jako Public metod rozbočovače</span><span class="sxs-lookup"><span data-stu-id="8f9ab-152">Hub or hub methods not declared as Public</span></span>

<span data-ttu-id="8f9ab-153">Uvidí na straně klienta, musí být deklarována implementace rozbočovače a metody jako `public`.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-153">To be visible on the client, the hub implementation and methods must be declared as `public`.</span></span>

### <a name="accessing-hub-from-a-different-application"></a><span data-ttu-id="8f9ab-154">Přístup k centru z jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="8f9ab-154">Accessing hub from a different application</span></span>

<span data-ttu-id="8f9ab-155">Rozbočovače SignalR je přístupný pouze prostřednictvím aplikací, které implementují klienti SignalR.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-155">SignalR Hubs can only be accessed through applications that implement SignalR clients.</span></span> <span data-ttu-id="8f9ab-156">SignalR nemůže spolupracovat s ostatními knihovnami komunikace (jako je protokol SOAP nebo webových služeb WCF.) Pokud není k dispozici pro cílovou platformu žádné klienta SignalR, nelze přímý přístup koncového bodu serveru.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-156">SignalR cannot interoperate with other communication libraries (like SOAP or WCF web services.) If there is no SignalR client available for your target platform, you cannot access the server's endpoint directly.</span></span>

### <a name="manually-serializing-data"></a><span data-ttu-id="8f9ab-157">Ruční serializaci dat</span><span class="sxs-lookup"><span data-stu-id="8f9ab-157">Manually serializing data</span></span>

<span data-ttu-id="8f9ab-158">Funkce SignalR automaticky použije JSON k serializaci metodu parametry neexistuje není potřeba provést sami.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-158">SignalR will automatically use JSON to serialize your method parameters- there's no need to do it yourself.</span></span>

### <a name="remote-hub-method-not-executed-on-client-in-ondisconnected-function"></a><span data-ttu-id="8f9ab-159">Vzdálené metody rozbočovače na klientovi ve funkci ondisconnected rozbočovače nebyly provedeny</span><span class="sxs-lookup"><span data-stu-id="8f9ab-159">Remote Hub method not executed on client in OnDisconnected function</span></span>

<span data-ttu-id="8f9ab-160">Toto chování je záměrné.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-160">This behavior is by design.</span></span> <span data-ttu-id="8f9ab-161">Když `OnDisconnected` je volání rozbočovače již přešla `Disconnected` stavu, což nepovoluje další metod rozbočovače, která se má volat.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-161">When `OnDisconnected` is called, the hub has already entered the `Disconnected` state, which does not allow further hub methods to be called.</span></span>

<span data-ttu-id="8f9ab-162">**Kód jazyka C# serveru, který správně spustí kód v případě ondisconnected rozbočovače**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-162">**C# server code that correctly executes code in the OnDisconnected event**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample8.cs)]

### <a name="connection-limit-reached"></a><span data-ttu-id="8f9ab-163">Dosáhlo se limitu připojení</span><span class="sxs-lookup"><span data-stu-id="8f9ab-163">Connection limit reached</span></span>

<span data-ttu-id="8f9ab-164">Při použití plnou verzi služby IIS na operačním systému klienta, jako je Windows 7, je nastaveno z důvodu omezení 10 připojení.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-164">When using the full version of IIS on a client operating system like Windows 7, a 10-connection limit is imposed.</span></span> <span data-ttu-id="8f9ab-165">Pokud používáte klientský operační systém, použijte službu IIS Express, aby tento limit.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-165">When using a client OS, use IIS Express instead to avoid this limit.</span></span>

### <a name="cross-domain-connection-not-set-up-properly"></a><span data-ttu-id="8f9ab-166">Připojení mezi doménami nejsou nastaveny správně</span><span class="sxs-lookup"><span data-stu-id="8f9ab-166">Cross-domain connection not set up properly</span></span>

<span data-ttu-id="8f9ab-167">Pokud připojení mezi doménami není správně nastaven (připojení, pro kterou SignalR adresa URL není ve stejné doméně jako stránka hostingu), může připojení selhat a chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-167">If a cross-domain connection (a connection for which the SignalR URL is not in the same domain as the hosting page) is not set up correctly, the connection may fail without an error message.</span></span> <span data-ttu-id="8f9ab-168">Informace o tom, jak povolit komunikaci mezi doménami, najdete v části [jak k navázání připojení mezi doménami](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-168">For information on how to enable cross-domain communication, see [How to establish a cross-domain connection](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).</span></span>

### <a name="connection-using-ntlm-active-directory-not-working-in-net-client"></a><span data-ttu-id="8f9ab-169">Připojení pomocí protokolu NTLM (Active Directory) nebudou fungovat v rozhraní .NET klienta</span><span class="sxs-lookup"><span data-stu-id="8f9ab-169">Connection using NTLM (Active Directory) not working in .NET client</span></span>

<span data-ttu-id="8f9ab-170">Připojení v klientské aplikaci .NET, který používá zabezpečení domény může selhat, pokud připojení není správně nakonfigurována.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-170">A connection in a .NET client application that uses Domain security may fail if the connection is not configured properly.</span></span> <span data-ttu-id="8f9ab-171">V prostředí domény používat SignalR, nastavte vlastnost požadavku připojení následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="8f9ab-171">To use SignalR in a domain environment, set the requisite connection property as follows:</span></span>

<span data-ttu-id="8f9ab-172">**Kód jazyka C# klienta, který implementuje přihlašovací údaje pro připojení**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-172">**C# client code that implements connection credentials**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample9.cs)]

<a id="other"></a>

## <a name="other-connection-issues"></a><span data-ttu-id="8f9ab-173">Další problémy s připojením</span><span class="sxs-lookup"><span data-stu-id="8f9ab-173">Other connection issues</span></span>

<span data-ttu-id="8f9ab-174">Tato část popisuje příčiny a řešení určitými příznaky nebo chybové zprávy, ke kterým dochází při připojení.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-174">This section describes the causes and solutions for specific symptoms or error messages that occur during a connection.</span></span>

### <a name="start-must-be-called-before-data-can-be-sent-error"></a><span data-ttu-id="8f9ab-175">Chyba "Start musí být volána před odesláním dat"</span><span class="sxs-lookup"><span data-stu-id="8f9ab-175">"Start must be called before data can be sent" error</span></span>

<span data-ttu-id="8f9ab-176">Tato chyba obvykle nastává Pokud kód odkazuje na objekty SignalR předtím, než je zahájeno připojení.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-176">This error is commonly seen if code references SignalR objects before the connection is started.</span></span> <span data-ttu-id="8f9ab-177">Wireup pro obslužné rutiny a podobně, že volání metody definované na serveru musí být přidá po dokončení připojení.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-177">The wireup for handlers and the like that will call methods defined on the server must be added after the connection completes.</span></span> <span data-ttu-id="8f9ab-178">Všimněte si, že volání `Start` je asynchronní, takže kód následující po volání může být spuštěna dříve, než se dokončí.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-178">Note that the call to `Start` is asynchronous, so code after the call may be executed before it completes.</span></span> <span data-ttu-id="8f9ab-179">Nejlepší způsob, jak přidat obslužné rutiny po spuštění připojení zcela je umístíte do zpětného volání funkce, která se předá jako parametr metodě start:</span><span class="sxs-lookup"><span data-stu-id="8f9ab-179">The best way to add handlers after a connection starts completely is to put them into a callback function that is passed as a parameter to the start method:</span></span>

<span data-ttu-id="8f9ab-180">**Klientský kód jazyka JavaScript, který správně přidá obslužné rutiny událostí, které odkazují na objekty SignalR**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-180">**JavaScript client code that correctly adds event handlers that reference SignalR objects**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample10.js?highlight=1)]

<span data-ttu-id="8f9ab-181">Tato chyba se zobrazí také v případě připojení přestane při SignalR objekty stále odkazuje.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-181">This error will also be seen if a connection stops while SignalR objects are still being referenced.</span></span>

### <a name="301-moved-permanently-or-302-moved-temporarily-error"></a><span data-ttu-id="8f9ab-182">"Trvale přesunuto 301" nebo "302 přesunout dočasně" Chyba</span><span class="sxs-lookup"><span data-stu-id="8f9ab-182">"301 Moved Permanently" or "302 Moved Temporarily" error</span></span>

<span data-ttu-id="8f9ab-183">Tato chyba může zobrazit, pokud projekt obsahuje složku s názvem SignalR, která bude v konfliktu s proxy automaticky vytvoří.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-183">This error may be seen if the project contains a folder called SignalR, which will interfere with the automatically-created proxy.</span></span> <span data-ttu-id="8f9ab-184">K této chybě předejít, nepoužívejte složku s názvem `SignalR` v aplikaci, nebo vypnout automatické proxy generování vypnout.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-184">To avoid this error, do not use a folder called `SignalR` in your application, or turn automatic proxy generation off.</span></span> <span data-ttu-id="8f9ab-185">Zobrazit [The generované Proxy a co to dělá za vás](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy) další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-185">See [The Generated Proxy and what it does for you](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy) for more details.</span></span>

### <a name="403-forbidden-error-in-net-or-silverlight-client"></a><span data-ttu-id="8f9ab-186">Chyba "403 Zakázáno" v .NET nebo Silverlight klientu</span><span class="sxs-lookup"><span data-stu-id="8f9ab-186">"403 Forbidden" error in .NET or Silverlight client</span></span>

<span data-ttu-id="8f9ab-187">K této chybě může dojít v prostředí napříč doménami, kde komunikace mezi doménami není povolené vhodným způsobem.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-187">This error may occur in cross-domain environments where cross-domain communication is not properly enabled.</span></span> <span data-ttu-id="8f9ab-188">Informace o tom, jak povolit komunikaci mezi doménami, najdete v části [jak k navázání připojení mezi doménami](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-188">For information on how to enable cross-domain communication, see [How to establish a cross-domain connection](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).</span></span> <span data-ttu-id="8f9ab-189">K navázání připojení mezi doménami v klienta programu Silverlight, naleznete v tématu [připojení mezi doménami z klienty prostředí Silverlight](../guide-to-the-api/hubs-api-guide-net-client.md#slcrossdomain).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-189">To establish a cross-domain connection in a Silverlight client, see [Cross-domain connections from Silverlight clients](../guide-to-the-api/hubs-api-guide-net-client.md#slcrossdomain).</span></span>

### <a name="404-not-found-error"></a><span data-ttu-id="8f9ab-190">Chyba "404 nebyl nalezen."</span><span class="sxs-lookup"><span data-stu-id="8f9ab-190">"404 Not Found" error</span></span>

<span data-ttu-id="8f9ab-191">Existuje několik důvodů, proč tento problém.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-191">There are several causes for this issue.</span></span> <span data-ttu-id="8f9ab-192">Ověřte všechny z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="8f9ab-192">Verify all of the following:</span></span>

- <span data-ttu-id="8f9ab-193">**Odkaz adresy proxy server rozbočovače není správně naformátované:** Tato chyba obvykle nastává Pokud odkaz na generovaný centra adresa proxy serveru není správně naformátovaná.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-193">**Hub proxy address reference not formatted correctly:** This error is commonly seen if the reference to the generated hub proxy address is not formatted correctly.</span></span> <span data-ttu-id="8f9ab-194">Ověřte, že odkaz na adresu centra správně vytvořen.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-194">Verify that the reference to the hub address is made properly.</span></span> <span data-ttu-id="8f9ab-195">Zobrazit [způsob vytvoření odkazu dynamicky generované proxy](../guide-to-the-api/hubs-api-guide-javascript-client.md#dynamicproxy) podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-195">See [How to reference the dynamically generated proxy](../guide-to-the-api/hubs-api-guide-javascript-client.md#dynamicproxy) for details.</span></span>
- <span data-ttu-id="8f9ab-196">**Přidání tras pro aplikaci před přidáním trasu rozbočovače:** Pokud vaše aplikace používá jiné trasy, ověřte, zda je první trasa přidaná volání `MapHubs`.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-196">**Adding routes to application before adding the hub route:** If your application uses other routes, verify that the first route added is the call to `MapHubs`.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="8f9ab-197">"Chyba 500 interní Server"</span><span class="sxs-lookup"><span data-stu-id="8f9ab-197">"500 Internal Server Error"</span></span>

<span data-ttu-id="8f9ab-198">To je velmi obecná chyba, která by mohla mít nejrůznější příčiny.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-198">This is a very generic error that could have a wide variety of causes.</span></span> <span data-ttu-id="8f9ab-199">Podrobnosti o chybě by se měla zobrazit v protokolu událostí serveru, nebo můžete najít pomocí ladění serveru.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-199">The details of the error should appear in the server's event log, or can be found through debugging the server.</span></span> <span data-ttu-id="8f9ab-200">Když zapnete podrobné chyby na serveru může získat podrobnější informace o chybě.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-200">More detailed error information may be obtained by turning on detailed errors on the server.</span></span> <span data-ttu-id="8f9ab-201">Další informace najdete v tématu [zpracování chyb ve třídě centra](../guide-to-the-api/hubs-api-guide-server.md#handleErrors).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-201">For more information, see [How to handle errors in the Hub class](../guide-to-the-api/hubs-api-guide-server.md#handleErrors).</span></span>

### <a name="typeerror-lthubtypegt-is-undefined-error"></a><span data-ttu-id="8f9ab-202">"TypeError: &lt;hubType&gt; není definován" Chyba</span><span class="sxs-lookup"><span data-stu-id="8f9ab-202">"TypeError: &lt;hubType&gt; is undefined" error</span></span>

<span data-ttu-id="8f9ab-203">K této chybě dojde, pokud volání `MapHubs` není správně vytvořena.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-203">This error will result if the call to `MapHubs` is not made properly.</span></span> <span data-ttu-id="8f9ab-204">Zobrazit [postupem registrace směrování funkce SignalR a konfiguraci možností SignalR](../guide-to-the-api/hubs-api-guide-server.md#route) Další informace.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-204">See [How to register the SignalR route and configure SignalR options](../guide-to-the-api/hubs-api-guide-server.md#route) for more information.</span></span>

### <a name="jsonserializationexception-was-unhandled-by-user-code"></a><span data-ttu-id="8f9ab-205">JsonSerializationException nebyla ošetřena uživatelským kódem</span><span class="sxs-lookup"><span data-stu-id="8f9ab-205">JsonSerializationException was unhandled by user code</span></span>

<span data-ttu-id="8f9ab-206">Ověřte, že parametry, které odesíláte do vaší metody neobsahují neserializovatelná typy (jako jsou popisovače souborů nebo připojení k databázi).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-206">Verify that the parameters you send to your methods do not include non-serializable types (like file handles or database connections).</span></span> <span data-ttu-id="8f9ab-207">Pokud je potřeba použít členy na objekt na straně serveru, který nechcete být zaslána klientovi (buď pro zabezpečení nebo z důvodu serializace), použijte `JSONIgnore` atribut.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-207">If you need to use members on a server-side object that you don't want to be sent to the client (either for security or for reasons of serialization), use the `JSONIgnore` attribute.</span></span>

### <a name="protocol-error-unknown-transport-error"></a><span data-ttu-id="8f9ab-208">"Chyba protokolu: Došlo k chybě neznámého přenosu"</span><span class="sxs-lookup"><span data-stu-id="8f9ab-208">"Protocol error: Unknown transport" error</span></span>

<span data-ttu-id="8f9ab-209">K této chybě může dojít, pokud klient nepodporuje přenosy, které používá SignalR.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-209">This error may occur if the client does not support the transports that SignalR uses.</span></span> <span data-ttu-id="8f9ab-210">Zobrazit [přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports) informace, na kterém je možné prohlížeče s knihovnou SignalR.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-210">See [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports) for information on which browsers can be used with SignalR.</span></span>

### <a name="javascript-hub-proxy-generation-has-been-disabled"></a><span data-ttu-id="8f9ab-211">"Byl zakázán jazyk JavaScript rozbočovače proxy generation."</span><span class="sxs-lookup"><span data-stu-id="8f9ab-211">"JavaScript Hub proxy generation has been disabled."</span></span>

<span data-ttu-id="8f9ab-212">Pokud dojde k této chybě `DisableJavaScriptProxies` nastavit zároveň zahrnuje také odkaz na dynamicky generované proxy serveru na `signalr/hubs`.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-212">This error will occur if `DisableJavaScriptProxies` is set while also including a reference to the dynamically generated proxy at `signalr/hubs`.</span></span> <span data-ttu-id="8f9ab-213">Další informace o vytvoření proxy serveru ručně, najdete v části [vygenerovaný proxy server a co to dělá za vás](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-213">For more information on creating the proxy manually, see [The generated proxy and what it does for you](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy).</span></span>

### <a name="the-connection-id-is-in-the-incorrect-format-or-the-user-identity-cannot-change-during-an-active-signalr-connection-error"></a><span data-ttu-id="8f9ab-214">"ID připojení je v nesprávném formátu" nebo "identitu uživatele nelze změnit během aktivního připojení SignalR" Chyba</span><span class="sxs-lookup"><span data-stu-id="8f9ab-214">"The connection ID is in the incorrect format" or "The user identity cannot change during an active SignalR connection" error</span></span>

<span data-ttu-id="8f9ab-215">Tato chyba může zobrazit, pokud se používá ověřování a klient je odhlášen před zastavením připojení.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-215">This error may be seen if authentication is being used, and the client is logged out before the connection is stopped.</span></span> <span data-ttu-id="8f9ab-216">Řešení je zastavit před odhlášení klientovi připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-216">The solution is to stop the SignalR connection before logging the client out.</span></span>

### <a name="uncaught-error-signalr-jquery-not-found-please-ensure-jquery-is-referenced-before-the-signalrjs-file-error"></a><span data-ttu-id="8f9ab-217">"Nezachycená Chyba: SignalR: jQuery nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-217">"Uncaught Error: SignalR: jQuery not found.</span></span> <span data-ttu-id="8f9ab-218">Ujistěte se prosím, že jQuery odkazuje souboru SignalR.js"Chyba</span><span class="sxs-lookup"><span data-stu-id="8f9ab-218">Please ensure jQuery is referenced before the SignalR.js file" error</span></span>

<span data-ttu-id="8f9ab-219">Klient SignalR JavaScript vyžaduje jQuery ke spuštění.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-219">The SignalR JavaScript client requires jQuery to run.</span></span> <span data-ttu-id="8f9ab-220">Ověřte, že vaši informaci, abyste jQuery je správný, cesta používá je platný a je odkaz na jQuery před referenční dokumentace ke knihovně SignalR.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-220">Verify that your reference to jQuery is correct, that the path used is valid, and that the reference to jQuery is before the reference to SignalR.</span></span>

### <a name="uncaught-typeerror-cannot-read-property-ltpropertygt-of-undefined-error"></a><span data-ttu-id="8f9ab-221">"Nezachycená TypeError: Nelze přečíst vlastnost '&lt;vlastnost&gt;"undefined" Chyba</span><span class="sxs-lookup"><span data-stu-id="8f9ab-221">"Uncaught TypeError: Cannot read property '&lt;property&gt;' of undefined" error</span></span>

<span data-ttu-id="8f9ab-222">Tato chyba je výsledkem nemají jQuery nebo proxy server rozbočovače správně odkazuje.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-222">This error results from not having jQuery or the hubs proxy referenced properly.</span></span> <span data-ttu-id="8f9ab-223">Ověřte, že vaši informaci, jQuery a proxy server rozbočovače je správný, platnost cesty použité a že je odkaz na jQuery před odkaz na proxy server rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-223">Verify that your reference to jQuery and the hubs proxy is correct, that the path used is valid, and that the reference to jQuery is before the reference to the hubs proxy.</span></span> <span data-ttu-id="8f9ab-224">Výchozí odkaz na proxy server rozbočovače by měl vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="8f9ab-224">The default reference to the hubs proxy should look like the following:</span></span>

<span data-ttu-id="8f9ab-225">**Kód na straně klienta HTML, která správně odkazuje na proxy server rozbočovače**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-225">**HTML client-side code that correctly references the Hubs proxy**</span></span>

[!code-html[Main](troubleshooting/samples/sample11.html)]

### <a name="runtimebinderexception-was-unhandled-by-user-code-error"></a><span data-ttu-id="8f9ab-226">Chyba "RuntimeBinderException nebyla ošetřena uživatelským kódem"</span><span class="sxs-lookup"><span data-stu-id="8f9ab-226">"RuntimeBinderException was unhandled by user code" error</span></span>

<span data-ttu-id="8f9ab-227">K této chybě může dojít při přetížení nesprávné `Hub.On` se používá.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-227">This error may occur when the incorrect overload of `Hub.On` is used.</span></span> <span data-ttu-id="8f9ab-228">Pokud metoda nemá návratovou hodnotu, návratový typ musí být specifikovaný jako parametr obecného typu:</span><span class="sxs-lookup"><span data-stu-id="8f9ab-228">If the method has a return value, the return type must be specified as a generic type parameter:</span></span>

<span data-ttu-id="8f9ab-229">**Metody definované na straně klienta (bez vygenerovaný proxy server)**</span><span class="sxs-lookup"><span data-stu-id="8f9ab-229">**Method defined on the client (without generated proxy)**</span></span>

[!code-html[Main](troubleshooting/samples/sample12.html?highlight=1)]

### <a name="connection-id-is-inconsistent-or-connection-breaks-between-page-loads"></a><span data-ttu-id="8f9ab-230">ID připojení je nekonzistentní nebo přestane fungovat připojení mezi načtení stránky</span><span class="sxs-lookup"><span data-stu-id="8f9ab-230">Connection ID is inconsistent or connection breaks between page loads</span></span>

<span data-ttu-id="8f9ab-231">Toto chování je záměrné.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-231">This behavior is by design.</span></span> <span data-ttu-id="8f9ab-232">Vzhledem k tomu, že v objektu page je hostovaný objekt rozbočovače, centra je zničen při aktualizaci stránky.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-232">Since the hub object is hosted in the page object, the hub is destroyed when the page refreshes.</span></span> <span data-ttu-id="8f9ab-233">Více stránek aplikace potřebuje udržovat spojení mezi uživateli a ID připojení tak, aby byly konzistentní mezi načtení stránky.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-233">A multi-page application needs to maintain the association between users and connection IDs so that they will be consistent between page loads.</span></span> <span data-ttu-id="8f9ab-234">ID připojení mohou být uloženy na serveru buď `ConcurrentDictionary` objektu nebo databáze.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-234">The connection IDs can be stored on the server in either a `ConcurrentDictionary` object or a database.</span></span>

### <a name="value-cannot-be-null-error"></a><span data-ttu-id="8f9ab-235">"Hodnota nemůže být null" Chyba</span><span class="sxs-lookup"><span data-stu-id="8f9ab-235">"Value cannot be null" error</span></span>

<span data-ttu-id="8f9ab-236">Metody na straně serveru s volitelnými parametry se aktuálně nepodporují; Pokud tento nepovinný parametr se vynechá, metoda se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-236">Server-side methods with optional parameters are not currently supported; if the optional parameter is omitted, the method will fail.</span></span> <span data-ttu-id="8f9ab-237">Další informace najdete v tématu [volitelné parametry](https://github.com/SignalR/SignalR/issues/324).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-237">For more information, see [Optional Parameters](https://github.com/SignalR/SignalR/issues/324).</span></span>

### <a name="firefox-cant-establish-a-connection-to-the-server-at-ltaddressgt-error-in-firebug"></a><span data-ttu-id="8f9ab-238">"Firefox nemůže navázat připojení k serveru na &lt;adresu&gt;" chyby v Firebug</span><span class="sxs-lookup"><span data-stu-id="8f9ab-238">"Firefox can't establish a connection to the server at &lt;address&gt;" error in Firebug</span></span>

<span data-ttu-id="8f9ab-239">Tato chybová zpráva lze zobrazit v Firebug, pokud selže vyjednávání protokolu WebSocket přenosu a místo ní se použije jiný přenos.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-239">This error message can be seen in Firebug if negotiation of the WebSocket transport fails and another transport is used instead.</span></span> <span data-ttu-id="8f9ab-240">Toto chování je záměrné.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-240">This behavior is by design.</span></span>

### <a name="the-remote-certificate-is-invalid-according-to-the-validation-procedure-error-in-net-client-application"></a><span data-ttu-id="8f9ab-241">Chyba "Vzdálený certifikát není platný podle ověřovací procedury" v klientské aplikaci rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="8f9ab-241">"The remote certificate is invalid according to the validation procedure" error in .NET client application</span></span>

<span data-ttu-id="8f9ab-242">Pokud váš server vyžaduje vlastní klientské certifikáty, pak můžete přidat certifikátu x 509 připojení předtím, než se požadavek.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-242">If your server requires custom client certificates, then you can add an x509certificate to the connection before the request is made.</span></span> <span data-ttu-id="8f9ab-243">Přidání certifikátu do připojení pomocí `Connection.AddClientCertificate`.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-243">Add the certificate to the connection using `Connection.AddClientCertificate`.</span></span>

### <a name="connection-drops-after-authentication-times-out"></a><span data-ttu-id="8f9ab-244">Zrušení připojení vyčkat, až se ověřování vyprší časový limit</span><span class="sxs-lookup"><span data-stu-id="8f9ab-244">Connection drops after authentication times out</span></span>

<span data-ttu-id="8f9ab-245">Toto chování je záměrné.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-245">This behavior is by design.</span></span> <span data-ttu-id="8f9ab-246">Přihlašovací údaje pro ověření nelze změnit, když je připojení aktivní; Pokud chcete aktualizovat přihlašovací údaje, musí zastavit, restartovat připojení.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-246">Authentication credentials cannot be modified while a connection is active; to refresh credentials, the connection must be stopped and restarted.</span></span>

### <a name="onconnected-gets-called-twice-when-using-jquery-mobile"></a><span data-ttu-id="8f9ab-247">Onconnected rozbočovače volána dvakrát při použití jQuery Mobile</span><span class="sxs-lookup"><span data-stu-id="8f9ab-247">OnConnected gets called twice when using jQuery Mobile</span></span>

<span data-ttu-id="8f9ab-248">jQuery Mobile pro `initializePage` funkce vynutí skripty na každé stránce, který se znovu spustí, tedy vytvořit druhé připojení.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-248">jQuery Mobile's `initializePage` function forces the scripts in each page to be re-executed, thus creating a second connection.</span></span> <span data-ttu-id="8f9ab-249">Řešení tohoto problému patří:</span><span class="sxs-lookup"><span data-stu-id="8f9ab-249">Solutions for this issue include:</span></span>

- <span data-ttu-id="8f9ab-250">Zahrňte odkaz na architekturu jQuery Mobile před souboru s kódem JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-250">Include the reference to jQuery Mobile before your JavaScript file.</span></span>
- <span data-ttu-id="8f9ab-251">Zakažte `initializePage` funkce tak, že nastavíte `$.mobile.autoInitializePage = false`.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-251">Disable the `initializePage` function by setting `$.mobile.autoInitializePage = false`.</span></span>
- <span data-ttu-id="8f9ab-252">Počkejte na dokončení inicializace před spuštěním připojení na stránce.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-252">Wait for the page to finish initializing before starting the connection.</span></span>

### <a name="messages-are-delayed-in-silverlight-applications-using-server-sent-events"></a><span data-ttu-id="8f9ab-253">Zprávy jsou zpožděné aplikace Silverlight pomocí události odeslané serverem</span><span class="sxs-lookup"><span data-stu-id="8f9ab-253">Messages are delayed in Silverlight applications using Server Sent Events</span></span>

<span data-ttu-id="8f9ab-254">Zprávy jsou zpožděné při používání serveru odesílání událostí na programu Silverlight.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-254">Messages are delayed when using server sent events on Silverlight.</span></span> <span data-ttu-id="8f9ab-255">Pokud chcete vynutit dlouhý interval dotazování, který se má použít místo toho, použijte následující postupy při zahájení připojení:</span><span class="sxs-lookup"><span data-stu-id="8f9ab-255">To force long polling to be used instead, use the following when starting the connection:</span></span>

[!code-css[Main](troubleshooting/samples/sample13.css)]

### <a name="permission-denied-using-forever-frame-protocol"></a><span data-ttu-id="8f9ab-256">Použití "Oprávnění odepřeno" navždy rámce protokolu</span><span class="sxs-lookup"><span data-stu-id="8f9ab-256">"Permission Denied" using Forever Frame protocol</span></span>

<span data-ttu-id="8f9ab-257">Jedná se o známý problém, popsané [tady](https://github.com/SignalR/SignalR/issues/1963).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-257">This is a known issue, described [here](https://github.com/SignalR/SignalR/issues/1963).</span></span> <span data-ttu-id="8f9ab-258">Tento příznak může zobrazit pomocí nejnovější verze knihovny JQuery; Alternativním řešením je na nižší verzi vaší aplikace do JQuery 1.8.2.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-258">This symptom may be seen using the latest JQuery library; the workaround is to downgrade your application to JQuery 1.8.2.</span></span>

<a id="server"></a>

## <a name="compilation-and-server-side-errors"></a><span data-ttu-id="8f9ab-259">Chyby kompilace a na straně serveru</span><span class="sxs-lookup"><span data-stu-id="8f9ab-259">Compilation and server-side errors</span></span>

 <span data-ttu-id="8f9ab-260">Následující část obsahuje možná řešení kompilátoru a chyb za běhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-260">The following section contains possible solutions to compiler and server-side runtime errors.</span></span> 

### <a name="reference-to-hub-instance-is-null"></a><span data-ttu-id="8f9ab-261">Odkaz na instanci rozbočovače má hodnotu null</span><span class="sxs-lookup"><span data-stu-id="8f9ab-261">Reference to Hub instance is null</span></span>

<span data-ttu-id="8f9ab-262">Vzhledem k tomu, že pro každé připojení se vytvoří instanci rozbočovače, je nelze vytvořit instanci rozbočovače ve vašem kódu sami.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-262">Since a hub instance is created for each connection, you can't create an instance of a hub in your code yourself.</span></span> <span data-ttu-id="8f9ab-263">Volání metody v rozbočovači z mimo samotný centra, najdete v článku [klienta volat metody a Správa skupiny mimo třídy rozbočovače](../guide-to-the-api/hubs-api-guide-server.md#callfromoutsidehub) pokyny k získání odkazu na kontext rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-263">To call methods on a hub from outside the hub itself, see [How to call client methods and manage groups from outside the Hub class](../guide-to-the-api/hubs-api-guide-server.md#callfromoutsidehub) for how to obtain a reference to the hub context.</span></span>

### <a name="httpcontextcurrentsession-is-null"></a><span data-ttu-id="8f9ab-264">HTTPContext.Current.Session má hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-264">HTTPContext.Current.Session is null</span></span>

<span data-ttu-id="8f9ab-265">Toto chování je záměrné.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-265">This behavior is by design.</span></span> <span data-ttu-id="8f9ab-266">Funkce SignalR stavu relace ASP.NET nepodporuje, protože stav relace povolení by narušil duplexní zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-266">SignalR does not support the ASP.NET session state, since enabling the session state would break duplex messaging.</span></span>

### <a name="no-suitable-method-to-override"></a><span data-ttu-id="8f9ab-267">Žádná vhodná metoda k přepsání</span><span class="sxs-lookup"><span data-stu-id="8f9ab-267">No suitable method to override</span></span>

<span data-ttu-id="8f9ab-268">Tato chyba může zobrazit, pokud používáte kód ze starší dokumentaci nebo v blozích.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-268">You may see this error if you are using code from older documentation or blogs.</span></span> <span data-ttu-id="8f9ab-269">Ověřte, že nejsou odkazující na názvy metod, které se změnily nebo zastaralý (jako je `OnConnectedAsync`).</span><span class="sxs-lookup"><span data-stu-id="8f9ab-269">Verify that you are not referencing names of methods that have been changed or deprecated (like `OnConnectedAsync`).</span></span>

### <a name="hostcontextextensionswebsocketserverurl-is-null"></a><span data-ttu-id="8f9ab-270">HostContextExtensions.WebSocketServerUrl má hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-270">HostContextExtensions.WebSocketServerUrl is null</span></span>

<span data-ttu-id="8f9ab-271">Toto chování je záměrné.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-271">This behavior is by design.</span></span> <span data-ttu-id="8f9ab-272">Tento člen je zastaralý a neměl by se používat.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-272">This member is deprecated and should not be used.</span></span>

### <a name="a-route-named-signalrhubs-is-already-in-the-route-collection-error"></a><span data-ttu-id="8f9ab-273">Trasa s názvem "signalr.hubs" Chyba "není již v kolekci tras"</span><span class="sxs-lookup"><span data-stu-id="8f9ab-273">"A route named 'signalr.hubs' is already in the route collection" error</span></span>

<span data-ttu-id="8f9ab-274">Tato chyba se zobrazí, pokud `MapHubs` je volán dvakrát vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-274">This error will be seen if `MapHubs` is called twice by your application.</span></span> <span data-ttu-id="8f9ab-275">Některé aplikace volání příklad `MapHubs` přímo v souboru global aplikace; ostatní může volat v obálkovou třídu.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-275">Some example applications call `MapHubs` directly in the global application file; others make the call in a wrapper class.</span></span> <span data-ttu-id="8f9ab-276">Ujistěte se, že vaše aplikace není proveďte obojí.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-276">Ensure that your application does not do both.</span></span>

<a id="vs"></a>

## <a name="visual-studio-issues"></a><span data-ttu-id="8f9ab-277">Problémy v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f9ab-277">Visual Studio issues</span></span>

<span data-ttu-id="8f9ab-278">Tato část popisuje problémy v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-278">This section describes issues encountered in Visual Studio.</span></span>

### <a name="script-documents-node-does-not-appear-in-solution-explorer"></a><span data-ttu-id="8f9ab-279">Uzlu dokumenty skriptu se nezobrazují v Průzkumníku řešení</span><span class="sxs-lookup"><span data-stu-id="8f9ab-279">Script Documents node does not appear in Solution Explorer</span></span>

<span data-ttu-id="8f9ab-280">Některé z našich kurzů nasměrujeme na uzlu "Dokumenty skriptu" v Průzkumníku řešení během ladění.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-280">Some of our tutorials direct you to the "Script Documents" node in Solution Explorer while debugging.</span></span> <span data-ttu-id="8f9ab-281">Tento uzel je vytvořen pomocí ladicího programu jazyka JavaScript a zobrazí se pouze při ladění klienty prohlížeče v Internet Exploreru; uzel se nezobrazí, pokud se používají Chrome nebo Firefox.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-281">This node is produced by the JavaScript debugger, and will only appear while debugging browser clients in Internet Explorer; the node will not appear if Chrome or Firefox are used.</span></span> <span data-ttu-id="8f9ab-282">Ladicí program jazyka JavaScript také nespustí, pokud je spuštěn jiný ladicí program klienta, jako je například ladicí program Silverlight.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-282">The JavaScript debugger will also not run if another client debugger is running, such as the Silverlight debugger.</span></span>

### <a name="signalr-does-not-work-on-visual-studio-2008-or-earlier"></a><span data-ttu-id="8f9ab-283">Funkce SignalR nefunguje v sadě Visual Studio 2008 nebo dřívější</span><span class="sxs-lookup"><span data-stu-id="8f9ab-283">SignalR does not work on Visual Studio 2008 or earlier</span></span>

<span data-ttu-id="8f9ab-284">Toto chování je záměrné.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-284">This behavior is by design.</span></span> <span data-ttu-id="8f9ab-285">Funkce SignalR vyžaduje rozhraní .NET Framework 4 nebo novější; Tento postup vyžaduje, aby aplikace knihovnou SignalR vyvíjet v sadě Visual Studio 2010 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-285">SignalR requires .NET Framework 4 or later; this requires that SignalR applications be developed in Visual Studio 2010 or later.</span></span>

<a id="iis"></a>

## <a name="iis-issues"></a><span data-ttu-id="8f9ab-286">Problémy s IIS</span><span class="sxs-lookup"><span data-stu-id="8f9ab-286">IIS issues</span></span>

<span data-ttu-id="8f9ab-287">Tato část obsahuje problémy se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-287">This section contains issues with Internet Information Services.</span></span>

### <a name="web-site-crashes-after-maphubs-call"></a><span data-ttu-id="8f9ab-288">Po volání MapHubs dojde k chybě webu</span><span class="sxs-lookup"><span data-stu-id="8f9ab-288">Web site crashes after MapHubs call</span></span>

<span data-ttu-id="8f9ab-289">Tento problém byl vyřešen v nejnovější verzi systému SignalR.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-289">This issue has been fixed in the latest version of SignalR.</span></span> <span data-ttu-id="8f9ab-290">Ověřte, že používáte nejnovější vydanou verzi SignalR aktualizací pomocí nástroje NuGet instalace.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-290">Verify that you are using the latest released version of SignalR by updating your installation using NuGet.</span></span>

<a id="azure"></a>

## <a name="azure-issues"></a><span data-ttu-id="8f9ab-291">Problémy s Azure</span><span class="sxs-lookup"><span data-stu-id="8f9ab-291">Azure issues</span></span>

<span data-ttu-id="8f9ab-292">Tato část obsahuje problémy s Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-292">This section contains issues with Microsoft Azure.</span></span>

### <a name="messages-are-not-received-through-the-azure-backplane-after-altering-topic-names"></a><span data-ttu-id="8f9ab-293">Nejsou přijaty zprávy přes Azure propojovací rozhraní systému po změně názvy témat</span><span class="sxs-lookup"><span data-stu-id="8f9ab-293">Messages are not received through the Azure backplane after altering topic names</span></span>

<span data-ttu-id="8f9ab-294">Témata používá Azure propojovacího rozhraní nemají být uživatelem konfigurovatelné.</span><span class="sxs-lookup"><span data-stu-id="8f9ab-294">The topics used by the Azure backplane are not intended to be user-configurable.</span></span>
