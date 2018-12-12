---
uid: signalr/overview/performance/signalr-performance
title: Výkon aplikace SignalR | Dokumentace Microsoftu
author: pfletcher
description: Výkon aplikace SignalR
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 3751f5e7-59db-4be0-a290-50abc24e5c84
msc.legacyurl: /signalr/overview/performance/signalr-performance
msc.type: authoredcontent
ms.openlocfilehash: 9346f0ff9720361f07afe196f59305f0f38ffe8a
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287766"
---
<a name="signalr-performance"></a><span data-ttu-id="ee7e8-103">Výkon aplikace SignalR</span><span class="sxs-lookup"><span data-stu-id="ee7e8-103">SignalR Performance</span></span>
====================
<span data-ttu-id="ee7e8-104">podle [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="ee7e8-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="ee7e8-105">Toto téma popisuje, jak navrhovat pro měření a zlepšit výkon aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-105">This topic describes how to design for, measure, and improve performance in a SignalR application.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="ee7e8-106">Verze softwaru použitým v tomto tématu</span><span class="sxs-lookup"><span data-stu-id="ee7e8-106">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="ee7e8-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="ee7e8-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="ee7e8-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="ee7e8-108">.NET 4.5</span></span>
> - <span data-ttu-id="ee7e8-109">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="ee7e8-109">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="ee7e8-110">Předchozích verzích tohoto tématu</span><span class="sxs-lookup"><span data-stu-id="ee7e8-110">Previous versions of this topic</span></span>
>
> <span data-ttu-id="ee7e8-111">Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-111">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="ee7e8-112">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="ee7e8-112">Questions and comments</span></span>
>
> <span data-ttu-id="ee7e8-113">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-113">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="ee7e8-114">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-114">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="ee7e8-115">Poslední prezentace na výkon aplikace SignalR a škálování najdete v části [škálování webu v reálném čase s knihovnou ASP.NET SignalR](https://channel9.msdn.com/Events/Build/2013/3-502).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-115">For a recent presentation on SignalR performance and scaling, see [Scaling the Real-time Web with ASP.NET SignalR](https://channel9.msdn.com/Events/Build/2013/3-502).</span></span>

<span data-ttu-id="ee7e8-116">Toto téma obsahuje následující oddíly:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-116">This topic contains the following sections:</span></span>

- [<span data-ttu-id="ee7e8-117">Aspekty návrhu</span><span class="sxs-lookup"><span data-stu-id="ee7e8-117">Design considerations</span></span>](#design)
- [<span data-ttu-id="ee7e8-118">Ladění serveru funkce SignalR pro výkon</span><span class="sxs-lookup"><span data-stu-id="ee7e8-118">Tuning your SignalR server for performance</span></span>](#tuning)
- [<span data-ttu-id="ee7e8-119">Řešení potíží s problémy s výkonem</span><span class="sxs-lookup"><span data-stu-id="ee7e8-119">Troubleshooting performance issues</span></span>](#troubleshooting)
- [<span data-ttu-id="ee7e8-120">Použití čítačů výkonu SignalR</span><span class="sxs-lookup"><span data-stu-id="ee7e8-120">Using SignalR performance counters</span></span>](#perfcounters)
- [<span data-ttu-id="ee7e8-121">Použití dalších čítačů výkonu</span><span class="sxs-lookup"><span data-stu-id="ee7e8-121">Using other performance counters</span></span>](#othercounters)
- [<span data-ttu-id="ee7e8-122">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="ee7e8-122">Other resources</span></span>](#otherresources)

<a id="design"></a>

## <a name="design-considerations"></a><span data-ttu-id="ee7e8-123">Aspekty návrhu</span><span class="sxs-lookup"><span data-stu-id="ee7e8-123">Design considerations</span></span>

<span data-ttu-id="ee7e8-124">Tato část popisuje vzory, které je možné implementovat při návrhu aplikace SignalR, ujistěte se, že není právě bránit výkon generování nepotřebný síťový provoz.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-124">This section describes patterns that can be implemented during the design of a SignalR application, to ensure that performance is not being hindered by generating unnecessary network traffic.</span></span>

### <a name="throttling-message-frequency"></a><span data-ttu-id="ee7e8-125">Omezování četnosti zprávy</span><span class="sxs-lookup"><span data-stu-id="ee7e8-125">Throttling message frequency</span></span>

<span data-ttu-id="ee7e8-126">I v aplikaci, která odesílá zprávy s vysokou frekvencí (jako například herních aplikací v reálném čase) nemusíte většinu aplikací na více než několik zpráv za sekundu.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-126">Even in an application that sends out messages at a high frequency (such as a realtime gaming application), most applications don't need to send more than a few messages a second.</span></span> <span data-ttu-id="ee7e8-127">Pokud chcete snížit objem provozu, který generuje každý klient, je možné implementovat smyčku zpráv, fronty a odesílá zprávy žádné další často než Pevná sazba (tedy až po určitém počtu zpráv se odešle, každou sekundu, pokud nejsou zprávy v dané chvíli ve terval k odeslání).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-127">To reduce the amount of traffic that each client generates, a message loop can be implemented that queues and sends out messages no more frequently than a fixed rate (that is, up to a certain number of messages will be sent every second, if there are messages in that time interval to be sent).</span></span> <span data-ttu-id="ee7e8-128">Ukázková aplikace, která omezuje zpráv pro určitou míru (z klientských i serverových), najdete v části [vysokofrekvenční Reálný čas s knihovnou SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-128">For a sample application that throttles messages to a certain rate (from both client and server), see [High-Frequency Realtime with SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).</span></span>

### <a name="reducing-message-size"></a><span data-ttu-id="ee7e8-129">Omezení velikosti zpráv</span><span class="sxs-lookup"><span data-stu-id="ee7e8-129">Reducing message size</span></span>

<span data-ttu-id="ee7e8-130">Díky snížení objemu Serializované objekty můžete zmenšit velikost zpráv SignalR.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-130">You can reduce the size of a SignalR message by reducing the size of your serialized objects.</span></span> <span data-ttu-id="ee7e8-131">V serverovém kódu, pokud odesíláte objekt, který obsahuje vlastnosti, které nemusíte přenášet, zabránit tyto vlastnosti serializována pomocí `JsonIgnore` atribut.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-131">In server code, if you're sending an object that contains properties that don't need to be transmitted, prevent those properties from being serialized by using the `JsonIgnore` attribute.</span></span> <span data-ttu-id="ee7e8-132">Názvy vlastností jsou také uloženy ve zprávě; názvy vlastností lze zkrátit použitím `JsonProperty` atribut.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-132">The names of properties are also stored in the message; the names of properties can be shortened using the `JsonProperty` attribute.</span></span> <span data-ttu-id="ee7e8-133">Následující vzorový kód ukazuje, jak být vyloučena určitá vlastnost nezabránil odesílání do klienta a jak zkrátit názvy vlastností:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-133">The following code sample demonstrates how to exclude a property from being sent to the client, and how to shorten property names:</span></span>

<span data-ttu-id="ee7e8-134">**Server kódu .NET, který ukazuje atribut JsonIgnore k vyloučení dat odesílaných do klienta a atribut JsonProperty snížit velikost zprávy**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-134">**.NET server code that demonstrates the JsonIgnore attribute to exclude data from being sent to the client, and the JsonProperty attribute to reduce message size**</span></span>

[!code-csharp[Main](signalr-performance/samples/sample1.cs?highlight=5,7,10)]

<span data-ttu-id="ee7e8-135">Aby bylo možné zachovat čitelnost / udržovatelnosti kódu klienta, názvy zkrácený vlastností, které může být změnu mapování na lidské vhodných názvů, po doručení zprávy.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-135">In order to retain readability/ maintainability in the client code, the abbreviated property names can be remapped to human-friendly names after the message is received.</span></span> <span data-ttu-id="ee7e8-136">Následující příklad kódu ukazuje jeden možný způsob, jak přemapování zkrácené názvy delší ty, které jsou tak, že definujete kontraktu zprávy (mapování) a pomocí `reMap` funkce má být použita kontrakt třídu optimalizované zpráv:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-136">The following code sample demonstrates one possible way of remapping shortened names to longer ones, by defining a message contract (mapping), and using the `reMap` function to apply the contract to the optimized message class:</span></span>

<span data-ttu-id="ee7e8-137">**Kód jazyka JavaScript na straně klienta, který změní zkrátila názvy vlastností na názvy v lidsky čitelném**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-137">**Client-side JavaScript code that remaps shortened property names to human-readable names**</span></span>

[!code-javascript[Main](signalr-performance/samples/sample2.js)]

<span data-ttu-id="ee7e8-138">Názvy lze zkrátit ve zprávách z klienta na server, stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-138">Names can be shortened in messages from the client to the server as well, using the same method.</span></span>

<span data-ttu-id="ee7e8-139">Snižuje nároky na paměť (to znamená, množství paměti používané pro zprávu) zprávy může objekt také zvýšit výkon.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-139">Reducing the memory footprint (that is, the amount of memory used for the message) of the message object can also improve performance.</span></span> <span data-ttu-id="ee7e8-140">Například pokud celou škálu `int` není potřeba, `short` nebo `byte` lze použít.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-140">For example, if the full range of an `int` is not needed, a `short` or `byte` can be used instead.</span></span>

<span data-ttu-id="ee7e8-141">Protože zprávy jsou uloženy ve sběrnici zpráv v paměti serveru nezmenšit velikost této zprávy můžete také problémy s pamětí adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-141">Since messages are stored in the message bus in server memory, reducing the size of messages can also address server memory issues.</span></span>

<a id="tuning"></a>

### <a name="tuning-your-signalr-server-for-performance"></a><span data-ttu-id="ee7e8-142">Ladění serveru funkce SignalR pro výkon</span><span class="sxs-lookup"><span data-stu-id="ee7e8-142">Tuning your SignalR server for performance</span></span>

<span data-ttu-id="ee7e8-143">Následující nastavení konfigurace slouží k ladění serveru pro vyšší výkon aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-143">The following configuration settings can be used to tune your server for better performance in a SignalR application.</span></span> <span data-ttu-id="ee7e8-144">Obecné informace o tom, jak zlepšit výkon v aplikaci ASP.NET najdete v tématu [zlepšení výkonu technologie ASP.NET](https://msdn.microsoft.com/library/ff647787.aspx).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-144">For general information on how to improve performance in an ASP.NET application, see [Improving ASP.NET Performance](https://msdn.microsoft.com/library/ff647787.aspx).</span></span>

<span data-ttu-id="ee7e8-145">**Nastavení konfigurace SignalR**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-145">**SignalR configuration settings**</span></span>

- <span data-ttu-id="ee7e8-146">**DefaultMessageBufferSize**: Ve výchozím nastavení zachová SignalR 1000 zpráv v paměti za rozbočovač za připojení.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-146">**DefaultMessageBufferSize**: By default, SignalR retains 1000 messages in memory per hub per connection.</span></span> <span data-ttu-id="ee7e8-147">Pokud velké zprávy se používají, to může způsobit problémy s pamětí, které můžete zmírnit tím, že snižuje tuto hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-147">If large messages are being used, this may create memory issues which can be alleviated by reducing this value.</span></span> <span data-ttu-id="ee7e8-148">Toto nastavení je možné nastavit v `Application_Start` obslužné rutiny události v aplikaci ASP.NET nebo `Configuration` metody třídy pro spuštění OWIN v místním prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-148">This setting can be set in the `Application_Start` event handler in an ASP.NET application, or in the `Configuration` method of an OWIN startup class in a self-hosted application.</span></span> <span data-ttu-id="ee7e8-149">Následující příklad ukazuje, jak snížit za účelem snížení paměťových nároků vaší aplikace za účelem snížení množství paměti serveru používá tuto hodnotu:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-149">The following sample demonstrates how to reduce this value in order to reduce the memory footprint of your application in order to reduce the amount of server memory used:</span></span>

    <span data-ttu-id="ee7e8-150">**Server kódu .NET v souboru Startup.cs pro snížení výchozí velikost vyrovnávací paměti zpráv**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-150">**.NET server code in Startup.cs for decreasing default message buffer size**</span></span>

    [!code-csharp[Main](signalr-performance/samples/sample3.cs)]

<span data-ttu-id="ee7e8-151">**Nastavení konfigurace služby IIS**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-151">**IIS configuration settings**</span></span>

- <span data-ttu-id="ee7e8-152">**Maximální počet souběžných požadavků na aplikaci**: Navýšení počtu souběžných IIS zvýší požadavky na prostředky serveru k dispozici obsluze žádostí.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-152">**Max concurrent requests per application**: Increasing the number of concurrent IIS requests will increase server resources available for serving requests.</span></span> <span data-ttu-id="ee7e8-153">Výchozí hodnota je 5 000; Pokud chcete zvýšit toto nastavení, spusťte následující příkazy v příkazovém řádku se zvýšenými oprávněními:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-153">The default value is 5000; to increase this setting, execute the following commands in an elevated command prompt:</span></span>

    [!code-console[Main](signalr-performance/samples/sample4.cmd)]
- <span data-ttu-id="ee7e8-154">**ApplicationPool QueueLength**: Toto je maximální počet požadavků, ovladač Http.sys zařadí do fronty pro fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-154">**ApplicationPool QueueLength**: This is the maximum number of requests that Http.sys queues for the application pool.</span></span> <span data-ttu-id="ee7e8-155">Po zaplnění fronty, nové požadavky obdrží odpověď 503 – Služba není dostupná".</span><span class="sxs-lookup"><span data-stu-id="ee7e8-155">When the queue is full, new requests receive a 503 "Service Unavailable" response.</span></span> <span data-ttu-id="ee7e8-156">Výchozí hodnota je 1000.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-156">The default value is 1000.</span></span>

    <span data-ttu-id="ee7e8-157">Zkrácení délky fronty pro pracovní proces ve fondu aplikací, který je hostitelem vaší aplikace bude šetřit prostředky paměti.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-157">Shortening the queue length for the worker process in the application pool hosting your application will conserve memory resources.</span></span> <span data-ttu-id="ee7e8-158">Další informace najdete v tématu [Správa, ladění a konfigurace fondů aplikací](https://technet.microsoft.com/library/cc745955.aspx).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-158">For more information, see [Managing, Tuning, and Configuring Application Pools](https://technet.microsoft.com/library/cc745955.aspx).</span></span>

<span data-ttu-id="ee7e8-159">**Konfigurace nastavení ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-159">**ASP.NET configuration settings**</span></span>

<span data-ttu-id="ee7e8-160">Tato část obsahuje nastavení konfigurace, které je možné nastavit v `aspnet.config` souboru.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-160">This section includes configuration settings that can be set in the `aspnet.config` file.</span></span> <span data-ttu-id="ee7e8-161">Tento soubor se nachází v jednom ze dvou umístění, v závislosti na platformě:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-161">This file is found in one of two locations, depending on platform:</span></span>

- `%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet.config`
- `%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet.config`

<span data-ttu-id="ee7e8-162">Nastavení technologie ASP.NET, které mohou zlepšit výkon aplikace SignalR patří:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-162">ASP.NET settings that may improve SignalR performance include the following:</span></span>

- <span data-ttu-id="ee7e8-163">**Maximální počet souběžných požadavků za procesoru**: Zvýšením tohoto nastavení může zmírnění výkonnostní kritické body.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-163">**Maximum concurrent requests per CPU**: Increasing this setting may alleviate performance bottlenecks.</span></span> <span data-ttu-id="ee7e8-164">Ke zvýšení tohoto nastavení, přidejte následující nastavení konfigurace `aspnet.config` souboru:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-164">To increase this setting, add the following configuration setting to the `aspnet.config` file:</span></span>

    [!code-xml[Main](signalr-performance/samples/sample5.xml?highlight=4)]
- <span data-ttu-id="ee7e8-165">**Limit fronty požadavků**: Pokud překročí celkový počet připojení `maxConcurrentRequestsPerCPU` nastavení technologie ASP.NET se spustí omezování požadavků pomocí fronty.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-165">**Request Queue Limit**: When the total number of connections exceeds the `maxConcurrentRequestsPerCPU` setting, ASP.NET will start throttling requests using a queue.</span></span> <span data-ttu-id="ee7e8-166">Pokud chcete zvýšit velikost fronty, můžete zvýšit `requestQueueLimit` nastavení.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-166">To increase the size of the queue, you can increase the `requestQueueLimit` setting.</span></span> <span data-ttu-id="ee7e8-167">Provedete to tak, přidejte následující nastavení konfigurace `processModel` uzel v `config/machine.config` (spíše než `aspnet.config`):</span><span class="sxs-lookup"><span data-stu-id="ee7e8-167">To do this, add the following configuration setting to the `processModel` node in `config/machine.config` (rather than `aspnet.config`):</span></span>

    [!code-xml[Main](signalr-performance/samples/sample6.xml)]

<a id="troubleshooting"></a>

## <a name="troubleshooting-performance-issues"></a><span data-ttu-id="ee7e8-168">Řešení potíží s problémy s výkonem</span><span class="sxs-lookup"><span data-stu-id="ee7e8-168">Troubleshooting performance issues</span></span>

<span data-ttu-id="ee7e8-169">Tato část popisuje, jak najít kritické body výkonu ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-169">This section describes ways to find performance bottlenecks in your application.</span></span>

### <a name="verifying-that-websocket-is-being-used"></a><span data-ttu-id="ee7e8-170">Ověřuje se, že se používá protokol WebSocket</span><span class="sxs-lookup"><span data-stu-id="ee7e8-170">Verifying that WebSocket is being used</span></span>

<span data-ttu-id="ee7e8-171">Zatímco SignalR můžete použít celou řadu přenosů pro komunikaci mezi klientem a serverem, objektu websocket na straně nabízí výhody výkonu a by měla sloužit, pokud klient a server podporovat.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-171">While SignalR can use a variety of transports for communication between client and server, WebSocket offers a significant performance advantage, and should be used if the client and server support it.</span></span> <span data-ttu-id="ee7e8-172">Pokud chcete zjistit, pokud klient a server splňovat požadavky protokolu websocket, naleznete v tématu [přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-172">To determine if your client and server meet the requirements for WebSocket, see [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span> <span data-ttu-id="ee7e8-173">Pokud chcete zjistit, jaký přenos se používá ve vaší aplikaci, můžete pomocí vývojářských nástrojů prohlížeče a zkontrolujte protokoly chcete zobrazit, jaký přenos se používá pro připojení.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-173">To determine what transport is being used in your application, you can use the browser developer tools, and examine the logs to see what transport is being used for the connection.</span></span> <span data-ttu-id="ee7e8-174">Informace o používání vývojářských nástrojů prohlížeče v aplikaci Internet Explorer a Chrome, naleznete v tématu [přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-174">For information on using the browser development tools in Internet Explorer and Chrome, see [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="perfcounters"></a>

## <a name="using-signalr-performance-counters"></a><span data-ttu-id="ee7e8-175">Použití čítačů výkonu SignalR</span><span class="sxs-lookup"><span data-stu-id="ee7e8-175">Using SignalR performance counters</span></span>

<span data-ttu-id="ee7e8-176">Tato část popisuje, jak povolit a použití čítačů výkonu SignalR, součástí `Microsoft.AspNet.SignalR.Utils` balíčku.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-176">This section describes how to enable and use SignalR performance counters, found in the `Microsoft.AspNet.SignalR.Utils` package.</span></span>

### <a name="installing-signalrexe"></a><span data-ttu-id="ee7e8-177">Instalace signalr.exe</span><span class="sxs-lookup"><span data-stu-id="ee7e8-177">Installing signalr.exe</span></span>

<span data-ttu-id="ee7e8-178">Čítače výkonu lze přidat na server pomocí nástroje volá SignalR.exe.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-178">Performance counters can be added to the server using a utility called SignalR.exe.</span></span> <span data-ttu-id="ee7e8-179">Chcete-li nainstalovat tento nástroj, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-179">To install this utility, follow these steps:</span></span>

1. <span data-ttu-id="ee7e8-180">V aplikaci Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **spravovat balíčky NuGet pro řešení**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-180">In Visual Studio, select **Tools** > **NuGet Package Manager** > **Manage NuGet Packages for Solution**</span></span>
2. <span data-ttu-id="ee7e8-181">Vyhledejte **signalr.utils**a kliknout na tlačítko nainstalovat.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-181">Search for **signalr.utils**, and select Install.</span></span>

    ![](signalr-performance/_static/image1.png)
3. <span data-ttu-id="ee7e8-182">Přijmout podmínky licenční smlouvy k instalaci balíčku.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-182">Accept the license agreement to install the package.</span></span>
4. <span data-ttu-id="ee7e8-183">Bude možné SignalR.exe `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-183">SignalR.exe will be installed to `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.</span></span>

### <a name="installing-performance-counters-with-signalrexe"></a><span data-ttu-id="ee7e8-184">Instalace čítačů výkonu s SignalR.exe</span><span class="sxs-lookup"><span data-stu-id="ee7e8-184">Installing performance counters with SignalR.exe</span></span>

<span data-ttu-id="ee7e8-185">K instalaci čítačů výkonu SignalR, spusťte v příkazovém řádku se zvýšenými oprávněními následující parametr SignalR.exe:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-185">To install SignalR performance counters, run SignalR.exe in an elevated command prompt with the following parameter:</span></span>

[!code-console[Main](signalr-performance/samples/sample7.cmd)]

<span data-ttu-id="ee7e8-186">Pokud chcete odebrat čítačů výkonu SignalR, spusťte v příkazovém řádku se zvýšenými oprávněními následující parametr SignalR.exe:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-186">To remove SignalR performance counters, run SignalR.exe in an elevated command prompt with the following parameter:</span></span>

[!code-console[Main](signalr-performance/samples/sample8.cmd)]

### <a name="signalr-performance-counters"></a><span data-ttu-id="ee7e8-187">Čítače výkonu SignalR</span><span class="sxs-lookup"><span data-stu-id="ee7e8-187">SignalR Performance counters</span></span>

<span data-ttu-id="ee7e8-188">Nástroje pro balíček nainstaluje následující čítače výkonu.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-188">The utilities package installs the following performance counters.</span></span> <span data-ttu-id="ee7e8-189">"Celkový" čítače měření počtu událostí od poslední fond aplikací nebo server restartovat.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-189">The "Total" counters measure the number of events since the last application pool or server restart.</span></span>

<span data-ttu-id="ee7e8-190">**Metrik připojení**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-190">**Connection metrics**</span></span>

<span data-ttu-id="ee7e8-191">Následující metriky měření události životnost připojení, ke kterým dochází.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-191">The following metrics measure the connection lifetime events that occur.</span></span> <span data-ttu-id="ee7e8-192">Další informace najdete v tématu [principy a zpracování událostí doby platnosti](../guide-to-the-api/handling-connection-lifetime-events.md).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-192">For more information, see [Understanding and Handling Connection Lifetime Events](../guide-to-the-api/handling-connection-lifetime-events.md).</span></span>

- <span data-ttu-id="ee7e8-193">**Připojení připojeno**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-193">**Connections Connected**</span></span>
- <span data-ttu-id="ee7e8-194">**Připojení znovu připojeny**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-194">**Connections Reconnected**</span></span>
- <span data-ttu-id="ee7e8-195">**Odpojené připojení**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-195">**Connections Disconnected**</span></span>
- <span data-ttu-id="ee7e8-196">**Aktuální připojení**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-196">**Connections Current**</span></span>

<span data-ttu-id="ee7e8-197">**Metriky zpráv**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-197">**Message metrics**</span></span>

<span data-ttu-id="ee7e8-198">Následující metriky měření přenos zpráv generované systémem SignalR.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-198">The following metrics measure the message traffic generated by SignalR.</span></span>

- <span data-ttu-id="ee7e8-199">**Připojení celkový počet přijatých zpráv**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-199">**Connection Messages Received Total**</span></span>
- <span data-ttu-id="ee7e8-200">**Připojení zprávy odeslané celkem**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-200">**Connection Messages Sent Total**</span></span>
- <span data-ttu-id="ee7e8-201">**Přijaté zprávy připojení/s**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-201">**Connection Messages Received/Sec**</span></span>
- <span data-ttu-id="ee7e8-202">**Odeslání zprávy připojení za sekundu**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-202">**Connection Messages Sent/Sec**</span></span>

<span data-ttu-id="ee7e8-203">**Metriky zpráv Service bus**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-203">**Message bus metrics**</span></span>

<span data-ttu-id="ee7e8-204">Následující metriky měření provoz přes vnitřní sběrnice zpráv SignalR, fronta, ve které se umístí všechny příchozí a odchozí zprávy SignalR.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-204">The following metrics measure traffic through the internal SignalR message bus, the queue in which all incoming and outgoing SignalR messages are placed.</span></span> <span data-ttu-id="ee7e8-205">Zpráva se **publikováno** , pokud je odeslána nebo všesměrového vysílání.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-205">A message is **Published** when it is sent or broadcast.</span></span> <span data-ttu-id="ee7e8-206">A **odběratele** v tomto kontextu je předplatné na sběrnici zpráv; to by měl být roven počtu klientů a samotný server.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-206">A **Subscriber** in this context is a subscription on the message bus; this should equal the number of clients plus the server itself.</span></span> <span data-ttu-id="ee7e8-207">**Pracovních procesů přidělených** je komponenta, která odesílá data do aktivních připojení; **zaneprázdněný pracovního procesu** je ten, který je aktivně odesílá zprávu.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-207">An **Allocated Worker** is a component that sends data to active connections; a **Busy Worker** is one that is actively sending a message.</span></span>

- <span data-ttu-id="ee7e8-208">**Celkový počet přijatých zpráv sběrnice zpráv**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-208">**Message Bus Messages Received Total**</span></span>
- <span data-ttu-id="ee7e8-209">**Sběrnice zpráv zprávy přijaté za sekundu**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-209">**Message Bus Messages Received/Sec**</span></span>
- <span data-ttu-id="ee7e8-210">**Zpráva Service Bus zprávy publikované celkem**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-210">**Message Bus Messages Published Total**</span></span>
- <span data-ttu-id="ee7e8-211">**Sběrnice zpráv zprávy publikované za sekundu**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-211">**Message Bus Messages Published/Sec**</span></span>
- <span data-ttu-id="ee7e8-212">**Aktuální předplatitelé zpráv Service Bus**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-212">**Message Bus Subscribers Current**</span></span>
- <span data-ttu-id="ee7e8-213">**Celkový počet zpráv Service Bus předplatitele**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-213">**Message Bus Subscribers Total**</span></span>
- <span data-ttu-id="ee7e8-214">**Předplatitelé sběrnice zpráv za sekundu**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-214">**Message Bus Subscribers/Sec**</span></span>
- <span data-ttu-id="ee7e8-215">**Sběrnice zpráv přidělených pracovních procesů**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-215">**Message Bus Allocated Workers**</span></span>
- <span data-ttu-id="ee7e8-216">**Service Bus zprávu vytížených pracovních procesů**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-216">**Message Bus Busy Workers**</span></span>
- <span data-ttu-id="ee7e8-217">**Aktuální témata sběrnice zpráv**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-217">**Message Bus Topics Current**</span></span>

<span data-ttu-id="ee7e8-218">**Chyba metriky**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-218">**Error metrics**</span></span>

<span data-ttu-id="ee7e8-219">Následující metriky měření chyby vygenerované nástrojem přenos zpráv SignalR.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-219">The following metrics measure errors generated by SignalR message traffic.</span></span> <span data-ttu-id="ee7e8-220">**Centrum řešení** dojít k chybám při rozbočovače a metody rozbočovače nelze rozpoznat.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-220">**Hub Resolution** errors occur when a hub or hub method cannot be resolved.</span></span> <span data-ttu-id="ee7e8-221">**Volání rozbočovače** chyby jsou výjimky vyvolané při volání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-221">**Hub Invocation** errors are exceptions thrown while invoking a hub method.</span></span> <span data-ttu-id="ee7e8-222">**Přenos** chyby jsou chyby připojení vyvolána během požadavku HTTP nebo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-222">**Transport** errors are connection errors thrown during an HTTP request or response.</span></span>

- <span data-ttu-id="ee7e8-223">**Chyby: Všechny celkem**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-223">**Errors: All Total**</span></span>
- <span data-ttu-id="ee7e8-224">**Chyby: Vše za sekundu**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-224">**Errors: All/Sec**</span></span>
- <span data-ttu-id="ee7e8-225">**Chyby: Centrum řešení celkem**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-225">**Errors: Hub Resolution Total**</span></span>
- <span data-ttu-id="ee7e8-226">**Chyby: Centrum řešení za sekundu**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-226">**Errors: Hub Resolution/Sec**</span></span>
- <span data-ttu-id="ee7e8-227">**Chyby: Celkový počet volání rozbočovače**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-227">**Errors: Hub Invocation Total**</span></span>
- <span data-ttu-id="ee7e8-228">**Chyby: Volání rozbočovače za sekundu**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-228">**Errors: Hub Invocation/Sec**</span></span>
- <span data-ttu-id="ee7e8-229">**Chyby: Celkový přenos**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-229">**Errors: Transport Total**</span></span>
- <span data-ttu-id="ee7e8-230">**Chyby: Přenos za sekundu**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-230">**Errors: Transport/Sec**</span></span>

<a id="scaleout_metrics"></a>

<span data-ttu-id="ee7e8-231">**Metriky horizontálním navýšením kapacity**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-231">**Scaleout metrics**</span></span>

<span data-ttu-id="ee7e8-232">Následující metriky měření provoz a chyby vygenerované zprostředkovatelem o škálování.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-232">The following metrics measure traffic and errors generated by the scaleout provider.</span></span> <span data-ttu-id="ee7e8-233">A **Stream** v tomto kontextu je jednotka škálování používá zprostředkovatel horizontálním navýšením kapacity; to je tabulka, pokud se používá SQL Server, téma služby Service Bus se používá a předplatné při použití Redis.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-233">A **Stream** in this context is a scale unit used by the scaleout provider; this is a table if SQL Server is used, a Topic if Service Bus is used, and a Subscription if Redis is used.</span></span> <span data-ttu-id="ee7e8-234">Každý datový proud zajišťuje uspořádaný operací čtení a zápisu; jeden datový proud je potenciální kritický bod škálování, proto je možné zvýšit počet datových proudů snížit tento kritický bod.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-234">Each stream ensures ordered read and write operations; a single stream is a potential scale bottleneck, so the number of streams can be increased to help reduce that bottleneck.</span></span> <span data-ttu-id="ee7e8-235">Pokud jsou používány více datové proudy, bude SignalR automaticky distribuovat napříč těchto datových proudů způsobem, který zajistí, že zpráv odeslaných z jakékoli dané připojení jsou v pořadí zpráv (horizontální oddíl).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-235">If multiple streams are used, SignalR will automatically distribute (shard) messages across these streams in a way that ensures messages sent from any given connection are in order.</span></span>

<span data-ttu-id="ee7e8-236">[MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx) nastavení ovládacích prvků délka fronty pro odesílání dat o škálování udržuje SignalR.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-236">The [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx) setting controls the length of the scaleout send queue maintained by SignalR.</span></span> <span data-ttu-id="ee7e8-237">Nastavení na hodnotu větší než 0, umístí všechny zprávy ve frontě odeslat postupně po jednom odeslání do propojovacího rozhraní nakonfigurované pro zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-237">Setting it to a value greater than 0 will place all messages in a send queue to be sent one at a time to the configured messaging backplane.</span></span> <span data-ttu-id="ee7e8-238">Pokud velikost fronty překročí nakonfigurovanou délku, následná volání k odeslání se okamžitě selhat s [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception(v=vs.118).aspx) dokud počet zpráv ve frontě je menší než nastavení znovu.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-238">If the size of the queue goes above the configured length, subsequent calls to send will fail immediately with an [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception(v=vs.118).aspx) until the number of messages in the queue is less than the setting again.</span></span> <span data-ttu-id="ee7e8-239">Zařazení do fronty je ve výchozím nastavení zakázáno, protože implementované backplanes obecně mít své vlastní zařazování do fronty a řízení toku na místě.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-239">Queueing is disabled by default because the implemented backplanes generally have their own queuing or flow-control in place.</span></span> <span data-ttu-id="ee7e8-240">V případě SQL serveru sdružování připojení efektivně omezuje počet odešle děje v daný okamžik.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-240">In the case of SQL Server, connection pooling effectively limits the number of sends going on at any one time.</span></span>

<span data-ttu-id="ee7e8-241">Ve výchozím nastavení, se používá pouze jeden datový proud pro SQL Server a Redis, pět datových proudů se používají pro Service Bus a jejich zařazování do fronty je zakázaná, ale tato nastavení lze změnit prostřednictvím konfigurace na serveru SQL Server a služby Service Bus:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-241">By default, only one stream is used for SQL Server and Redis, five streams are used for Service Bus, and queueing is disabled, but these settings can be changed through configuration on SQL Server and Service Bus:</span></span>

<span data-ttu-id="ee7e8-242">**.NET serverový kód pro konfigurace tabulky count a fronta délku propojovací rozhraní systému SQL Server**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-242">**.NET Server Code for configuring table count and queue length for SQL Server backplane**</span></span>

[!code-csharp[Main](signalr-performance/samples/sample9.cs)]

<span data-ttu-id="ee7e8-243">**.NET serverový kód pro konfiguraci tématu počet a fronta délku pro propojovací Service Bus rozhraní**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-243">**.NET Server Code for configuring topic count and queue length for Service Bus backplane**</span></span>

[!code-csharp[Main](signalr-performance/samples/sample10.cs)]

<span data-ttu-id="ee7e8-244">A **ukládání do vyrovnávací paměti** datový proud je ten, který má zadaný chybovém stavu, když je datový proud v chybovém stavu, všechny zprávy odeslané do propojovacího rozhraní dojde okamžitě, dokud je datový proud již chybující.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-244">A **Buffering** stream is one that has entered a faulted state; when the stream is in the faulted state, all messages sent to the backplane will fail immediately until the stream is no longer faulting.</span></span> <span data-ttu-id="ee7e8-245">**Délku fronty odesílání** je počet zpráv, které byly odeslány, ale dosud nebyl odeslán.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-245">The **Send Queue Length** is the number of messages that have been posted but not yet sent.</span></span>

- <span data-ttu-id="ee7e8-246">**Sběrnice zpráv o škálování zprávy přijaté za sekundu**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-246">**Scaleout Message Bus Messages Received/Sec**</span></span>
- <span data-ttu-id="ee7e8-247">**Celkový počet datových proudů horizontálním navýšením kapacity**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-247">**Scaleout Streams Total**</span></span>
- <span data-ttu-id="ee7e8-248">**Škálování aplikace streamuje Open**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-248">**Scaleout Streams Open**</span></span>
- <span data-ttu-id="ee7e8-249">**Datové proudy dat o škálování, ukládání do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-249">**Scaleout Streams Buffering**</span></span>
- <span data-ttu-id="ee7e8-250">**Celkový počet chyb škálování aplikace**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-250">**Scaleout Errors Total**</span></span>
- <span data-ttu-id="ee7e8-251">**Chyby/s horizontálním navýšením kapacity**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-251">**Scaleout Errors/Sec**</span></span>
- <span data-ttu-id="ee7e8-252">**Délku fronty odesílání horizontálním navýšením kapacity**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-252">**Scaleout Send Queue Length**</span></span>

<span data-ttu-id="ee7e8-253">Další informace o co tyto čítače jsou měření najdete v tématu [škálování aplikace SignalR službou Azure Service Bus](scaleout-with-windows-azure-service-bus.md).</span><span class="sxs-lookup"><span data-stu-id="ee7e8-253">For more information on what these counters are measuring, see [SignalR Scaleout with Azure Service Bus](scaleout-with-windows-azure-service-bus.md).</span></span>

<a id="othercounters"></a>

## <a name="using-other-performance-counters"></a><span data-ttu-id="ee7e8-254">Použití dalších čítačů výkonu</span><span class="sxs-lookup"><span data-stu-id="ee7e8-254">Using other performance counters</span></span>

<span data-ttu-id="ee7e8-255">Následující čítače výkonu může být užitečná při monitorování výkonu aplikace.</span><span class="sxs-lookup"><span data-stu-id="ee7e8-255">The following performance counters may also be useful in monitoring your application's performance.</span></span>

<span data-ttu-id="ee7e8-256">**Paměť**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-256">**Memory**</span></span>

- <span data-ttu-id="ee7e8-257">Paměť .NET CLR\\počet bajtů ve všech haldách (pro w3wp)</span><span class="sxs-lookup"><span data-stu-id="ee7e8-257">.NET CLR Memory\\# bytes in all Heaps (for w3wp)</span></span>

<span data-ttu-id="ee7e8-258">**ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-258">**ASP.NET**</span></span>

- <span data-ttu-id="ee7e8-259">Aktuální ASP.NET\Requests</span><span class="sxs-lookup"><span data-stu-id="ee7e8-259">ASP.NET\Requests Current</span></span>
- <span data-ttu-id="ee7e8-260">ASP.NET\Queued</span><span class="sxs-lookup"><span data-stu-id="ee7e8-260">ASP.NET\Queued</span></span>
- <span data-ttu-id="ee7e8-261">ASP.NET\Rejected</span><span class="sxs-lookup"><span data-stu-id="ee7e8-261">ASP.NET\Rejected</span></span>

<span data-ttu-id="ee7e8-262">**CPU**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-262">**CPU**</span></span>

- <span data-ttu-id="ee7e8-263">Information\Processor času procesoru</span><span class="sxs-lookup"><span data-stu-id="ee7e8-263">Processor Information\Processor Time</span></span>

<span data-ttu-id="ee7e8-264">**TCP/IP**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-264">**TCP/IP**</span></span>

- <span data-ttu-id="ee7e8-265">TCPv6/připojení</span><span class="sxs-lookup"><span data-stu-id="ee7e8-265">TCPv6/Connections Established</span></span>
- <span data-ttu-id="ee7e8-266">TCPv4/připojení</span><span class="sxs-lookup"><span data-stu-id="ee7e8-266">TCPv4/Connections Established</span></span>

<span data-ttu-id="ee7e8-267">**Webová služba**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-267">**Web Service**</span></span>

- <span data-ttu-id="ee7e8-268">Webová Service\Current připojení</span><span class="sxs-lookup"><span data-stu-id="ee7e8-268">Web Service\Current Connections</span></span>
- <span data-ttu-id="ee7e8-269">Webová Service\Maximum připojení</span><span class="sxs-lookup"><span data-stu-id="ee7e8-269">Web Service\Maximum Connections</span></span>

<span data-ttu-id="ee7e8-270">**Dělení na vlákna**</span><span class="sxs-lookup"><span data-stu-id="ee7e8-270">**Threading**</span></span>

- <span data-ttu-id="ee7e8-271">.NET CLR uzamčení a vláken\\počet aktuálních logických podprocesů</span><span class="sxs-lookup"><span data-stu-id="ee7e8-271">.NET CLR Locks And Threads\\# of current logical Threads</span></span>
- <span data-ttu-id="ee7e8-272">.NET CLR uzamčení a vláken\\počet aktuálních fyzických vláken</span><span class="sxs-lookup"><span data-stu-id="ee7e8-272">.NET CLR Locks And Threads\\# of current physical Threads</span></span>

<a id="otherresources"></a>

## <a name="other-resources"></a><span data-ttu-id="ee7e8-273">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ee7e8-273">Other Resources</span></span>

<span data-ttu-id="ee7e8-274">Další informace o monitorování a optimalizace výkonu do technologie ASP.NET naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="ee7e8-274">For more information on ASP.NET performance monitoring and tuning, see the following topics:</span></span>

- <span data-ttu-id="ee7e8-275">[ASP.NET: přehled výkonu](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)</span><span class="sxs-lookup"><span data-stu-id="ee7e8-275">[ASP.NET Performance Overview](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)</span></span>
- [<span data-ttu-id="ee7e8-276">Využití vlákna ASP.NET na IIS 7.5, IIS 7.0 a IIS 6.0</span><span class="sxs-lookup"><span data-stu-id="ee7e8-276">ASP.NET Thread Usage on IIS 7.5, IIS 7.0, and IIS 6.0</span></span>](https://blogs.msdn.com/b/tmarq/archive/2007/07/21/asp-net-thread-usage-on-iis-7-0-and-6-0.aspx)
- [<span data-ttu-id="ee7e8-277">&lt;applicationPool&gt; – Element (nastavení webu)</span><span class="sxs-lookup"><span data-stu-id="ee7e8-277">&lt;applicationPool&gt; Element (Web Settings)</span></span>](https://msdn.microsoft.com/library/dd560842.aspx)
