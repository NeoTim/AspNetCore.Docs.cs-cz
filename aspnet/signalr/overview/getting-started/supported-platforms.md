---
uid: signalr/overview/getting-started/supported-platforms
title: Podporované platformy | Dokumentace Microsoftu
author: bradygaster
description: Tento článek popisuje, jaké klienty a servery jsou podporovány systémem SignalR.
ms.author: bradyg
ms.date: 04/18/2018
ms.assetid: eac31beb-0f46-4afa-9def-e80904dea4f0
msc.legacyurl: /signalr/overview/getting-started/supported-platforms
msc.type: authoredcontent
ms.openlocfilehash: 60fa74b54797efbe14ba525160b2f750a4f5a451
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836048"
---
<a name="supported-platforms"></a>Podporované platformy
====================
podle [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento článek popisuje, jaké klienty a servery jsou podporovány systémem SignalR. 
> 
> ## <a name="questions-and-comments"></a>Otázky a komentáře
> 
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).

SignalR je podporována v různých konfigurací klienta a serveru. Kromě toho každá možnost přenosu má sadu požadavků na své vlastní. Pokud požadavky na systém pro přenos nejsou k dispozici, bude převzetí služeb při selhání do jiné přenosy řádně SignalR. Další informace o přenosy, které podporuje funkci SignalR naleznete v tématu [přenosy a náhrad](introduction-to-signalr.md#transports).

## <a name="server-system-requirements"></a>Požadavky na systém Server

Součást serveru SignalR je možné hostovat na různých konfigurací serveru. Tato část popisuje podporované verze operačních systémů, rozhraní .NET framework, Internet Information Server a další komponenty.

### <a name="supported-server-operating-systems"></a>Podporované serverové operační systémy

V následující serverové nebo klientské operační systémy je možné hostovat součást serveru funkce SignalR. Mějte na paměti, že pro funkci SignalR k používají protokoly Websocket, Windows Server 2012, Windows Server 2016 nebo Windows 8 vyžádáním (pomocí protokolu WebSocket je možné na Windows Azure Web Sites nastavena v lokalitě verze rozhraní .NET framework 4.5, a pokud je povolené webové sokety na webu Stránka Konfigurace).

- Windows Server 2016
- Windows Server 2012
- Windows Server 2008 r2
- Windows 10
- Windows 8
- Windows 7
- Microsoft Azure

### <a name="supported-server-net-framework-version"></a>Podporované serverové verze rozhraní .NET Framework

Funkce SignalR 2 je podporován pouze v rozhraní .NET Framework 4.5. Zobrazit [doporučené aktualizace](#updates) oddílu pro aktualizace, které zvyšuje spolehlivost, kompatibility, stabilitu a výkon.

### <a name="supported-server-iis-versions"></a>Podporované serverové verze služby IIS

Když jsou SignalR je hostované v IIS, se podporují následující verze. Všimněte si, že pokud slouží klientský operační systém, například pro vývoj (Windows 8 nebo Windows 7), plné verze služby IIS nebo Cassini by neměl být, protože nebude limit 10 souběžných připojení, která jsou uložena, a které bude dosažen velmi rychle od připojení není přechodná, často se obnovil a jsou odstraněny ihned po již nejsou déle používány. Služba IIS Express je třeba použít v klientských operačních systémech.

Všimněte si také, že pro funkci SignalR k použití protokolu WebSocket, IIS 8 nebo služby IIS Express 8 musí být použit, server musí používat systém Windows 8, Windows Server 2012 nebo novější, a protokolu WebSocket musí být povolené ve službě IIS. Informace o tom, jak povolit objektu websocket na straně ve službě IIS najdete v tématu [podpora protokolu WebSocket služby IIS 8.0](https://www.iis.net/learn/get-started/whats-new-in-iis-8/iis-80-websocket-protocol-support).

- Služba IIS 8 nebo 8 služby IIS Express.
- Služba IIS 7 a 7.5. Podpora pro [adresy URL bez přípony](https://support.microsoft.com/kb/980368) je povinný.
- Služba IIS musí být spuštěn v integrovaném režimu; klasický režim není podporován. Zpožděním zpráv až 30 sekund může dojít, pokud služba IIS je spuštěn v režimu classic pomocí přenosu Server-Sent události.
- Hostitelské aplikace musí být spuštěn v režimu úplné důvěryhodnosti.

## <a name="client-system-requirements"></a>Požadavky na systém klienta

SignalR je možné v různých klientských platformách. Tato část popisuje požadavky na systém pro použití aplikace SignalR ve webových prohlížečů, aplikací klasické pracovní plochy Windows, aplikací Silverlight a mobilní zařízení.

### <a name="web-browsers"></a>webové prohlížeče

SignalR je použít v různých webových prohlížečů, ale obvykle jsou podporovány pouze nejnovější dvě verze.

Aplikace, které používají funkci SignalR v prohlížečích musí používat verzi jQuery 1.6.4 nebo hlavní novější verze (například 1.7.2, 1.8.2 nebo 1.9.1).

SignalR je použít v následujících prohlížečů:

- Verze aplikace Microsoft Internet Explorer 8, 9, 10 a 11. Moderní, Desktop a mobilní verze podporují.
- Mozilla Firefox: aktuální verze - 1, Windows i Mac verze.
- Google Chrome: aktuální verze - 1, Windows i Mac verze.
- Safari: aktuální verze - 1, verze Mac OS a iOS.
- Opera: aktuální verze - 1, pouze Windows.
- Prohlížeč systému Android

Kromě nutnosti některé prohlížeče, mají různé přenosy, které používá SignalR své vlastní požadavky. Následující přenosy jsou podporovány v rámci následující konfigurace:

<a id="browser"></a>

**Požadavky na webový prohlížeč přenosu**

| Přenos | Internet Explorer | Chrome (Windows nebo iOS) | Firefox | Safari (OSX nebo iOS) | Android |
| --- | --- | --- | --- | --- | --- |
| WebSocket | 10+ | aktuální - 1 | aktuální - 1 | aktuální - 1 | Není k dispozici |
| Události odeslané serverem | Není k dispozici | aktuální - 1 | aktuální - 1 | aktuální - 1 | Není k dispozici |
| ForeverFrame | 8+ | Není k dispozici | Není k dispozici | Není k dispozici | 4.1 |
| Dlouhým dotazováním | 8+ | aktuální - 1 | aktuální - 1 | aktuální - 1 | 4.1 |

\*: 6 + je nutná pro všemi funkcemi.

#### <a name="unsupported-browsers"></a>Nepodporované prohlížeče

Zatímco SignalR *může* spustit bez hlavní problémy ve starších verzích prohlížečů, jsme SignalR aktivně netestujte v nich a obecně nelze vyřešit chyby, které se mohou objevit v nich.

### <a name="windows-desktop-and-silverlight-applications"></a>Plochy Windows a aplikace Silverlight

Kromě spuštění ve webovém prohlížeči, je možné hostovat SignalR samostatný klient Windows a v aplikacích Silverlight. Plochy Windows a funkce SignalR technologie Silverlight aplikace mají následující požadavky na systém.

- Windows XP SP3 nebo novější, jsou podporovány aplikací s použitím rozhraní .NET 4.
- V systému Windows Vista nebo novější, jsou podporovány aplikací s použitím rozhraní .NET Framework 4.5.

Kromě operačního systému a požadavky na rozhraní .NET framework mají k dispozici pro funkci SignalR přenosy své vlastní požadavky. Následující přenosy jsou podporovány v rámci následující konfigurace:

**Požadavky na přenos Silverlight a Windows Desktop**

| Přenos | Aplikace .NET | Silverlight |
| --- | --- | --- |
| Webové sokety | Windows 8 + a .NET 4.5 + | Není k dispozici |
| Navždy rámce | Není k dispozici | Není k dispozici |
| Události odeslané serverem | .NET 4+ | 5+ |
| Dlouhým dotazováním | .NET 4+ | 5+ |

<a id="android"></a>

### <a name="windows-store-and-windows-phone-applications"></a>Windows Store a aplikací Windows Phone

SignalR je použít v aplikacích Windows Store a aplikace Windows Phone 8. Následující přenosy jsou podporovány v rámci následující konfigurace:

**Windows Store a Windows Phone přenosu požadavky**

| Přenos | Windows Store / .NET | Windows Store / JavaScript | Windows Phone / IE | Windows Phone a .NET |
| --- | --- | --- | --- | --- |
| WebSocket | Není k dispozici | Win8 + | 8+ | Není k dispozici |
| Navždy rámce | Není k dispozici | Win8 + | 7.5+ | Není k dispozici |
| Události odeslané serverem | Win8 + | Není k dispozici | Není k dispozici | 8+ |
| Dlouhým dotazováním | Win8 + | Win8 + | 7.5+ | 8+ |

<a id="updates"></a>

## <a name="recommended-updates"></a>Doporučené aktualizace

Tyto aktualizace se doporučují pro funkci SignalR serverů:

- Aktualizace pro rozhraní .NET Framework 4.5 je k dispozici [tady](https://support.microsoft.com/kb/2750149).
- Microsoft pravidelně vydá opravy QFE pro technologii ASP.NET. Toto bude použito jako dostupné.
