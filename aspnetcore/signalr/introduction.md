---
title: Úvod do ASP.NET Core SignalR
author: bradygaster
description: Přečtěte si, jak ASP.NET Core SignalR Library zjednodušuje přidávání funkcí v reálném čase do aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 7108d9f223db78937dd1203a1cb4b890006b20ec
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963943"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a>Úvod do ASP.NET Core SignalR

## <a name="what-is-opno-locsignalr"></a>Co je SignalR?

ASP.NET Core SignalR je open source knihovna, která zjednodušuje přidávání webových funkcí v reálném čase do aplikací. Webová funkce v reálném čase umožňuje, aby kód na straně serveru nabízel okamžitý obsah klientům.

Dobré kandidáty na SignalR:

* Aplikace, které vyžadují aktualizace s vysokou frekvencí ze serveru. Příklady her, sociálních sítí, hlasovacích, aukcí, map a aplikací GPS.
* Řídicí panely a monitorovací aplikace. Mezi příklady patří řídicí panely společnosti, aktualizace rychlých prodejů nebo upozornění na cestování.
* Aplikace pro spolupráci. Příklady aplikací pro spolupráci jsou aplikace tabule a software pro týmovou schůzku.
* Aplikace, které vyžadují oznámení Oznámení se týkají sociálních sítí, e-mailů, chatu, her, oznámení o cestách a mnoha dalších aplikací.

SignalR poskytuje rozhraní API pro vytváření [vzdálených volání procedur (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)mezi servery. Volání RPC volá funkce JavaScriptu na klientech z kódu .NET Core na straně serveru.

Zde jsou některé funkce SignalR pro ASP.NET Core:

* Zpracovává správu připojení automaticky.
* Odesílá současně zprávy všem připojeným klientům. Například chatovací místnost.
* Odesílá zprávy konkrétním klientům nebo skupinám klientů.
* Škálujte na zvýšení provozu.

Zdroj je hostovaný v [SignalR úložišti na GitHubu](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).

## <a name="transports"></a>Přenosy

SignalR podporuje několik postupů pro zpracování komunikace v reálném čase:

* [Webové sokety](https://tools.ietf.org/html/rfc7118)
* Události odeslané serverem
* Dlouhé cyklické dotazování

SignalR automaticky zvolí nejlepší přenosovou metodu, která se nachází v rámci schopností serveru a klienta.

## <a name="hubs"></a>Centra

SignalR používá ke komunikaci mezi klienty a servery *centra* .

Centrum je kanál vysoké úrovně, který umožňuje klientovi a serveru volat metody na sebe navzájem. SignalR zpracovává automatické odesílání mezi hranicemi počítačů a umožňuje klientům volat metody na serveru a naopak. Můžete předat parametry silného typu metodám, které umožňují vazbu modelu. SignalR poskytuje dva integrované protokoly rozbočovačů: textový protokol založený na formátu JSON a binární protokol založený na [MessagePack](https://msgpack.org/).  MessagePack obvykle vytváří menší zprávy ve srovnání se JSON. Aby bylo možné poskytovat podporu protokolu MessagePack, starší prohlížeče musí podporovat [XHR úrovně 2](https://caniuse.com/#feat=xhr2) .

Centra volají kód na straně klienta odesláním zpráv, které obsahují název a parametry metody na straně klienta. Objekty odeslané jako parametry metody jsou deserializovány pomocí nakonfigurovaného protokolu. Klient se pokusí porovnat název s metodou v kódu na straně klienta. Když klient najde shodu, zavolá metodu a předá jí deserializovaná data parametrů.

## <a name="additional-resources"></a>Další zdroje

* [Začínáme s SignalR pro ASP.NET Core](xref:tutorials/signalr)
* [Podporované platformy](xref:signalr/supported-platforms)
* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
