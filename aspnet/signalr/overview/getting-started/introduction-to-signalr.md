---
uid: signalr/overview/getting-started/introduction-to-signalr
title: Úvod ke knihovně SignalR | Dokumentace Microsoftu
author: bradygaster
description: Tento článek popisuje, co je SignalR a některé z řešení, která byla navržena k vytvoření.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 0fab5e35-8c1f-43d4-8635-b8aba8766a71
msc.legacyurl: /signalr/overview/getting-started/introduction-to-signalr
msc.type: authoredcontent
ms.openlocfilehash: 7b9dae3e5d79319a9fefee41f4525a59f950746a
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837764"
---
<a name="introduction-to-signalr"></a>Úvod ke knihovně SignalR
====================

podle [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]


> Tento článek popisuje, co je SignalR a některé z řešení, která byla navržena k vytvoření. 
> 
> ## <a name="questions-and-comments"></a>Otázky a komentáře
> 
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](https://stackoverflow.com/questions/tagged/signalr).

## <a name="what-is-signalr"></a>Co je SignalR?

Funkce SignalR technologie ASP.NET představují knihovnu pro vývojáře využívající technologii ASP.NET, která zjednodušuje proces přidávání funkce webu v reálném čase do aplikací. Funkce webu v reálném čase je schopnost nabízených kód serveru obsah připojeným klientům okamžitě, jakmile je k dispozici, namísto nutnosti čekat klient k vyžádání nových dat serveru.

SignalR je možné přidat jakýkoliv druh funkcí "v reálném čase" pro vaši aplikaci ASP.NET. Při konverzace se často používá jako příklad, můžete udělat mnohem více. Kdykoli uživatel aktualizuje zobrazíte nová data na webové stránce nebo stránce implementuje [dlouhé dotazování](http://en.wikipedia.org/wiki/Push_technology#Long_polling) načte nová data, je kandidátem pro použití aplikace SignalR. Mezi příklady patří řídicí panely a monitorování aplikací, aplikace pro spolupráci (jako je například simultánní úpravy dokumentů), úlohy, průběh aktualizace a v reálném čase formuláře.

Funkce SignalR také umožňuje zcela nové typy webových aplikací, které vyžadují vysoká frekvence aktualizace ze serveru, například v reálném čase hry.

Funkce SignalR poskytuje jednoduché rozhraní API pro vytváření server klient vzdálených volání procedur (RPC), které volají funkce JavaScript v klientovi prohlížeče (a ostatní klientských platformách) z kódu .NET na straně serveru. Funkce SignalR také zahrnuje rozhraní API pro správu připojení (pro instanci, připojení a odpojení události) a seskupení připojení.

![Vyvolání metody s knihovnou SignalR](introduction-to-signalr/_static/image1.png)

Funkce SignalR automaticky zpracovává připojení správy a umožňuje zprávy všesměrového vysílání na všechny připojené klienty najednou, jako je chatovací místnosti. Můžete také odesílají zprávy službě konkrétních klientů. Připojení mezi klientem a serverem je trvalé, na rozdíl od klasického připojení HTTP, které je obnoveno pro každé komunikace směrem.

Funkce SignalR podporuje funkce "serveru push", ve kterém můžete volat kód serveru navýšení kapacity pro klientský kód v prohlížeči pomocí vzdáleného volání procedur (RPC), spíše než běžné typu žádost odpověď modelu na webu ještě dnes.

Aplikace knihovnou SignalR můžete škálovat do tisíců klientů pomocí služby Service Bus, SQL Server nebo [Redis](http://redis.io).

SignalR je open source, přístupné prostřednictvím [Githubu](https://github.com/signalr).

## <a name="signalr-and-websocket"></a>SignalR a protokol WebSocket

SignalR používá nové dopravní WebSocket, pokud je k dispozici a přejde zpět na starší přenos v případě potřeby. Při psaní by jistě vaší aplikace pomocí WebSocket přímo, pomocí SignalR znamená, že se již děje mnoho dalších funkcí, které je nutné implementovat. Co je nejdůležitější to znamená, že můžete kód vaší aplikace využít WebSocket bez starosti o vytvoření samostatného kódu cesty pro starší klienty. SignalR také chrání před starosti o aktualizacích WebSocket, protože SignalR je aktualizována na podporu změn v podkladové dopravy poskytuje konzistentní rozhraní aplikace napříč různými verzemi WebSocket.

<a id="transports"></a>

## <a name="transports-and-fallbacks"></a>Přenosy a náhrad

SignalR je abstrakcí některé přenosy, které jsou potřeba k práci v reálném čase mezi klientem a serverem. Připojení SignalR se spustí jako HTTP a je pak povýšen na připojení soketu WebSocket, pokud je k dispozici. Objekt WebSocket je ideální přenosu pro funkci SignalR, protože nejúčinnější využívá paměť serveru, má nejnižší latenci a má nejvíce základní funkce (například plně duplexní komunikace mezi klientem a serverem), ale má také nejpřísnější požadavky: Protokol WebSocket vyžaduje server musí používat Windows Server 2012 nebo Windows 8 a rozhraní .NET Framework 4.5. Pokud tyto požadavky nejsou splněny, SignalR se pokusí použít další přenosy, aby jeho připojení.

### <a name="html-5-transports"></a>Přenáší HTML 5

Tyto přenosy závisí na podporu [HTML 5](http://en.wikipedia.org/wiki/HTML5). Pokud prohlížeč klienta nepodporuje standardu HTML 5, použije se starší přenosy.

- **Protokol WebSocket** (Pokud serveru i prohlížeče určit, jakým může podpořit objektu websocket na straně). Objekt WebSocket je pouze přenosu, který vytvoří true trvalé, pokud vytvoříte obousměrný připojení mezi klientem a serverem. Ale protokolu WebSocket má také nejpřísnějšími požadavky na; je plně podporovaný jenom v nejnovějších verzích Microsoft Internet Explorer, Google Chrome a Mozilla Firefox a má jenom částečnou implementaci v dalších prohlížečích, jako je například Opera a Safari.
- **Události odeslané serverem**, označovaný také jako EventSource (Pokud je prohlížeč podporuje odeslání událostí na serveru, který je v podstatě všech prohlížečích, s výjimkou aplikace Internet Explorer).

### <a name="comet-transports"></a>Přenosy Comet

Následující přenosy jsou založeny na [Comet](http://en.wikipedia.org/wiki/Comet_(programming)) model webové aplikace 00Z prohlížeči nebo jiném klientovi udržuje dlouhodobě uložená požadavek HTTP, který server můžete použít k zápisu dat do klienta bez klienta konkrétně o to požádá.

- **Navždy rámec** (pro aplikaci Internet Explorer). Navždy rámce vytváří skrytý element IFrame, který odešle požadavek na koncový bod na serveru, která není dokončena. Server pak průběžně odešle skript klienta, který je proveden okamžitě, poskytuje jednosměrnou v reálném čase připojení ze serveru do klienta. Připojení z klienta k serveru pomocí samostatného připojení ze serveru pro připojení klienta, a jako standardní požadavek HTTP se vytvoří nové připojení pro jednotlivá data, která se pošle.
- **Dlouhý interval dotazování AJAX**. Dlouhý interval dotazování nevytváří trvalé připojení, ale místo toho dotazuje server s žádostí, které zůstávají otevřené, dokud nebude tento server odpoví, v tomto okamžiku se připojení uzavře, a okamžitě vyžádání nového připojení. To může způsobit určitou latenci, zatímco připojení se obnoví.

Další informace o jaké přenosy jsou podporovány v rámci které konfigurace najdete v tématu [podporované platformy](supported-platforms.md).

### <a name="transport-selection-process"></a>Proces přenosu výběr

Následující seznam obsahuje kroky, které používá SignalR rozhodnout, které používaného přenosu.

1. Pokud je prohlížeč Internet Explorer 8 nebo dřívější verzí, se používá dlouhý interval dotazování.
2. Pokud je nakonfigurovaný JSONP (to znamená `jsonp` parametr je nastaven na `true` zahájení připojení), dlouhé dotazování se používá.
3. Pokud mezi doménami právě připojení (tj. Pokud koncových bodů SignalR není ve stejné doméně jako stránka hostingu), bude objekt WebSocket použit, pokud se splní následující kritéria:

   - Klient podporuje CORS (sdílení prostředků různého původu). Podrobnosti, které klienti podpora CORS, najdete v části [CORS v caniuse.com](http://www.caniuse.com/CORS).
   - Klient podporuje protokol WebSocket
   - Podporuje objektu websocket na straně serveru

     Pokud se nesplní žádné z těchto kritérií, dlouhé dotazování použít. Další informace o připojeních mezi doménami, najdete v části [jak k navázání připojení mezi doménami](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).
4. Pokud není nakonfigurovaný JSONP a připojení není mezi doménami, objektu websocket na straně se použijí, pokud klient i server podporovat.
5. Pokud klient nebo server nepodporují protokolu WebSocket, události odeslané serverem se používá, pokud je k dispozici.
6. Pokud události odeslání serveru není k dispozici, dojde k pokusu o navždy rámce.
7. Pokud se nezdaří navždy rámce, dlouhé dotazování se používá.

<a id="MonitoringTransports"></a>
### <a name="monitoring-transports"></a>Monitorování přenosů

Můžete určit, jaký přenos vaše aplikace používá povolením protokolování na rozbočovače a otevřete okno konzoly v prohlížeči.

Povolení protokolování pro vaše Centrum událostí v prohlížeči, přidáním následujícího příkazu do klientské aplikace:

`$.connection.hub.logging = true;`

- V aplikaci Internet Explorer stisknutím klávesy F12 otevřete Nástroje pro vývojáře a klikněte na kartu konzoly.

    ![V Microsoft Internet Explorer](introduction-to-signalr/_static/image2.png)
- V prohlížeči Chrome otevřete stisknutím kombinace kláves Ctrl + Shift + J konzolu.

    ![Konzoly v prohlížeči Google Chrome](introduction-to-signalr/_static/image3.png)

Otevřete konzoly a protokolování povoleno budete moci zobrazit, které přenosu se používá v systému SignalR.

![Konzola znázorňující přenos pomocí protokolu WebSocket aplikace Internet Explorer](introduction-to-signalr/_static/image4.png)

### <a name="specifying-a-transport"></a>Určení přenosu

Vyjednávání přenos trvá určitou část času a klient/server prostředky. Pokud jsou známé možnosti klienta, pak přenos je možné zadat při spuštění připojení klienta. Následující fragment kódu ukazuje spuštění pomocí přenosu Ajax dlouhý interval dotazování, jako by se použily, pokud byla uložena, že klient nepodporuje žádný jiný protokol pro připojení:

`connection.start({ transport: 'longPolling' });`

Pokud chcete, aby klient vyzkoušet konkrétní přenosy v pořadí, můžete zadat záložní pořadí. Následující fragment kódu ukazuje chybě objektu websocket na straně a služeb při selhání, který, že přejdete přímo na dlouhé dotazování.

`connection.start({ transport: ['webSockets','longPolling'] });`

Řetězcové konstanty pro zadání přenosy jsou definovány takto:

- `webSockets`
- `foreverFrame`
- `serverSentEvents`
- `longPolling`

## <a name="connections-and-hubs"></a>Připojeními a rozbočovači

Rozhraní API SignalR obsahuje dva modely pro komunikaci mezi klienty a servery: Trvalé připojeními a rozbočovači.

Připojení představuje jednoduchý koncový bod pro odesílání zpráv jednoho příjemce, seskupené nebo všesměrového vysílání. Poskytuje trvalé připojení rozhraní API (představovanými v kódu .NET třídou PersistentConnection), vývojář přímý přístup k nižší úrovně komunikační protokol, který zveřejňuje funkce SignalR. Pomocí připojení komunikační model bude zkušenosti vývojáře, kteří používají rozhraní API založená na připojení jako je Windows Communication Foundation.

Centrum je více základní kanál postavené na rozhraní API připojení, které umožňuje klientem a serverem pro volání metod na sobě navzájem přímo. Funkce SignalR zpracovává odeslání přes hranice počítače stejně, jako by magic, umožňuje klientům volání metod na serveru jako snadno jako místní metody a naopak. Pomocí centra komunikační model bude pro vývojáře, kteří použili vzdáleného volání rozhraní API, jako je .NET Remoting srozumitelná. Použití rozbočovače také umožňuje předání silného typu parametrů k metodám, povolení vazby modelu.

### <a name="architecture-diagram"></a>Diagram architektury

Následující diagram znázorňuje vztah mezi rozbočovače, trvalé připojení a základní technologie používané pro přenosy.

![Diagram architektury SignalR znázorňující rozhraní API, přenosy a klientů](introduction-to-signalr/_static/image5.png)

### <a name="how-hubs-work"></a>Jak fungují rozbočovače

Když na straně serveru kód volá metody na straně klienta, paket posílání přes aktivní přenos, který obsahuje název a parametry metody, která se má volat (při odesílání objektu jako parametru metody, je serializován pomocí formátu JSON). Klient pak odpovídá názvu metody do metody definované v kódu na straně klienta. Pokud se zjistí shoda, metoda klienta se spustí pomocí dat deserializovat parametr.

Volání metody, které je možné monitorovat pomocí nástrojů, jako je [Fiddleru.](http://fiddler2.com/) Následující obrázek ukazuje volání metody odesílat webového prohlížeče klienta v podokně protokoly Fiddleru ze serveru funkce SignalR. Volání metody, které je odesílány z centrum s názvem `MoveShapeHub`, a volaná metoda je volána `updateShape`.

![Zobrazení protokolu Fiddleru zobrazující provoz SignalR](introduction-to-signalr/_static/image6.png)

V tomto příkladu je přiřazen název centra `H` parametr; metodu se určuje podle názvu `M` parametr a datech odesílaných do metody se určuje podle `A` parametru. Vytvoření aplikace, které vygenerovalo tuto zprávu v [vysokofrekvenční Reálný čas](tutorial-high-frequency-realtime-with-signalr.md) kurzu.

### <a name="choosing-a-communication-model"></a>Výběr modelu komunikace

Většina aplikací musí použít rozhraní API rozbočovače. Připojení rozhraní API se daly použít v následujících případech:

- Formát skutečné byla odeslána zpráva musí být zadán.
- Vývojář upřednostňuje pro práci s jako model zasílání zpráv nebo dispatching spíše než modelu vzdálené volání.
- Existující aplikaci, která používá model zasílání zpráv je při přenosu používat funkci SignalR.
