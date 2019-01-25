---
uid: signalr/overview/performance/scaleout-in-signalr
title: Úvod do škálování aplikace SignalR | Dokumentace Microsoftu
author: bradygaster
description: Verze softwaru použitým v tomto tématu Visual Studio 2013 .NET 4.5 SignalR 2 předchozí verze tohoto tématu informace o předchozích verzích...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 7e781fc1-1c1f-45a8-bc1d-338e96dbe9c9
msc.legacyurl: /signalr/overview/performance/scaleout-in-signalr
msc.type: authoredcontent
ms.openlocfilehash: 60ae0353745284796eb7e0ddb6397ecb48eceaf0
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836200"
---
<a name="introduction-to-scaleout-in-signalr"></a>Úvod do škálování aplikace SignalR
====================
podle [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

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


Obecně platí, existují dva způsoby, jak škálovat webovou aplikaci: *vertikálně navýšit kapacitu* a *horizontální navýšení kapacity*.

- Vertikálně navýšit kapacitu znamená větší serveru (nebo větší virtuální počítač) pomocí více paměti RAM, procesory atd.
- Horizontální navýšení kapacity znamená, že přidáte další servery pro zpracování zátěže.

Problém s vertikálním navýšení kapacity je rychle dosažení limitu na velikost počítače. Kromě toho je potřeba škálovat na více systémů. Ale při horizontálním navýšením kapacity je získat klienty směrovat na různé servery. Klient, který je připojený k jednomu serveru nebude přijímat zprávy odeslané z jiného serveru.

![](scaleout-in-signalr/_static/image1.png)

Jedním z řešení je k předávání zpráv mezi servery pomocí komponenty s názvem *propojovací rozhraní systému*. Propojovací rozhraní povolená každá instance aplikace odesílá zprávy do propojovacího rozhraní a propojovacího rozhraní předává je do jiné instance aplikace. (V elektronika, je skupina paralelní konektory propojovacího rozhraní. Obdobně propojovací rozhraní systému SignalR připojí víc serverů.)

![](scaleout-in-signalr/_static/image2.png)

Funkce SignalR v současné době poskytuje tři backplanes:

- **Azure Service Bus**. Service Bus je infrastruktura zasílání zpráv, který umožňuje součástem pro odesílání zpráv volně způsobem.
- **Redis**. Redis je úložiště klíč / hodnota v paměti. Redis podporuje vzorec publikovat/odebírat ("pub/sub") pro odesílání zpráv.
- **SQL Server**. Propojovací rozhraní systému SQL Server zapisuje zprávy do tabulky SQL. Propojovacího rozhraní používá pro efektivní zasílání zpráv služby Service Broker. Ale to funguje také v případě služby Service Broker není povolená.

Pokud nasadíte svou aplikaci v Azure, zvažte použití pomocí propojovací rozhraní systému Redis [Azure Redis Cache](https://azure.microsoft.com/services/cache/). Pokud provádíte nasazení do serverové farmy, vezměte v úvahu systému SQL Server nebo Redis backplanes.

Následující témata obsahují podrobné kurzy pro každého propojovací rozhraní systému:

- [Škálování aplikace SignalR službou Azure Service Bus](scaleout-with-windows-azure-service-bus.md)
- [Šklálování aplikace SignalR službou Redis](scaleout-with-redis.md)
- [Šklálování aplikace SignalR SQL Serverem](scaleout-with-sql-server.md)

## <a name="implementation"></a>Implementace

V knihovně SignalR každá zpráva se odesílá prostřednictvím sběrnice zpráv. Implementuje sběrnice zpráv [IMessageBus](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.imessagebus(v=vs.100).aspx) rozhraní, které poskytuje abstrakci se publikovat/odebírat. Backplanes fungovat tak, že nahradíte výchozí **IMessageBus** se sběrnicí navržené pro propojovací rozhraní. Například sběrnici zpráv pro Redis je [RedisMessageBus](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.redis.redismessagebus(v=vs.100).aspx), a používá Redis [pub/sub](http://redis.io/topics/pubsub) mechanismus pro odesílání a příjem zpráv.

Každá instance serveru se připojí k propojovací rozhraní systému přes Service bus. Při odesílání zprávy přejde do propojovacího rozhraní a odesílá je propojovacího rozhraní na každý server. Když server dostane zprávu z propojovacího rozhraní, vloží zprávu v místní mezipaměti. Server poté předává zprávy klientů z místní mezipaměti.

Pro každé připojení klienta jsou sledovány klienta probíhá čtení datový proud zpráv pomocí kurzoru. (Kurzor představuje pozice v proudu zpráv.) Pokud se klient odpojí a potom se znovu připojí, požádá Service bus pro všechny zprávy, které byly přijaty za hodnotou kurzor klienta. Se stejnou věc stane, když připojení používá [dlouhé dotazování](../getting-started/introduction-to-signalr.md#transports). Po dokončení žádosti dlouhé dotazování klienta se otevře nové připojení a vyzve k zadání zprávy, které byly přijaty po kurzor.

Mechanismus funguje kurzor i v případě, že klient se směruje na jiný server na znovu připojit. Propojovacího rozhraní zohledňuje všech serverů a nebude vadit, který server pro připojení klienta.

## <a name="limitations"></a>Omezení

Maximální propustnost, pomocí propojovací rozhraní, je nižší, než je, pokud klienti komunikovat přímo k uzlu jeden server. Důvodem je, propojovacího rozhraní předává všechny zprávy pro každý uzel, takže propojovacího rozhraní se může stát kritickým bodem. Záleží na aplikaci, zda toto omezení je nějaký problém. Například tady jsou některé typické scénáře SignalR:

- [Server vysílání](../getting-started/tutorial-server-broadcast-with-signalr.md) (například akciích): Backplanes fungovat dobře pro tento scénář, protože rychlost, jakou jsou odesílány zprávy pro ovládací prvky server.
- [Klient klient](../getting-started/tutorial-getting-started-with-signalr.md) (například konverzace): V tomto scénáři propojovacího rozhraní může být kritickým bodem v případě, že počet zpráv, které se škáluje s počtem klientů; To znamená, pokud roste počet zpráv proporcionálně Další klienti se připojují k.
- [Vysokofrekvenční Reálný čas](../getting-started/tutorial-high-frequency-realtime-with-signalr.md) (například hry v reálném čase): Propojovací rozhraní se nedoporučuje pro tento scénář.

## <a name="enabling-tracing-for-signalr-scaleout"></a>Povolení trasování pro škálování aplikace SignalR

Povolení trasování pro backplanes, přidejte do souboru web.config v kořenovém adresáři v dalších částech **konfigurace** element:

[!code-html[Main](scaleout-in-signalr/samples/sample1.html)]
