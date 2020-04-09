---
title: Porovnání služeb gRPC pomocí rozhraní HTTP API
author: jamesnk
description: Zjistěte, jak gRPC porovnává s HTTP API a jaké jsou doporučené scénáře.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- SignalR
uid: grpc/comparison
ms.openlocfilehash: 2dff64f1f2d67b8a1e676acf6cf131b684099750
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405873"
---
# <a name="compare-grpc-services-with-http-apis"></a>Porovnání služeb gRPC pomocí rozhraní HTTP API

Podle [James Newton-King](https://twitter.com/jamesnk)

Tento článek vysvětluje, jak [gRPC služby](https://grpc.io/docs/guides/) porovnat s HTTP API (včetně ASP.NET základní [webová api](xref:web-api/index)). Technologie používaná k poskytování rozhraní API pro vaši aplikaci je důležitou volbou a gRPC nabízí jedinečné výhody ve srovnání s rozhraními HTTP API. Tento článek popisuje silné a slabé stránky gRPC a doporučuje scénáře pro použití gRPC přes jiné technologie.

## <a name="high-level-comparison"></a>Porovnání na vysoké úrovni

Následující tabulka nabízí porovnání funkcí na vysoké úrovni mezi gRPC a HTTP API s JSON.

| Funkce          | gRPC                                               | HTTP API s JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Kontrakt         | Povinné (*.proto*)                                | Volitelné (OpenAPI)            |
| Protocol (Protokol)         | HTTP/2                                             | HTTP                          |
| Datová část          | [Protobuf (malý, binární)](#performance)           | JSON (velký, čitelný pro člověka)  |
| Normativní | [Přísná specifikace](#strict-specification)      | Volné. Všechny protokoly HTTP jsou platné.     |
| Streamování        | [Klient, server, obousměrný](#streaming)       | Klient, server                |
| Podpora prohlížečů  | [Ne (vyžaduje grpc-web)](#limited-browser-support) | Ano                           |
| Zabezpečení         | Doprava (TLS)                                    | Doprava (TLS)               |
| Generování kódu klienta | [Ano](#code-generation)                      | OpenAPI + nástroje třetích stran |

## <a name="grpc-strengths"></a>gRPC silné stránky

### <a name="performance"></a>Výkon

gRPC zprávy jsou serializovány pomocí [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), efektivní binární formát zprávy. Protobuf serializuje velmi rychle na serveru a klienta. Protobuf serializace má za následek malé datové části zpráv, důležité v omezené šířky pásma scénáře, jako jsou mobilní aplikace.

gRPC je určen pro HTTP/2, hlavní revizi HTTP, která poskytuje významné výhody výkonu oproti HTTP 1.x:

* Binární rámování a komprese. Protokol HTTP/2 je kompaktní a efektivní jak při odesílání, tak při příjmu.
* Multiplexování více volání HTTP/2 přes jedno připojení TCP. Multiplexování eliminuje [blokování hlavou linky](https://en.wikipedia.org/wiki/Head-of-line_blocking).

### <a name="code-generation"></a>Generování kódu

Všechny gRPC architektury poskytují prvotřídní podporu pro generování kódu. Jádrovým souborem pro vývoj gRPC je [soubor .proto](https://developers.google.com/protocol-buffers/docs/proto3), který definuje kontrakt služeb a zpráv gRPC. Z tohoto souboru gRPC rozhraní bude kód generovat základní třídu služby, zprávy a kompletní klienta.

Sdílením souboru *.proto* mezi serverem a klientem lze zprávy a klientský kód generovat od začátku do konce. Generování kódu klienta eliminuje duplikaci zpráv na straně klienta a serveru a vytvoří pro vás klienta silného typu. Není nutnost psát klienta šetří významné vývojové čas v aplikacích s mnoha službami.

### <a name="strict-specification"></a>Přísná specifikace

Formální specifikace pro rozhraní HTTP API s JSON neexistuje. Vývojáři debatují o nejlepším formátu adres URL, sloves HTTP a kódů odpovědí.

[Specifikace gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) je normativní ohledně formátu, který musí služba gRPC dodržovat. gRPC eliminuje debatu a šetří čas vývojářům, protože gRPC je konzistentní napříč platformami a implementacemi.

### <a name="streaming"></a>Streamování

HTTP/2 poskytuje základ pro dlouhodobé komunikační proudy v reálném čase. gRPC poskytuje prvotřídní podporu pro streamování přes HTTP/2.

Služba gRPC podporuje všechny kombinace streamování:

* Unární (bez streamování)
* Streamování ze serveru do klienta
* Streamování z klienta na server
* Obousměrné streamování

### <a name="deadlinetimeouts-and-cancellation"></a>Termíny/časové lhůty a zrušení

gRPC umožňuje klientům určit, jak dlouho jsou ochotni čekat na dokončení rpc. [Konečný termín](https://grpc.io/blog/deadlines) je odeslán na server a server může rozhodnout, jaké kroky provést, pokud překročí konečný termín. Server může například zrušit probíhající požadavky gRPC/HTTP/database v časovém výpadku.

Šíření konečného termínu a zrušení prostřednictvím podřízených volání gRPC pomáhá vynutit omezení využití prostředků.

## <a name="grpc-recommended-scenarios"></a>gRPC doporučené scénáře

gRPC je vhodný pro následující scénáře:

* **Mikroslužeb** &ndash; gRPC je určen pro nízkou latenci a vysokou propustnost komunikace. gRPC je ideální pro lehké mikroslužby, kde je důležitá účinnost.
* **Point-to-point real-time komunikace** &ndash; gRPC má vynikající podporu pro obousměrné streamování. gRPC služby mohou odesílat zprávy v reálném čase bez dotazování.
* **Polyglot prostředí** &ndash; gRPC nástroje podporuje všechny populární vývojové jazyky, takže gRPC je dobrou volbou pro vícejazyčná prostředí.
* **Síťově omezená prostředí** &ndash; gRPC zprávy jsou serializovány s Protobuf, zjednodušený formát zprávy. Zpráva gRPC je vždy menší než ekvivalentní zpráva JSON.

## <a name="grpc-weaknesses"></a>gRPC slabá místa

### <a name="limited-browser-support"></a>Omezená podpora prohlížeče

Je nemožné přímo volat gRPC služby z prohlížeče dnes. gRPC silně používá funkce HTTP/2 a žádný prohlížeč neposkytuje úroveň kontroly požadovanou nad webovými požadavky pro podporu klienta gRPC. Prohlížeče například neumožňují volajícímu vyžadovat použití protokolu HTTP/2 nebo poskytovat přístup k podkladovým rámcům HTTP/2.

[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) je další technologie od týmu gRPC, která poskytuje omezenou podporu gRPC v prohlížeči. gRPC-Web se skládá ze dvou částí: javascriptového klienta, který podporuje všechny moderní prohlížeče, a gRPC-Web proxy na serveru. Klient gRPC-Web volá proxy server a proxy server předá na gRPC požadavky na gRPC server.

Ne všechny funkce gRPC jsou podporovány gRPC-Web. Klientské a obousměrné streamování není podporováno a je omezená podpora pro streamování serveru.

> [!TIP]
> .NET Core má experimentální podporu pro gRPC-Web. Navštivte <xref:grpc/browser> pro více informací.

### <a name="not-human-readable"></a>Není čitelný pro člověka

Požadavky rozhraní HTTP API jsou odesílány jako text a mohou být přečteny a vytvořeny lidmi.

gRPC zprávy jsou ve výchozím nastavení kódovány s Protobuf. Zatímco Protobuf je efektivní pro odesílání a přijímání, jeho binární formát není čitelný pro člověka. Protobuf vyžaduje popis rozhraní zprávy zadaný v souboru *.proto,* aby správně rekonstruoval. Další nástroje jsou nutné k analýze užitečného zatížení Protobuf na drátě a k ručnímu sestavování požadavků.

Funkce, jako je [odraz serveru](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) a [nástroj příkazového řádku gRPC,](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) existují, aby pomohly s binárními zprávami Protobuf. Zprávy Protobuf také podporují [převod do a z JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). Předdefinovaný převod JSON poskytuje efektivní způsob převodu zpráv Protobuf do a z lidské čitelné formy při ladění.

## <a name="alternative-framework-scenarios"></a>Scénáře alternativního rámce

Další architektury jsou doporučeny přes gRPC v následujících scénářích:

* **Prohlížeče přístupné API** &ndash; gRPC není plně podporovánv prohlížeči. gRPC-Web může nabídnout podporu prohlížeče, ale má omezení a zavádí proxy server.
* **Vysílání komunikace v** &ndash; reálném čase gRPC podporuje komunikaci v reálném čase prostřednictvím streamování, ale koncept vysílání zprávy do registrovaných připojení neexistuje. Například ve scénáři chatovací místnosti, kde by měly být odesílány nové zprávy chatu všem klientům v chatovací místnosti, je každé volání gRPC vyžadováno pro individuální streamování nových zpráv chatu klientovi. [SignalR](xref:signalr/introduction)je užitečný rámec pro tento scénář. SignalRmá koncept trvalých připojení a vestavěnou podporu pro vysílání zpráv.
* **Meziprocesová komunikace** &ndash; Proces musí být hostitelem serveru HTTP/2, aby bylo nutné přijímat příchozí volání gRPC. Pro windows je meziprocesová komunikační [potrubí](/dotnet/standard/io/pipe-operations) rychlou a lehkou metodou komunikace.

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
