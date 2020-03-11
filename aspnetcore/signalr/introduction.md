---
title: Úvod do ASP.NET Core SignalR
author: bradygaster
description: Přečtěte si, jak ASP.NET Core SignalR Library zjednodušuje přidávání funkcí v reálném čase do aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 635431abf9263c2dff261aea47e6f8324061763f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78662916"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a>Úvod do ASP.NET Core SignalR

## <a name="what-is-opno-locsignalr"></a>Co je SignalR?

ASP.NET Core SignalR je open source knihovna, která zjednodušuje přidávání webových funkcí v reálném čase do aplikací. Webová funkce v reálném čase umožňuje, aby kód na straně serveru nabízel okamžitý obsah klientům.

Dobré kandidáty na SignalR:

* Aplikace, které vyžadují aktualizace ze serveru s vysokou frekvencí. Příkladem jsou hry, sociální sítě, hlasování, aukce, mapy a aplikace využívající GPS.
* Řídicí panely a aplikace pro monitorování. Mezi příklady patří řídicí panely společností, okamžité a aktuální informace o prodeji nebo upozornění pro cestující.
* Aplikace podporující spolupráci. Příklady aplikací podporujících spolupráci jsou aplikace tabulí a software pro týmové schůzky.
* Aplikace, které vyžadují oznámení. Oznámení využívají sociální sítě, e-mailové aplikace, chaty, hry, upozornění pro cestující a řada dalších aplikací.

SignalR poskytuje rozhraní API pro vytváření [vzdálených volání procedur (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)mezi servery. Volání RPC volá funkce JavaScriptu na klientech z kódu .NET Core na straně serveru.

Zde jsou některé funkce SignalR pro ASP.NET Core:

* Zpracovává správu připojení automaticky.
* Odesílá současně zprávy všem připojeným klientům. Například chatovací místnost.
* Odesílá zprávy konkrétním klientům nebo skupinám klientů.
* Škálujte na zvýšení provozu.

Zdroj je hostovaný v [SignalR úložišti na GitHubu](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).

## <a name="transports"></a>Přenosy

SignalR podporuje následující techniky pro zpracování komunikace v reálném čase (v pořadí podle řádného použití):

* [Webové sokety](https://tools.ietf.org/html/rfc7118)
* Události odeslané serverem
* Dlouhé cyklické dotazování

SignalR automaticky zvolí nejlepší přenosovou metodu, která se nachází v rámci schopností serveru a klienta.

## <a name="hubs"></a>Hubs

SignalR používá ke komunikaci mezi klienty a servery *centra* .

Centrum je kanál vysoké úrovně, který umožňuje klientovi a serveru volat metody na sebe navzájem. SignalR zpracovává automatické odesílání mezi hranicemi počítačů a umožňuje klientům volat metody na serveru a naopak. Můžete předat parametry silného typu metodám, které umožňují vazbu modelu. SignalR poskytuje dva integrované protokoly rozbočovačů: textový protokol založený na formátu JSON a binární protokol založený na [MessagePack](https://msgpack.org/).  MessagePack obvykle vytváří menší zprávy ve srovnání se JSON. Aby bylo možné poskytovat podporu protokolu MessagePack, starší prohlížeče musí podporovat [XHR úrovně 2](https://caniuse.com/#feat=xhr2) .

Centra volají kód na straně klienta odesláním zpráv, které obsahují název a parametry metody na straně klienta. Objekty odeslané jako parametry metody jsou deserializovány pomocí nakonfigurovaného protokolu. Klient se pokusí porovnat název s metodou v kódu na straně klienta. Když klient najde shodu, zavolá metodu a předá jí deserializovaná data parametrů.

## <a name="additional-resources"></a>Další zdroje

* [Začínáme s SignalR pro ASP.NET Core](xref:tutorials/signalr)
* [Podporované platformy](xref:signalr/supported-platforms)
* [Centra](xref:signalr/hubs)
* [Javascriptový klient](xref:signalr/javascript-client)
