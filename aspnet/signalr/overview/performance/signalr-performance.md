---
uid: signalr/overview/performance/signalr-performance
title: Výkon aplikace SignalR | Dokumentace Microsoftu
author: bradygaster
description: Výkon aplikace SignalR
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 3751f5e7-59db-4be0-a290-50abc24e5c84
msc.legacyurl: /signalr/overview/performance/signalr-performance
msc.type: authoredcontent
ms.openlocfilehash: 3326c2e600854fc7a4435d96c45b04a6188d3937
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836509"
---
<a name="signalr-performance"></a>Výkon aplikace SignalR
====================
podle [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Toto téma popisuje, jak navrhovat pro měření a zlepšit výkon aplikace SignalR.
>
> ## <a name="software-versions-used-in-this-topic"></a>Verze softwaru použitým v tomto tématu
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Funkce SignalR verze 2
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Předchozích verzích tohoto tématu
>
> Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).


Poslední prezentace na výkon aplikace SignalR a škálování najdete v části [škálování webu v reálném čase s knihovnou ASP.NET SignalR](https://channel9.msdn.com/Events/Build/2013/3-502).

Toto téma obsahuje následující oddíly:

- [Aspekty návrhu](#design)
- [Ladění serveru funkce SignalR pro výkon](#tuning)
- [Řešení potíží s problémy s výkonem](#troubleshooting)
- [Použití čítačů výkonu SignalR](#perfcounters)
- [Použití dalších čítačů výkonu](#othercounters)
- [Další prostředky](#otherresources)

<a id="design"></a>

## <a name="design-considerations"></a>Aspekty návrhu

Tato část popisuje vzory, které je možné implementovat při návrhu aplikace SignalR, ujistěte se, že není právě bránit výkon generování nepotřebný síťový provoz.

### <a name="throttling-message-frequency"></a>Omezování četnosti zprávy

I v aplikaci, která odesílá zprávy s vysokou frekvencí (jako například herních aplikací v reálném čase) nemusíte většinu aplikací na více než několik zpráv za sekundu. Pokud chcete snížit objem provozu, který generuje každý klient, je možné implementovat smyčku zpráv, fronty a odesílá zprávy žádné další často než Pevná sazba (tedy až po určitém počtu zpráv se odešle, každou sekundu, pokud nejsou zprávy v dané chvíli ve terval k odeslání). Ukázková aplikace, která omezuje zpráv pro určitou míru (z klientských i serverových), najdete v části [vysokofrekvenční Reálný čas s knihovnou SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).

### <a name="reducing-message-size"></a>Omezení velikosti zpráv

Díky snížení objemu Serializované objekty můžete zmenšit velikost zpráv SignalR. V serverovém kódu, pokud odesíláte objekt, který obsahuje vlastnosti, které nemusíte přenášet, zabránit tyto vlastnosti serializována pomocí `JsonIgnore` atribut. Názvy vlastností jsou také uloženy ve zprávě; názvy vlastností lze zkrátit použitím `JsonProperty` atribut. Následující vzorový kód ukazuje, jak být vyloučena určitá vlastnost nezabránil odesílání do klienta a jak zkrátit názvy vlastností:

**Server kódu .NET, který ukazuje atribut JsonIgnore k vyloučení dat odesílaných do klienta a atribut JsonProperty snížit velikost zprávy**

[!code-csharp[Main](signalr-performance/samples/sample1.cs?highlight=5,7,10)]

Aby bylo možné zachovat čitelnost / udržovatelnosti kódu klienta, názvy zkrácený vlastností, které může být změnu mapování na lidské vhodných názvů, po doručení zprávy. Následující příklad kódu ukazuje jeden možný způsob, jak přemapování zkrácené názvy delší ty, které jsou tak, že definujete kontraktu zprávy (mapování) a pomocí `reMap` funkce má být použita kontrakt třídu optimalizované zpráv:

**Kód jazyka JavaScript na straně klienta, který změní zkrátila názvy vlastností na názvy v lidsky čitelném**

[!code-javascript[Main](signalr-performance/samples/sample2.js)]

Názvy lze zkrátit ve zprávách z klienta na server, stejným způsobem.

Snižuje nároky na paměť (to znamená, množství paměti používané pro zprávu) zprávy může objekt také zvýšit výkon. Například pokud celou škálu `int` není potřeba, `short` nebo `byte` lze použít.

Protože zprávy jsou uloženy ve sběrnici zpráv v paměti serveru nezmenšit velikost této zprávy můžete také problémy s pamětí adresu serveru.

<a id="tuning"></a>

### <a name="tuning-your-signalr-server-for-performance"></a>Ladění serveru funkce SignalR pro výkon

Následující nastavení konfigurace slouží k ladění serveru pro vyšší výkon aplikace SignalR. Obecné informace o tom, jak zlepšit výkon v aplikaci ASP.NET najdete v tématu [zlepšení výkonu technologie ASP.NET](https://msdn.microsoft.com/library/ff647787.aspx).

**Nastavení konfigurace SignalR**

- **DefaultMessageBufferSize**: Ve výchozím nastavení zachová SignalR 1000 zpráv v paměti za rozbočovač za připojení. Pokud velké zprávy se používají, to může způsobit problémy s pamětí, které můžete zmírnit tím, že snižuje tuto hodnotu. Toto nastavení je možné nastavit v `Application_Start` obslužné rutiny události v aplikaci ASP.NET nebo `Configuration` metody třídy pro spuštění OWIN v místním prostředí aplikace. Následující příklad ukazuje, jak snížit za účelem snížení paměťových nároků vaší aplikace za účelem snížení množství paměti serveru používá tuto hodnotu:

    **Server kódu .NET v souboru Startup.cs pro snížení výchozí velikost vyrovnávací paměti zpráv**

    [!code-csharp[Main](signalr-performance/samples/sample3.cs)]

**Nastavení konfigurace služby IIS**

- **Maximální počet souběžných požadavků na aplikaci**: Navýšení počtu souběžných IIS zvýší požadavky na prostředky serveru k dispozici obsluze žádostí. Výchozí hodnota je 5 000; Pokud chcete zvýšit toto nastavení, spusťte následující příkazy v příkazovém řádku se zvýšenými oprávněními:

    [!code-console[Main](signalr-performance/samples/sample4.cmd)]
- **ApplicationPool QueueLength**: Toto je maximální počet požadavků, ovladač Http.sys zařadí do fronty pro fond aplikací. Po zaplnění fronty, nové požadavky obdrží odpověď 503 – Služba není dostupná". Výchozí hodnota je 1000.

    Zkrácení délky fronty pro pracovní proces ve fondu aplikací, který je hostitelem vaší aplikace bude šetřit prostředky paměti. Další informace najdete v tématu [Správa, ladění a konfigurace fondů aplikací](https://technet.microsoft.com/library/cc745955.aspx).

**Konfigurace nastavení ASP.NET**

Tato část obsahuje nastavení konfigurace, které je možné nastavit v `aspnet.config` souboru. Tento soubor se nachází v jednom ze dvou umístění, v závislosti na platformě:

- `%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet.config`
- `%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet.config`

Nastavení technologie ASP.NET, které mohou zlepšit výkon aplikace SignalR patří:

- **Maximální počet souběžných požadavků za procesoru**: Zvýšením tohoto nastavení může zmírnění výkonnostní kritické body. Ke zvýšení tohoto nastavení, přidejte následující nastavení konfigurace `aspnet.config` souboru:

    [!code-xml[Main](signalr-performance/samples/sample5.xml?highlight=4)]
- **Limit fronty požadavků**: Pokud překročí celkový počet připojení `maxConcurrentRequestsPerCPU` nastavení technologie ASP.NET se spustí omezování požadavků pomocí fronty. Pokud chcete zvýšit velikost fronty, můžete zvýšit `requestQueueLimit` nastavení. Provedete to tak, přidejte následující nastavení konfigurace `processModel` uzel v `config/machine.config` (spíše než `aspnet.config`):

    [!code-xml[Main](signalr-performance/samples/sample6.xml)]

<a id="troubleshooting"></a>

## <a name="troubleshooting-performance-issues"></a>Řešení potíží s problémy s výkonem

Tato část popisuje, jak najít kritické body výkonu ve vaší aplikaci.

### <a name="verifying-that-websocket-is-being-used"></a>Ověřuje se, že se používá protokol WebSocket

Zatímco SignalR můžete použít celou řadu přenosů pro komunikaci mezi klientem a serverem, objektu websocket na straně nabízí výhody výkonu a by měla sloužit, pokud klient a server podporovat. Pokud chcete zjistit, pokud klient a server splňovat požadavky protokolu websocket, naleznete v tématu [přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports). Pokud chcete zjistit, jaký přenos se používá ve vaší aplikaci, můžete pomocí vývojářských nástrojů prohlížeče a zkontrolujte protokoly chcete zobrazit, jaký přenos se používá pro připojení. Informace o používání vývojářských nástrojů prohlížeče v aplikaci Internet Explorer a Chrome, naleznete v tématu [přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports).

<a id="perfcounters"></a>

## <a name="using-signalr-performance-counters"></a>Použití čítačů výkonu SignalR

Tato část popisuje, jak povolit a použití čítačů výkonu SignalR, součástí `Microsoft.AspNet.SignalR.Utils` balíčku.

### <a name="installing-signalrexe"></a>Instalace signalr.exe

Čítače výkonu lze přidat na server pomocí nástroje volá SignalR.exe. Chcete-li nainstalovat tento nástroj, postupujte takto:

1. V aplikaci Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **spravovat balíčky NuGet pro řešení**
2. Vyhledejte **signalr.utils**a kliknout na tlačítko nainstalovat.

    ![](signalr-performance/_static/image1.png)
3. Přijmout podmínky licenční smlouvy k instalaci balíčku.
4. Bude možné SignalR.exe `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.

### <a name="installing-performance-counters-with-signalrexe"></a>Instalace čítačů výkonu s SignalR.exe

K instalaci čítačů výkonu SignalR, spusťte v příkazovém řádku se zvýšenými oprávněními následující parametr SignalR.exe:

[!code-console[Main](signalr-performance/samples/sample7.cmd)]

Pokud chcete odebrat čítačů výkonu SignalR, spusťte v příkazovém řádku se zvýšenými oprávněními následující parametr SignalR.exe:

[!code-console[Main](signalr-performance/samples/sample8.cmd)]

### <a name="signalr-performance-counters"></a>Čítače výkonu SignalR

Nástroje pro balíček nainstaluje následující čítače výkonu. "Celkový" čítače měření počtu událostí od poslední fond aplikací nebo server restartovat.

**Metrik připojení**

Následující metriky měření události životnost připojení, ke kterým dochází. Další informace najdete v tématu [principy a zpracování událostí doby platnosti](../guide-to-the-api/handling-connection-lifetime-events.md).

- **Připojení připojeno**
- **Připojení znovu připojeny**
- **Odpojené připojení**
- **Aktuální připojení**

**Metriky zpráv**

Následující metriky měření přenos zpráv generované systémem SignalR.

- **Připojení celkový počet přijatých zpráv**
- **Připojení zprávy odeslané celkem**
- **Přijaté zprávy připojení/s**
- **Odeslání zprávy připojení za sekundu**

**Metriky zpráv Service bus**

Následující metriky měření provoz přes vnitřní sběrnice zpráv SignalR, fronta, ve které se umístí všechny příchozí a odchozí zprávy SignalR. Zpráva se **publikováno** , pokud je odeslána nebo všesměrového vysílání. A **odběratele** v tomto kontextu je předplatné na sběrnici zpráv; to by měl být roven počtu klientů a samotný server. **Pracovních procesů přidělených** je komponenta, která odesílá data do aktivních připojení; **zaneprázdněný pracovního procesu** je ten, který je aktivně odesílá zprávu.

- **Celkový počet přijatých zpráv sběrnice zpráv**
- **Sběrnice zpráv zprávy přijaté za sekundu**
- **Zpráva Service Bus zprávy publikované celkem**
- **Sběrnice zpráv zprávy publikované za sekundu**
- **Aktuální předplatitelé zpráv Service Bus**
- **Celkový počet zpráv Service Bus předplatitele**
- **Předplatitelé sběrnice zpráv za sekundu**
- **Sběrnice zpráv přidělených pracovních procesů**
- **Service Bus zprávu vytížených pracovních procesů**
- **Aktuální témata sběrnice zpráv**

**Chyba metriky**

Následující metriky měření chyby vygenerované nástrojem přenos zpráv SignalR. **Centrum řešení** dojít k chybám při rozbočovače a metody rozbočovače nelze rozpoznat. **Volání rozbočovače** chyby jsou výjimky vyvolané při volání metody rozbočovače. **Přenos** chyby jsou chyby připojení vyvolána během požadavku HTTP nebo odpovědi.

- **Chyby: Všechny celkem**
- **Chyby: Vše za sekundu**
- **Chyby: Centrum řešení celkem**
- **Chyby: Centrum řešení za sekundu**
- **Chyby: Celkový počet volání rozbočovače**
- **Chyby: Volání rozbočovače za sekundu**
- **Chyby: Celkový přenos**
- **Chyby: Přenos za sekundu**

<a id="scaleout_metrics"></a>

**Metriky horizontálním navýšením kapacity**

Následující metriky měření provoz a chyby vygenerované zprostředkovatelem o škálování. A **Stream** v tomto kontextu je jednotka škálování používá zprostředkovatel horizontálním navýšením kapacity; to je tabulka, pokud se používá SQL Server, téma služby Service Bus se používá a předplatné při použití Redis. Každý datový proud zajišťuje uspořádaný operací čtení a zápisu; jeden datový proud je potenciální kritický bod škálování, proto je možné zvýšit počet datových proudů snížit tento kritický bod. Pokud jsou používány více datové proudy, bude SignalR automaticky distribuovat napříč těchto datových proudů způsobem, který zajistí, že zpráv odeslaných z jakékoli dané připojení jsou v pořadí zpráv (horizontální oddíl).

[MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx) nastavení ovládacích prvků délka fronty pro odesílání dat o škálování udržuje SignalR. Nastavení na hodnotu větší než 0, umístí všechny zprávy ve frontě odeslat postupně po jednom odeslání do propojovacího rozhraní nakonfigurované pro zasílání zpráv. Pokud velikost fronty překročí nakonfigurovanou délku, následná volání k odeslání se okamžitě selhat s [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception(v=vs.118).aspx) dokud počet zpráv ve frontě je menší než nastavení znovu. Zařazení do fronty je ve výchozím nastavení zakázáno, protože implementované backplanes obecně mít své vlastní zařazování do fronty a řízení toku na místě. V případě SQL serveru sdružování připojení efektivně omezuje počet odešle děje v daný okamžik.

Ve výchozím nastavení, se používá pouze jeden datový proud pro SQL Server a Redis, pět datových proudů se používají pro Service Bus a jejich zařazování do fronty je zakázaná, ale tato nastavení lze změnit prostřednictvím konfigurace na serveru SQL Server a služby Service Bus:

**.NET serverový kód pro konfigurace tabulky count a fronta délku propojovací rozhraní systému SQL Server**

[!code-csharp[Main](signalr-performance/samples/sample9.cs)]

**.NET serverový kód pro konfiguraci tématu počet a fronta délku pro propojovací Service Bus rozhraní**

[!code-csharp[Main](signalr-performance/samples/sample10.cs)]

A **ukládání do vyrovnávací paměti** datový proud je ten, který má zadaný chybovém stavu, když je datový proud v chybovém stavu, všechny zprávy odeslané do propojovacího rozhraní dojde okamžitě, dokud je datový proud již chybující. **Délku fronty odesílání** je počet zpráv, které byly odeslány, ale dosud nebyl odeslán.

- **Sběrnice zpráv o škálování zprávy přijaté za sekundu**
- **Celkový počet datových proudů horizontálním navýšením kapacity**
- **Škálování aplikace streamuje Open**
- **Datové proudy dat o škálování, ukládání do vyrovnávací paměti**
- **Celkový počet chyb škálování aplikace**
- **Chyby/s horizontálním navýšením kapacity**
- **Délku fronty odesílání horizontálním navýšením kapacity**

Další informace o co tyto čítače jsou měření najdete v tématu [škálování aplikace SignalR službou Azure Service Bus](scaleout-with-windows-azure-service-bus.md).

<a id="othercounters"></a>

## <a name="using-other-performance-counters"></a>Použití dalších čítačů výkonu

Následující čítače výkonu může být užitečná při monitorování výkonu aplikace.

**Paměť**

- Paměť .NET CLR\\počet bajtů ve všech haldách (pro w3wp)

**ASP.NET**

- Aktuální ASP.NET\Requests
- ASP.NET\Queued
- ASP.NET\Rejected

**CPU**

- Information\Processor času procesoru

**TCP/IP**

- TCPv6/připojení
- TCPv4/připojení

**Webová služba**

- Webová Service\Current připojení
- Webová Service\Maximum připojení

**Dělení na vlákna**

- .NET CLR uzamčení a vláken\\počet aktuálních logických podprocesů
- .NET CLR uzamčení a vláken\\počet aktuálních fyzických vláken

<a id="otherresources"></a>

## <a name="other-resources"></a>Další zdroje

Další informace o monitorování a optimalizace výkonu do technologie ASP.NET naleznete v následujících tématech:

- [ASP.NET: přehled výkonu](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)
- [Využití vlákna ASP.NET na IIS 7.5, IIS 7.0 a IIS 6.0](https://blogs.msdn.com/b/tmarq/archive/2007/07/21/asp-net-thread-usage-on-iis-7-0-and-6-0.aspx)
- [&lt;applicationPool&gt; – Element (nastavení webu)](https://msdn.microsoft.com/library/dd560842.aspx)
