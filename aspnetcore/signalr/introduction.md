---
title: Úvod do ASP.NET Core SignalR
author: tdykstra
description: Zjistěte, jak funkce SignalR technologie ASP.NET Core library usnadňuje přidávání funkcí v reálném čase do aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/25/2018
uid: signalr/introduction
ms.openlocfilehash: da18837c690d2182589db5f486ae74e537ade931
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425143"
---
# <a name="introduction-to-aspnet-core-signalr"></a>Úvod do ASP.NET Core SignalR

## <a name="what-is-signalr"></a>Co je SignalR?

Funkce SignalR technologie ASP.NET Core je open source knihovna, která zjednodušuje přidávání funkce webu v reálném čase do aplikací. Funkce webu v reálném čase umožňuje okamžitě kódu na straně serveru předávaný obsah do klientů.

Vhodnými kandidáty pro funkci SignalR:

* Aplikace, které vyžadují vysoká frekvence aktualizace ze serveru. Příkladem jsou hry, sociálních sítích, hlasování, aukce, mapy a GPS aplikace.
* Řídicí panely a monitorování aplikace. Příklady zahrnují společnosti řídicích panelů, rychlých aktualizací prodeje, nebo cestují výstrahy.
* Aplikace pro spolupráci. Aplikace tabule a týmu splnění softwaru jsou příklady aplikací pro spolupráci.
* Aplikace, které vyžadují oznámení. Sociálních sítí, e-mailu, konverzace, využívají hry, cestování výstrahy a mnoha jiných aplikací oznámení.

Funkce SignalR poskytuje rozhraní API pro vytváření server klient [vzdálených volání procedur (RPC)](https://wikipedia.org/wiki/Remote_procedure_call). Vzdálených volání procedur volají funkce JavaScript na klientských počítačích z kódu .NET Core na straně serveru.

Tady jsou některé funkce SignalR technologie ASP.NET Core:

* Správa připojení automaticky zpracovává.
* Odešle zprávy do všech připojených klientů současně. Například chatovací místnosti.
* Odešle zprávy do konkrétních klientů nebo skupiny klientů.
* Škálování zpracování rostoucí provoz.

Zdroj je hostován v [SignalR úložišti na Githubu](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).

## <a name="transports"></a>Přenosy

Funkce SignalR podporuje několik postupů pro zpracování komunikaci v reálném čase:

* [Webové sokety](https://tools.ietf.org/html/rfc7118)
* Události odeslané serverem
* Dlouhým dotazováním

Funkce SignalR automaticky vybere nejlepší metody přenosu, který je v rámci funkce serveru a klienta.

## <a name="hubs"></a>Rozbočovače

Používá funkci SignalR *rozbočovače* ke komunikaci mezi klienty a servery.

Centrum je základní kanál, který umožňuje klienta a serveru, volání metod na sobě navzájem. Funkce SignalR zpracovává odeslání přes hranice počítač automaticky, umožňuje klientům volání metod na serveru a naopak. Typově silné parametry můžete předat do metody, což umožňuje vazby modelu. Funkce SignalR poskytuje dva protokoly integrované centra: textového protokolu na základě JSON a binární protokol založený na [MessagePack](https://msgpack.org/).  MessagePack vytváří obecně menší zprávy ve srovnání s JSON. Starší prohlížeče musí podporovat [XHR úrovně 2](https://caniuse.com/#feat=xhr2) k zajištění podpory protokolu MessagePack.

Rozbočovače pro volání kód na straně klienta zasílání zpráv, které obsahují název a parametry metody na straně klienta. Objekty, které jako parametry metody jsou deserializaci pomocí nakonfigurovaný protokol. Klient se pokusí shodovat s názvem metody v kódu na straně klienta. Když klient najde shodu, volá metodu a předá data deserializovaný parametrů.

## <a name="additional-resources"></a>Další zdroje

* [Začínáme s knihovnou SignalR pro ASP.NET Core](xref:tutorials/signalr)
* [Podporované platformy](xref:signalr/supported-platforms)
* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
