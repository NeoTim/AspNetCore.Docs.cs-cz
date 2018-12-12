---
uid: signalr/overview/older-versions/signalr-performance
title: Výkon aplikace SignalR (SignalR 1.x) | Dokumentace Microsoftu
author: pfletcher
description: Výkon aplikace SignalR
ms.author: riande
ms.date: 07/03/2013
ms.assetid: 9594d644-66b6-4223-acdd-23e29a6e4c46
msc.legacyurl: /signalr/overview/older-versions/signalr-performance
msc.type: authoredcontent
ms.openlocfilehash: ea2d3908544ac8b3ea17ceceaf1d2905c5c6f322
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287562"
---
<a name="signalr-performance-signalr-1x"></a><span data-ttu-id="bea99-103">Výkon aplikace SignalR (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="bea99-103">SignalR Performance (SignalR 1.x)</span></span>
====================
<span data-ttu-id="bea99-104">podle [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="bea99-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="bea99-105">Toto téma popisuje, jak navrhovat pro měření a zlepšit výkon aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="bea99-105">This topic describes how to design for, measure, and improve performance in a SignalR application.</span></span>


<span data-ttu-id="bea99-106">Poslední prezentace na výkon aplikace SignalR a škálování najdete v části [škálování webu v reálném čase s knihovnou ASP.NET SignalR](https://channel9.msdn.com/Events/Build/2013/3-502).</span><span class="sxs-lookup"><span data-stu-id="bea99-106">For a recent presentation on SignalR performance and scaling, see [Scaling the Real-time Web with ASP.NET SignalR](https://channel9.msdn.com/Events/Build/2013/3-502).</span></span>

<span data-ttu-id="bea99-107">Toto téma obsahuje následující oddíly:</span><span class="sxs-lookup"><span data-stu-id="bea99-107">This topic contains the following sections:</span></span>

- [<span data-ttu-id="bea99-108">Aspekty návrhu</span><span class="sxs-lookup"><span data-stu-id="bea99-108">Design considerations</span></span>](#design)
- [<span data-ttu-id="bea99-109">Ladění serveru funkce SignalR pro výkon</span><span class="sxs-lookup"><span data-stu-id="bea99-109">Tuning your SignalR server for performance</span></span>](#tuning)
- [<span data-ttu-id="bea99-110">Řešení potíží s problémy s výkonem</span><span class="sxs-lookup"><span data-stu-id="bea99-110">Troubleshooting performance issues</span></span>](#troubleshooting)
- [<span data-ttu-id="bea99-111">Použití čítačů výkonu SignalR</span><span class="sxs-lookup"><span data-stu-id="bea99-111">Using SignalR performance counters</span></span>](#perfcounters)
- [<span data-ttu-id="bea99-112">Použití dalších čítačů výkonu</span><span class="sxs-lookup"><span data-stu-id="bea99-112">Using other performance counters</span></span>](#othercounters)
- [<span data-ttu-id="bea99-113">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="bea99-113">Other resources</span></span>](#otherresources)

<a id="design"></a>

## <a name="design-considerations"></a><span data-ttu-id="bea99-114">Aspekty návrhu</span><span class="sxs-lookup"><span data-stu-id="bea99-114">Design considerations</span></span>

<span data-ttu-id="bea99-115">Tato část popisuje vzory, které je možné implementovat při návrhu aplikace SignalR, ujistěte se, že není právě bránit výkon generování nepotřebný síťový provoz.</span><span class="sxs-lookup"><span data-stu-id="bea99-115">This section describes patterns that can be implemented during the design of a SignalR application, to ensure that performance is not being hindered by generating unnecessary network traffic.</span></span>

### <a name="throttling-message-frequency"></a><span data-ttu-id="bea99-116">Omezování četnosti zprávy</span><span class="sxs-lookup"><span data-stu-id="bea99-116">Throttling message frequency</span></span>

<span data-ttu-id="bea99-117">I v aplikaci, která odesílá zprávy s vysokou frekvencí (jako například herních aplikací v reálném čase) nemusíte většinu aplikací na více než několik zpráv za sekundu.</span><span class="sxs-lookup"><span data-stu-id="bea99-117">Even in an application that sends out messages at a high frequency (such as a realtime gaming application), most applications don't need to send more than a few messages a second.</span></span> <span data-ttu-id="bea99-118">Pokud chcete snížit objem provozu, který generuje každý klient, je možné implementovat smyčku zpráv, fronty a odesílá zprávy žádné další často než Pevná sazba (tedy až po určitém počtu zpráv se odešle, každou sekundu, pokud nejsou zprávy v dané chvíli ve terval k odeslání).</span><span class="sxs-lookup"><span data-stu-id="bea99-118">To reduce the amount of traffic that each client generates, a message loop can be implemented that queues and sends out messages no more frequently than a fixed rate (that is, up to a certain number of messages will be sent every second, if there are messages in that time interval to be sent).</span></span> <span data-ttu-id="bea99-119">Ukázková aplikace, která omezuje zpráv pro určitou míru (z klientských i serverových), najdete v části [vysokofrekvenční Reálný čas s knihovnou SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="bea99-119">For a sample application that throttles messages to a certain rate (from both client and server), see [High-Frequency Realtime with SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).</span></span>

### <a name="reducing-message-size"></a><span data-ttu-id="bea99-120">Omezení velikosti zpráv</span><span class="sxs-lookup"><span data-stu-id="bea99-120">Reducing message size</span></span>

<span data-ttu-id="bea99-121">Díky snížení objemu Serializované objekty můžete zmenšit velikost zpráv SignalR.</span><span class="sxs-lookup"><span data-stu-id="bea99-121">You can reduce the size of a SignalR message by reducing the size of your serialized objects.</span></span> <span data-ttu-id="bea99-122">V serverovém kódu, pokud odesíláte objekt, který obsahuje vlastnosti, které nemusíte přenášet, zabránit tyto vlastnosti serializována pomocí `JsonIgnore` atribut.</span><span class="sxs-lookup"><span data-stu-id="bea99-122">In server code, if you're sending an object that contains properties that don't need to be transmitted, prevent those properties from being serialized by using the `JsonIgnore` attribute.</span></span> <span data-ttu-id="bea99-123">Názvy vlastností jsou také uloženy ve zprávě; názvy vlastností lze zkrátit použitím `JsonProperty` atribut.</span><span class="sxs-lookup"><span data-stu-id="bea99-123">The names of properties are also stored in the message; the names of properties can be shortened using the `JsonProperty` attribute.</span></span> <span data-ttu-id="bea99-124">Následující vzorový kód ukazuje, jak být vyloučena určitá vlastnost nezabránil odesílání do klienta a jak zkrátit názvy vlastností:</span><span class="sxs-lookup"><span data-stu-id="bea99-124">The following code sample demonstrates how to exclude a property from being sent to the client, and how to shorten property names:</span></span>

<span data-ttu-id="bea99-125">**Server kódu .NET, který ukazuje atribut JsonIgnore k vyloučení dat odesílaných do klienta a atribut JsonProperty snížit velikost zprávy**</span><span class="sxs-lookup"><span data-stu-id="bea99-125">**.NET server code that demonstrates the JsonIgnore attribute to exclude data from being sent to the client, and the JsonProperty attribute to reduce message size**</span></span>

[!code-csharp[Main](signalr-performance/samples/sample1.cs?highlight=5,7,10)]

<span data-ttu-id="bea99-126">Aby bylo možné zachovat čitelnost / udržovatelnosti kódu klienta, názvy zkrácený vlastností, které může být změnu mapování na lidské vhodných názvů, po doručení zprávy.</span><span class="sxs-lookup"><span data-stu-id="bea99-126">In order to retain readability/ maintainability in the client code, the abbreviated property names can be remapped to human-friendly names after the message is received.</span></span> <span data-ttu-id="bea99-127">Následující příklad kódu ukazuje jeden možný způsob, jak přemapování zkrácené názvy delší ty, které jsou tak, že definujete kontraktu zprávy (mapování) a pomocí `reMap` funkce má být použita kontrakt třídu optimalizované zpráv:</span><span class="sxs-lookup"><span data-stu-id="bea99-127">The following code sample demonstrates one possible way of remapping shortened names to longer ones, by defining a message contract (mapping), and using the `reMap` function to apply the contract to the optimized message class:</span></span>

<span data-ttu-id="bea99-128">**Kód jazyka JavaScript na straně klienta, který změní zkrátila názvy vlastností na názvy v lidsky čitelném**</span><span class="sxs-lookup"><span data-stu-id="bea99-128">**Client-side JavaScript code that remaps shortened property names to human-readable names**</span></span>

[!code-javascript[Main](signalr-performance/samples/sample2.js)]

<span data-ttu-id="bea99-129">Názvy lze zkrátit ve zprávách z klienta na server, stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="bea99-129">Names can be shortened in messages from the client to the server as well, using the same method.</span></span>

<span data-ttu-id="bea99-130">Snižuje nároky na paměť (to znamená, množství paměti používané pro zprávu) zprávy může objekt také zvýšit výkon.</span><span class="sxs-lookup"><span data-stu-id="bea99-130">Reducing the memory footprint (that is, the amount of memory used for the message) of the message object can also improve performance.</span></span> <span data-ttu-id="bea99-131">Například pokud celou škálu `int` není potřeba, `short` nebo `byte` lze použít.</span><span class="sxs-lookup"><span data-stu-id="bea99-131">For example, if the full range of an `int` is not needed, a `short` or `byte` can be used instead.</span></span>

<span data-ttu-id="bea99-132">Protože zprávy jsou uloženy ve sběrnici zpráv v paměti serveru nezmenšit velikost této zprávy můžete také problémy s pamětí adresu serveru.</span><span class="sxs-lookup"><span data-stu-id="bea99-132">Since messages are stored in the message bus in server memory, reducing the size of messages can also address server memory issues.</span></span>

<a id="tuning"></a>

### <a name="tuning-your-signalr-server-for-performance"></a><span data-ttu-id="bea99-133">Ladění serveru funkce SignalR pro výkon</span><span class="sxs-lookup"><span data-stu-id="bea99-133">Tuning your SignalR server for performance</span></span>

<span data-ttu-id="bea99-134">Následující nastavení konfigurace slouží k ladění serveru pro vyšší výkon aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="bea99-134">The following configuration settings can be used to tune your server for better performance in a SignalR application.</span></span> <span data-ttu-id="bea99-135">Obecné informace o tom, jak zlepšit výkon v aplikaci ASP.NET najdete v tématu [zlepšení výkonu technologie ASP.NET](https://msdn.microsoft.com/library/ff647787.aspx).</span><span class="sxs-lookup"><span data-stu-id="bea99-135">For general information on how to improve performance in an ASP.NET application, see [Improving ASP.NET Performance](https://msdn.microsoft.com/library/ff647787.aspx).</span></span>

<span data-ttu-id="bea99-136">**Nastavení konfigurace SignalR**</span><span class="sxs-lookup"><span data-stu-id="bea99-136">**SignalR configuration settings**</span></span>

- <span data-ttu-id="bea99-137">**DefaultMessageBufferSize**: Ve výchozím nastavení zachová SignalR 1000 zpráv v paměti za rozbočovač za připojení.</span><span class="sxs-lookup"><span data-stu-id="bea99-137">**DefaultMessageBufferSize**: By default, SignalR retains 1000 messages in memory per hub per connection.</span></span> <span data-ttu-id="bea99-138">Pokud velké zprávy se používají, to může způsobit problémy s pamětí, které můžete zmírnit tím, že snižuje tuto hodnotu.</span><span class="sxs-lookup"><span data-stu-id="bea99-138">If large messages are being used, this may create memory issues which can be alleviated by reducing this value.</span></span> <span data-ttu-id="bea99-139">Toto nastavení je možné nastavit v `Application_Start` obslužné rutiny události v aplikaci ASP.NET nebo `Configuration` metody třídy pro spuštění OWIN v místním prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="bea99-139">This setting can be set in the `Application_Start` event handler in an ASP.NET application, or in the `Configuration` method of an OWIN startup class in a self-hosted application.</span></span> <span data-ttu-id="bea99-140">Následující příklad ukazuje, jak snížit za účelem snížení paměťových nároků vaší aplikace za účelem snížení množství paměti serveru používá tuto hodnotu:</span><span class="sxs-lookup"><span data-stu-id="bea99-140">The following sample demonstrates how to reduce this value in order to reduce the memory footprint of your application in order to reduce the amount of server memory used:</span></span>

    <span data-ttu-id="bea99-141">**Server kódu .NET v souboru Global.asax pro snížení výchozí velikost vyrovnávací paměti zpráv**</span><span class="sxs-lookup"><span data-stu-id="bea99-141">**.NET server code in Global.asax for decreasing default message buffer size**</span></span>

    [!code-csharp[Main](signalr-performance/samples/sample3.cs)]

<span data-ttu-id="bea99-142">**Nastavení konfigurace služby IIS**</span><span class="sxs-lookup"><span data-stu-id="bea99-142">**IIS configuration settings**</span></span>

- <span data-ttu-id="bea99-143">**Maximální počet souběžných požadavků na aplikaci**: Navýšení počtu souběžných IIS zvýší požadavky na prostředky serveru k dispozici obsluze žádostí.</span><span class="sxs-lookup"><span data-stu-id="bea99-143">**Max concurrent requests per application**: Increasing the number of concurrent IIS requests will increase server resources available for serving requests.</span></span> <span data-ttu-id="bea99-144">Výchozí hodnota je 5 000; Pokud chcete zvýšit toto nastavení, spusťte následující příkazy v příkazovém řádku se zvýšenými oprávněními:</span><span class="sxs-lookup"><span data-stu-id="bea99-144">The default value is 5000; to increase this setting, execute the following commands in an elevated command prompt:</span></span>

    [!code-console[Main](signalr-performance/samples/sample4.cmd)]

<span data-ttu-id="bea99-145">**Konfigurace nastavení ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="bea99-145">**ASP.NET configuration settings**</span></span>

<span data-ttu-id="bea99-146">Tato část obsahuje nastavení konfigurace, které je možné nastavit v `aspnet.config` souboru.</span><span class="sxs-lookup"><span data-stu-id="bea99-146">This section includes configuration settings that can be set in the `aspnet.config` file.</span></span> <span data-ttu-id="bea99-147">Tento soubor se nachází v jednom ze dvou umístění, v závislosti na platformě:</span><span class="sxs-lookup"><span data-stu-id="bea99-147">This file is found in one of two locations, depending on platform:</span></span>

- `%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet.config`
- `%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet.config`

<span data-ttu-id="bea99-148">Nastavení technologie ASP.NET, které mohou zlepšit výkon aplikace SignalR patří:</span><span class="sxs-lookup"><span data-stu-id="bea99-148">ASP.NET settings that may improve SignalR performance include the following:</span></span>

- <span data-ttu-id="bea99-149">**Maximální počet souběžných požadavků za procesoru**: Zvýšením tohoto nastavení může zmírnění výkonnostní kritické body.</span><span class="sxs-lookup"><span data-stu-id="bea99-149">**Maximum concurrent requests per CPU**: Increasing this setting may alleviate performance bottlenecks.</span></span> <span data-ttu-id="bea99-150">Ke zvýšení tohoto nastavení, přidejte následující nastavení konfigurace `aspnet.config` souboru:</span><span class="sxs-lookup"><span data-stu-id="bea99-150">To increase this setting, add the following configuration setting to the `aspnet.config` file:</span></span>

    [!code-xml[Main](signalr-performance/samples/sample5.xml?highlight=4)]
- <span data-ttu-id="bea99-151">**Limit fronty požadavků**: Pokud překročí celkový počet připojení `maxConcurrentRequestsPerCPU` nastavení technologie ASP.NET se spustí omezování požadavků pomocí fronty.</span><span class="sxs-lookup"><span data-stu-id="bea99-151">**Request Queue Limit**: When the total number of connections exceeds the `maxConcurrentRequestsPerCPU` setting, ASP.NET will start throttling requests using a queue.</span></span> <span data-ttu-id="bea99-152">Pokud chcete zvýšit velikost fronty, můžete zvýšit `requestQueueLimit` nastavení.</span><span class="sxs-lookup"><span data-stu-id="bea99-152">To increase the size of the queue, you can increase the `requestQueueLimit` setting.</span></span> <span data-ttu-id="bea99-153">Provedete to tak, přidejte následující nastavení konfigurace `processModel` uzel v `config/machine.config` (spíše než `aspnet.config`):</span><span class="sxs-lookup"><span data-stu-id="bea99-153">To do this, add the following configuration setting to the `processModel` node in `config/machine.config` (rather than `aspnet.config`):</span></span>

    [!code-xml[Main](signalr-performance/samples/sample6.xml)]

<a id="troubleshooting"></a>

## <a name="troubleshooting-performance-issues"></a><span data-ttu-id="bea99-154">Řešení potíží s problémy s výkonem</span><span class="sxs-lookup"><span data-stu-id="bea99-154">Troubleshooting performance issues</span></span>

<span data-ttu-id="bea99-155">Tato část popisuje, jak najít kritické body výkonu ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bea99-155">This section describes ways to find performance bottlenecks in your application.</span></span>

### <a name="verifying-that-websocket-is-being-used"></a><span data-ttu-id="bea99-156">Ověřuje se, že se používá protokol WebSocket</span><span class="sxs-lookup"><span data-stu-id="bea99-156">Verifying that WebSocket is being used</span></span>

<span data-ttu-id="bea99-157">Zatímco SignalR můžete použít celou řadu přenosů pro komunikaci mezi klientem a serverem, objektu websocket na straně nabízí výhody výkonu a by měla sloužit, pokud klient a server podporovat.</span><span class="sxs-lookup"><span data-stu-id="bea99-157">While SignalR can use a variety of transports for communication between client and server, WebSocket offers a significant performance advantage, and should be used if the client and server support it.</span></span> <span data-ttu-id="bea99-158">Pokud chcete zjistit, pokud klient a server splňovat požadavky protokolu websocket, naleznete v tématu [přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="bea99-158">To determine if your client and server meet the requirements for WebSocket, see [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span> <span data-ttu-id="bea99-159">Pokud chcete zjistit, jaký přenos se používá ve vaší aplikaci, můžete pomocí vývojářských nástrojů prohlížeče a zkontrolujte protokoly chcete zobrazit, jaký přenos se používá pro připojení.</span><span class="sxs-lookup"><span data-stu-id="bea99-159">To determine what transport is being used in your application, you can use the browser developer tools, and examine the logs to see what transport is being used for the connection.</span></span> <span data-ttu-id="bea99-160">Informace o používání vývojářských nástrojů prohlížeče v aplikaci Internet Explorer a Chrome, naleznete v tématu [přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="bea99-160">For information on using the browser development tools in Internet Explorer and Chrome, see [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="perfcounters"></a>

## <a name="using-signalr-performance-counters"></a><span data-ttu-id="bea99-161">Použití čítačů výkonu SignalR</span><span class="sxs-lookup"><span data-stu-id="bea99-161">Using SignalR performance counters</span></span>

<span data-ttu-id="bea99-162">Tato část popisuje, jak povolit a použití čítačů výkonu SignalR, součástí `Microsoft.AspNet.SignalR.Utils` balíčku.</span><span class="sxs-lookup"><span data-stu-id="bea99-162">This section describes how to enable and use SignalR performance counters, found in the `Microsoft.AspNet.SignalR.Utils` package.</span></span>

### <a name="installing-signalrexe"></a><span data-ttu-id="bea99-163">Instalace signalr.exe</span><span class="sxs-lookup"><span data-stu-id="bea99-163">Installing signalr.exe</span></span>

<span data-ttu-id="bea99-164">Čítače výkonu lze přidat na server pomocí nástroje volá SignalR.exe.</span><span class="sxs-lookup"><span data-stu-id="bea99-164">Peformance counters can be added to the server using a utility called SignalR.exe.</span></span> <span data-ttu-id="bea99-165">Chcete-li nainstalovat tento nástroj, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="bea99-165">To install this utility, follow these steps:</span></span>

1. <span data-ttu-id="bea99-166">V aplikaci Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **spravovat balíčky NuGet pro řešení**</span><span class="sxs-lookup"><span data-stu-id="bea99-166">In Visual Studio, select **Tools** > **NuGet Package Manager** > **Manage NuGet Packages for Solution**</span></span>
2. <span data-ttu-id="bea99-167">Vyhledejte **signalr.utils**a kliknout na tlačítko nainstalovat.</span><span class="sxs-lookup"><span data-stu-id="bea99-167">Search for **signalr.utils**, and select Install.</span></span>

    ![](signalr-performance/_static/image1.png)
3. <span data-ttu-id="bea99-168">Přijmout podmínky licenční smlouvy k instalaci balíčku.</span><span class="sxs-lookup"><span data-stu-id="bea99-168">Accept the license agreement to install the package.</span></span>
4. <span data-ttu-id="bea99-169">Bude možné SignalR.exe `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.</span><span class="sxs-lookup"><span data-stu-id="bea99-169">SignalR.exe will be installed to `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.</span></span>

### <a name="installing-performance-counters-with-signalrexe"></a><span data-ttu-id="bea99-170">Instalace čítačů výkonu s SignalR.exe</span><span class="sxs-lookup"><span data-stu-id="bea99-170">Installing performance counters with SignalR.exe</span></span>

<span data-ttu-id="bea99-171">K instalaci čítačů výkonu SignalR, spusťte v příkazovém řádku se zvýšenými oprávněními následující parametr SignalR.exe:</span><span class="sxs-lookup"><span data-stu-id="bea99-171">To install SignalR performance counters, run SignalR.exe in an elevated command prompt with the following parameter:</span></span>

[!code-console[Main](signalr-performance/samples/sample7.cmd)]

<span data-ttu-id="bea99-172">Pokud chcete odebrat čítačů výkonu SignalR, spusťte v příkazovém řádku se zvýšenými oprávněními následující parametr SignalR.exe:</span><span class="sxs-lookup"><span data-stu-id="bea99-172">To remove SignalR performance counters, run SignalR.exe in an elevated command prompt with the following parameter:</span></span>

[!code-console[Main](signalr-performance/samples/sample8.cmd)]

### <a name="signalr-performance-counters"></a><span data-ttu-id="bea99-173">Čítače výkonu SignalR</span><span class="sxs-lookup"><span data-stu-id="bea99-173">SignalR Performance counters</span></span>

<span data-ttu-id="bea99-174">Balíček nástroje nainstaluje následující čítače výkonu.</span><span class="sxs-lookup"><span data-stu-id="bea99-174">The utilites package installs the following performance counters.</span></span> <span data-ttu-id="bea99-175">"Celkový" čítače měření počtu událostí od poslední fond aplikací nebo server restartovat.</span><span class="sxs-lookup"><span data-stu-id="bea99-175">The "Total" counters measure the number of events since the last application pool or server restart.</span></span>

<span data-ttu-id="bea99-176">**Metrik připojení**</span><span class="sxs-lookup"><span data-stu-id="bea99-176">**Connection metrics**</span></span>

<span data-ttu-id="bea99-177">Následující metriky měření události životnost připojení, ke kterým dochází.</span><span class="sxs-lookup"><span data-stu-id="bea99-177">The following metrics measure the connection lifetime events that occur.</span></span> <span data-ttu-id="bea99-178">Další informace najdete v tématu [principy a zpracování událostí doby platnosti](../guide-to-the-api/handling-connection-lifetime-events.md).</span><span class="sxs-lookup"><span data-stu-id="bea99-178">For more information, see [Understanding and Handling Connection Lifetime Events](../guide-to-the-api/handling-connection-lifetime-events.md).</span></span>

- <span data-ttu-id="bea99-179">**Připojení připojeno**</span><span class="sxs-lookup"><span data-stu-id="bea99-179">**Connections Connected**</span></span>
- <span data-ttu-id="bea99-180">**Připojení znovu připojeny**</span><span class="sxs-lookup"><span data-stu-id="bea99-180">**Connections Reconnected**</span></span>
- <span data-ttu-id="bea99-181">**Disonnected připojení**</span><span class="sxs-lookup"><span data-stu-id="bea99-181">**Connections Disonnected**</span></span>
- <span data-ttu-id="bea99-182">**Aktuální připojení**</span><span class="sxs-lookup"><span data-stu-id="bea99-182">**Connections Current**</span></span>

<span data-ttu-id="bea99-183">**Metriky zpráv**</span><span class="sxs-lookup"><span data-stu-id="bea99-183">**Message metrics**</span></span>

<span data-ttu-id="bea99-184">Následující metriky měření přenos zpráv generované systémem SignalR.</span><span class="sxs-lookup"><span data-stu-id="bea99-184">The following metrics measure the message traffic generated by SignalR.</span></span>

- <span data-ttu-id="bea99-185">**Připojení celkový počet přijatých zpráv**</span><span class="sxs-lookup"><span data-stu-id="bea99-185">**Connection Messages Received Total**</span></span>
- <span data-ttu-id="bea99-186">**Připojení zprávy odeslané celkem**</span><span class="sxs-lookup"><span data-stu-id="bea99-186">**Connection Messages Sent Total**</span></span>
- <span data-ttu-id="bea99-187">**Přijaté zprávy připojení/s**</span><span class="sxs-lookup"><span data-stu-id="bea99-187">**Connection Messages Received/Sec**</span></span>
- <span data-ttu-id="bea99-188">**Odeslání zprávy připojení za sekundu**</span><span class="sxs-lookup"><span data-stu-id="bea99-188">**Connection Messages Sent/Sec**</span></span>

<span data-ttu-id="bea99-189">**Metriky zpráv Service bus**</span><span class="sxs-lookup"><span data-stu-id="bea99-189">**Message bus metrics**</span></span>

<span data-ttu-id="bea99-190">Následující metriky měření provoz přes vnitřní sběrnice zpráv SignalR, fronta, ve které se umístí všechny příchozí a odchozí zprávy SignalR.</span><span class="sxs-lookup"><span data-stu-id="bea99-190">The following metrics measure traffic through the internal SignalR message bus, the queue in which all incoming and outgoing SignalR messages are placed.</span></span> <span data-ttu-id="bea99-191">Zpráva se **publikováno** , pokud je odeslána nebo všesměrového vysílání.</span><span class="sxs-lookup"><span data-stu-id="bea99-191">A message is **Published** when it is sent or broadcast.</span></span> <span data-ttu-id="bea99-192">A **odběratele** v tomto kontextu je předplatné na sběrnici zpráv; to by měl být roven počtu klientů a samotný server.</span><span class="sxs-lookup"><span data-stu-id="bea99-192">A **Subscriber** in this context is a subscription on the message bus; this should equal the number of clients plus the server itself.</span></span> <span data-ttu-id="bea99-193">**Pracovních procesů přidělených** je komponenta, která odesílá data do aktivních připojení; **zaneprázdněný pracovního procesu** je ten, který je aktivně odesílá zprávu.</span><span class="sxs-lookup"><span data-stu-id="bea99-193">An **Allocated Worker** is a component that sends data to active connections; a **Busy Worker** is one that is actively sending a message.</span></span>

- <span data-ttu-id="bea99-194">**Celkový počet přijatých zpráv sběrnice zpráv**</span><span class="sxs-lookup"><span data-stu-id="bea99-194">**Message Bus Messages Received Total**</span></span>
- <span data-ttu-id="bea99-195">**Sběrnice zpráv zprávy přijaté za sekundu**</span><span class="sxs-lookup"><span data-stu-id="bea99-195">**Message Bus Messages Received/Sec**</span></span>
- <span data-ttu-id="bea99-196">**Zpráva Service Bus zprávy publikované celkem**</span><span class="sxs-lookup"><span data-stu-id="bea99-196">**Message Bus Messages Published Total**</span></span>
- <span data-ttu-id="bea99-197">**Sběrnice zpráv zprávy publikované za sekundu**</span><span class="sxs-lookup"><span data-stu-id="bea99-197">**Message Bus Messages Published/Sec**</span></span>
- <span data-ttu-id="bea99-198">**Aktuální předplatitelé zpráv Service Bus**</span><span class="sxs-lookup"><span data-stu-id="bea99-198">**Message Bus Subscribers Current**</span></span>
- <span data-ttu-id="bea99-199">**Celkový počet zpráv Service Bus předplatitele**</span><span class="sxs-lookup"><span data-stu-id="bea99-199">**Message Bus Subscribers Total**</span></span>
- <span data-ttu-id="bea99-200">**Předplatitelé sběrnice zpráv za sekundu**</span><span class="sxs-lookup"><span data-stu-id="bea99-200">**Message Bus Subscribers/Sec**</span></span>
- <span data-ttu-id="bea99-201">**Sběrnice zpráv přidělených pracovních procesů**</span><span class="sxs-lookup"><span data-stu-id="bea99-201">**Message Bus Allocated Workers**</span></span>
- <span data-ttu-id="bea99-202">**Service Bus zprávu vytížených pracovních procesů**</span><span class="sxs-lookup"><span data-stu-id="bea99-202">**Message Bus Busy Workers**</span></span>
- <span data-ttu-id="bea99-203">**Aktuální témata sběrnice zpráv**</span><span class="sxs-lookup"><span data-stu-id="bea99-203">**Message Bus Topics Current**</span></span>

<span data-ttu-id="bea99-204">**Chyba metriky**</span><span class="sxs-lookup"><span data-stu-id="bea99-204">**Error metrics**</span></span>

<span data-ttu-id="bea99-205">Následující metriky měření chyby vygenerované nástrojem přenos zpráv SignalR.</span><span class="sxs-lookup"><span data-stu-id="bea99-205">The following metrics measure errors generated by SignalR message traffic.</span></span> <span data-ttu-id="bea99-206">**Centrum řešení** dojít k chybám při rozbočovače a metody rozbočovače nelze rozpoznat.</span><span class="sxs-lookup"><span data-stu-id="bea99-206">**Hub Resolution** errors occur when a hub or hub method cannot be resolved.</span></span> <span data-ttu-id="bea99-207">**Volání rozbočovače** chyby jsou výjimky vyvolané při volání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="bea99-207">**Hub Invocation** errors are exceptions thrown while invoking a hub method.</span></span> <span data-ttu-id="bea99-208">**Přenos** chyby jsou chyby připojení vyvolána během požadavku HTTP nebo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="bea99-208">**Transport** errors are connection errors thrown during an HTTP request or response.</span></span>

- <span data-ttu-id="bea99-209">**Chyby: Všechny celkem**</span><span class="sxs-lookup"><span data-stu-id="bea99-209">**Errors: All Total**</span></span>
- <span data-ttu-id="bea99-210">**Chyby: Vše za sekundu**</span><span class="sxs-lookup"><span data-stu-id="bea99-210">**Errors: All/Sec**</span></span>
- <span data-ttu-id="bea99-211">**Chyby: Centrum řešení celkem**</span><span class="sxs-lookup"><span data-stu-id="bea99-211">**Errors: Hub Resolution Total**</span></span>
- <span data-ttu-id="bea99-212">**Chyby: Centrum řešení za sekundu**</span><span class="sxs-lookup"><span data-stu-id="bea99-212">**Errors: Hub Resolution/Sec**</span></span>
- <span data-ttu-id="bea99-213">**Chyby: Celkový počet volání rozbočovače**</span><span class="sxs-lookup"><span data-stu-id="bea99-213">**Errors: Hub Invocation Total**</span></span>
- <span data-ttu-id="bea99-214">**Chyby: Volání rozbočovače za sekundu**</span><span class="sxs-lookup"><span data-stu-id="bea99-214">**Errors: Hub Invocation/Sec**</span></span>
- <span data-ttu-id="bea99-215">**Chyby: Celkový přenos**</span><span class="sxs-lookup"><span data-stu-id="bea99-215">**Errors: Transport Total**</span></span>
- <span data-ttu-id="bea99-216">**Chyby: Přenos za sekundu**</span><span class="sxs-lookup"><span data-stu-id="bea99-216">**Errors: Transport/Sec**</span></span>

<span data-ttu-id="bea99-217">**Metriky horizontálním navýšením kapacity**</span><span class="sxs-lookup"><span data-stu-id="bea99-217">**Scaleout metrics**</span></span>

<span data-ttu-id="bea99-218">Následující metriky měření provoz a chyby vygenerované zprostředkovatelem o škálování.</span><span class="sxs-lookup"><span data-stu-id="bea99-218">The following metrics measure traffic and errors generated by the scaleout provider.</span></span> <span data-ttu-id="bea99-219">A **Stream** v tomto kontextu je jednotka škálování používá zprostředkovatel horizontálním navýšením kapacity; to je tabulka, pokud se používá SQL Server, téma služby Service Bus se používá a předplatné při použití Redis.</span><span class="sxs-lookup"><span data-stu-id="bea99-219">A **Stream** in this context is a scale unit used by the scaleout provider; this is a table if SQL Server is used, a Topic if Service Bus is used, and a Subscription if Redis is used.</span></span> <span data-ttu-id="bea99-220">Ve výchozím nastavení se používá pouze jeden datový proud, ale to je možné zvýšit prostřednictvím konfigurace na serveru SQL Server a služby Service Bus.</span><span class="sxs-lookup"><span data-stu-id="bea99-220">By default, only one stream is used, but this can be increased through configuration on SQL Server and Service Bus.</span></span> <span data-ttu-id="bea99-221">A **ukládání do vyrovnávací paměti** datový proud je ten, který má zadaný chybovém stavu, když je datový proud v chybovém stavu, všechny zprávy odeslané do propojovacího rozhraní dojde okamžitě, dokud je datový proud již chybující.</span><span class="sxs-lookup"><span data-stu-id="bea99-221">A **Buffering** stream is one that has entered a faulted state; when the stream is in the faulted state, all messages sent to the backplane will fail immediately until the stream is no longer faulting.</span></span> <span data-ttu-id="bea99-222">**Délku fronty odesílání** je počet zpráv, které byly odeslány, ale dosud nebyl odeslán.</span><span class="sxs-lookup"><span data-stu-id="bea99-222">The **Send Queue Length** is the number of messages that have been posted but not yet sent.</span></span>

- <span data-ttu-id="bea99-223">**Sběrnice zpráv o škálování zprávy přijaté za sekundu**</span><span class="sxs-lookup"><span data-stu-id="bea99-223">**Scaleout Message Bus Messages Received/Sec**</span></span>
- <span data-ttu-id="bea99-224">**Celkový počet datových proudů horizontálním navýšením kapacity**</span><span class="sxs-lookup"><span data-stu-id="bea99-224">**Scaleout Streams Total**</span></span>
- <span data-ttu-id="bea99-225">**Škálování aplikace streamuje Open**</span><span class="sxs-lookup"><span data-stu-id="bea99-225">**Scaleout Streams Open**</span></span>
- <span data-ttu-id="bea99-226">**Datové proudy dat o škálování, ukládání do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="bea99-226">**Scaleout Streams Buffering**</span></span>
- <span data-ttu-id="bea99-227">**Celkový počet chyb škálování aplikace**</span><span class="sxs-lookup"><span data-stu-id="bea99-227">**Scaleout Errors Total**</span></span>
- <span data-ttu-id="bea99-228">**Chyby/s horizontálním navýšením kapacity**</span><span class="sxs-lookup"><span data-stu-id="bea99-228">**Scaleout Errors/Sec**</span></span>
- <span data-ttu-id="bea99-229">**Délku fronty odesílání horizontálním navýšením kapacity**</span><span class="sxs-lookup"><span data-stu-id="bea99-229">**Scaleout Send Queue Length**</span></span>

<span data-ttu-id="bea99-230">Další informace o co tyto čítače jsou měření najdete v tématu [škálování aplikace SignalR službou Azure Service Bus](scaleout-with-windows-azure-service-bus.md).</span><span class="sxs-lookup"><span data-stu-id="bea99-230">For more information on what these counters are measuring, see [SignalR Scaleout with Azure Service Bus](scaleout-with-windows-azure-service-bus.md).</span></span>

<a id="othercounters"></a>

## <a name="using-other-performance-counters"></a><span data-ttu-id="bea99-231">Použití dalších čítačů výkonu</span><span class="sxs-lookup"><span data-stu-id="bea99-231">Using other performance counters</span></span>

<span data-ttu-id="bea99-232">Následující čítače výkonu může být užitečná při monitorování výkonu aplikace.</span><span class="sxs-lookup"><span data-stu-id="bea99-232">The following performance counters may also be useful in monitoring your application's performance.</span></span>

<span data-ttu-id="bea99-233">**Paměť**</span><span class="sxs-lookup"><span data-stu-id="bea99-233">**Memory**</span></span>

- <span data-ttu-id="bea99-234">.NET CLR paměti počet bajtů ve všech haldách (pro w3wp)</span><span class="sxs-lookup"><span data-stu-id="bea99-234">.NET CLR Memory# bytes in all Heaps (for w3wp)</span></span>

<span data-ttu-id="bea99-235">**ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="bea99-235">**ASP.NET**</span></span>

- <span data-ttu-id="bea99-236">Aktuální ASP.NET\Requests</span><span class="sxs-lookup"><span data-stu-id="bea99-236">ASP.NET\Requests Current</span></span>
- <span data-ttu-id="bea99-237">ASP.NET\Queued</span><span class="sxs-lookup"><span data-stu-id="bea99-237">ASP.NET\Queued</span></span>
- <span data-ttu-id="bea99-238">ASP.NET\Rejected</span><span class="sxs-lookup"><span data-stu-id="bea99-238">ASP.NET\Rejected</span></span>

<span data-ttu-id="bea99-239">**CPU**</span><span class="sxs-lookup"><span data-stu-id="bea99-239">**CPU**</span></span>

- <span data-ttu-id="bea99-240">Information\Processor času procesoru</span><span class="sxs-lookup"><span data-stu-id="bea99-240">Processor Information\Processor Time</span></span>

<span data-ttu-id="bea99-241">**TCP/IP**</span><span class="sxs-lookup"><span data-stu-id="bea99-241">**TCP/IP**</span></span>

- <span data-ttu-id="bea99-242">TCPv6/připojení</span><span class="sxs-lookup"><span data-stu-id="bea99-242">TCPv6/Connections Established</span></span>
- <span data-ttu-id="bea99-243">TCPv4/připojení</span><span class="sxs-lookup"><span data-stu-id="bea99-243">TCPv4/Connections Established</span></span>

<span data-ttu-id="bea99-244">**Webová služba**</span><span class="sxs-lookup"><span data-stu-id="bea99-244">**Web Service**</span></span>

- <span data-ttu-id="bea99-245">Webová Service\Current připojení</span><span class="sxs-lookup"><span data-stu-id="bea99-245">Web Service\Current Connections</span></span>
- <span data-ttu-id="bea99-246">Webová Service\Maximum připojení</span><span class="sxs-lookup"><span data-stu-id="bea99-246">Web Service\Maximum Connections</span></span>

<span data-ttu-id="bea99-247">**Dělení na vlákna**</span><span class="sxs-lookup"><span data-stu-id="bea99-247">**Threading**</span></span>

- <span data-ttu-id="bea99-248">Uzamčení a vlákna .NET CLR\# z aktuálních logických podprocesů</span><span class="sxs-lookup"><span data-stu-id="bea99-248">.NET CLR LocksAndThreads\# of current logical Threads</span></span>
- <span data-ttu-id="bea99-249">Vlákna .NET CLR LocksAnd\# z aktuálních fyzických vláken</span><span class="sxs-lookup"><span data-stu-id="bea99-249">.NET CLR LocksAnd Threads\# of current physical Threads</span></span>

<a id="otherresources"></a>

## <a name="other-resources"></a><span data-ttu-id="bea99-250">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bea99-250">Other Resources</span></span>

<span data-ttu-id="bea99-251">Další informace o monitorování a optimalizace výkonu do technologie ASP.NET naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="bea99-251">For more information on ASP.NET performance monitoring and tuning, see the following topics:</span></span>

- <span data-ttu-id="bea99-252">[ASP.NET: přehled výkonu](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)</span><span class="sxs-lookup"><span data-stu-id="bea99-252">[ASP.NET Performance Overview](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)</span></span>
- [<span data-ttu-id="bea99-253">Využití vlákna ASP.NET na IIS 7.5, IIS 7.0 a IIS 6.0</span><span class="sxs-lookup"><span data-stu-id="bea99-253">ASP.NET Thread Usage on IIS 7.5, IIS 7.0, and IIS 6.0</span></span>](https://blogs.msdn.com/b/tmarq/archive/2007/07/21/asp-net-thread-usage-on-iis-7-0-and-6-0.aspx)
- [<span data-ttu-id="bea99-254">&lt;applicationPool&gt; – Element (nastavení webu)</span><span class="sxs-lookup"><span data-stu-id="bea99-254">&lt;applicationPool&gt; Element (Web Settings)</span></span>](https://msdn.microsoft.com/library/dd560842.aspx)
