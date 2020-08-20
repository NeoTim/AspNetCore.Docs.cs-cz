---
title: Úvod do ASP.NET Core SignalR
author: bradygaster
description: Přečtěte si, jak SignalR knihovna ASP.NET Core zjednodušuje přidávání funkcí v reálném čase do aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: ab850fa8afbee9d2664868937362388a03374908
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634693"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a>Úvod do ASP.NET Core SignalR

## <a name="what-is-no-locsignalr"></a>Co je to SignalR ?

ASP.NET Core SignalR je open source knihovna, která zjednodušuje přidávání webových funkcí v reálném čase do aplikací. Webová funkce v reálném čase umožňuje, aby kód na straně serveru nabízel okamžitý obsah klientům.

Dobré kandidáty pro SignalR :

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

Zdroj je hostovaný v [ SignalR úložišti na GitHubu](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).

## <a name="transports"></a>Přenosy

SignalR podporuje následující techniky pro zpracování komunikace v reálném čase (v pořadí podle řádného použití):

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* Události odeslané serverem
* Dlouhé cyklické dotazování

SignalR automaticky zvolí nejlepší přenosovou metodu, která je v rámci schopností serveru a klienta.

## <a name="hubs"></a>Rozbočovače

SignalR používá *centra* ke komunikaci mezi klienty a servery.

Centrum je kanál vysoké úrovně, který umožňuje klientovi a serveru volat metody na sebe navzájem. SignalR zpracovává odesílání mezi hranicemi počítačů automaticky a umožňuje klientům volat metody na serveru a naopak. Můžete předat parametry silného typu metodám, které umožňují vazbu modelu. SignalR poskytuje dva integrované protokoly rozbočovače: textový protokol založený na formátu JSON a binární protokol založený na [MessagePack](https://msgpack.org/).  MessagePack obvykle vytváří menší zprávy ve srovnání se JSON. Aby bylo možné poskytovat podporu protokolu MessagePack, starší prohlížeče musí podporovat [XHR úrovně 2](https://caniuse.com/#feat=xhr2) .

Centra volají kód na straně klienta odesláním zpráv, které obsahují název a parametry metody na straně klienta. Objekty odeslané jako parametry metody jsou deserializovány pomocí nakonfigurovaného protokolu. Klient se pokusí porovnat název s metodou v kódu na straně klienta. Když klient najde shodu, zavolá metodu a předá jí deserializovaná data parametrů.

## <a name="additional-resources"></a>Další zdroje informací

* [Začínáme s nástrojem SignalR pro ASP.NET Core](xref:tutorials/signalr)
* [Podporované platformy](xref:signalr/supported-platforms)
* [Rozbočovače](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
