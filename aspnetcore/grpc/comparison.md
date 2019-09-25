---
title: Porovnání služeb gRPC s rozhraními API HTTP
author: jamesnk
description: Přečtěte si, jak gRPC porovnává s rozhraními API HTTP a co doporučuje scénáře.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/25/2019
uid: grpc/comparison
ms.openlocfilehash: 935078d890998fe6af366e3f6a7bf21f53c20cf7
ms.sourcegitcommit: a7813a776809a5029c94aa503ee71994f156231f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71267719"
---
# <a name="compare-grpc-services-with-http-apis"></a>Porovnání služeb gRPC s rozhraními API HTTP

Od [James Newton – král](https://twitter.com/jamesnk)

Tento článek vysvětluje, jak [gRPC služby](https://grpc.io/docs/guides/) porovnávají s rozhraními API http (včetně ASP.NET Core [webových rozhraní API](xref:web-api/index)). Technologie používaná k poskytování rozhraní API pro vaši aplikaci je důležitou volbou a gRPC nabízí jedinečné výhody v porovnání s rozhraními API protokolu HTTP. Tento článek popisuje silné a slabé stránky gRPC a doporučuje scénáře použití gRPC nad jinými technologiemi.

## <a name="high-level-comparison"></a>Porovnání na vysoké úrovni

Následující tabulka nabízí vysoké srovnání funkcí mezi gRPC a rozhraními API protokolu HTTP s JSON.

| Funkce          | gRPC                                               | HTTP API s JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Kontrakt         | Požadováno ( *.* )                                | Volitelné (OpenAPI)            |
| Přepravu        | HTTP/2                                             | HTTP                          |
| Délka          | [Protobuf (malý, binární)](#performance)           | JSON (velký, lidský čitelný)  |
| Prescriptiveness | [Striktní specifikace](#strict-specification)      | Spojování. Všechny požadavky HTTP jsou platné.      |
| Streamování        | [Klient, server, obousměrné](#streaming)       | Klient, Server                |
| Podpora prohlížeče  | [Ne (vyžaduje grpc-Web)](#limited-browser-support) | Ano                           |
| Zabezpečení         | Přenos (HTTPS)                                  | Přenos (HTTPS)             |
| Generování kódu klienta | [Ano](#code-generation)                      | OpenAPI + nástroje třetích stran |

## <a name="grpc-strengths"></a>gRPC síly

### <a name="performance"></a>Výkon

zprávy gRPC jsou serializovány pomocí [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), efektivního formátu binární zprávy. Protobuf se na server a klientovi velmi rychle serializovat. Protobuf serializace vede k malým datovým částem zprávy, důležité ve scénářích s omezenou šířkou pásma, jako jsou mobilní aplikace.

gRPC je navržená pro HTTP/2, což je hlavní revize HTTP, která poskytuje významné výhody výkonu oproti HTTP 1. x:

* Binární rámce a komprese. Protokol HTTP/2 je komprimován a efektivní při posílání i přijímání.
* Multiplexování více volání HTTP/2 přes jedno připojení TCP. Multiplexování eliminuje [blokování po řádcích](https://en.wikipedia.org/wiki/Head-of-line_blocking).

### <a name="code-generation"></a>Generování kódu

Všechny gRPC architektury poskytují prvotřídní podporu pro generování kódu. Základním souborem pro gRPC vývoj je [soubor *...* ](https://developers.google.com/protocol-buffers/docs/proto3)to definuje kontrakt služeb gRPC a zpráv. Z tohoto souboru gRPC architektury budou kód generovat základní třídu služby, zprávy a kompletního klienta.

Sdílení souboru *.* a klienta mezi serverem a klientem může být vygenerováno z koncového konce. Generování kódu klienta eliminuje duplikaci zpráv na straně klienta a serveru a pro vás vytvoří klienta se silným typem. Nemusíte psát klienta, ale v aplikacích s mnoha službami ušetříte významnou dobu vývoje.

### <a name="strict-specification"></a>Striktní specifikace

Formální specifikace pro HTTP API s JSON neexistuje. Vývojáři připravují nejlepší formát adres URL, příkazů HTTP a kódů odpovědí.

[Specifikace gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) je přednastavená na formát, který musí služba gRPC sledovat. gRPC eliminuje diskusi a šetří čas vývojáře, protože gPRC je konzistentní napříč platformami a implementacemi.

### <a name="streaming"></a>Streamování

HTTP/2 poskytuje základ pro dlouhodobé streamy komunikace v reálném čase. gRPC poskytuje prvotřídní podporu pro streamování prostřednictvím HTTP/2.

Služba gRPC podporuje všechny kombinace streamování:

* Unární (žádné streamování)
* Streamování serveru na klienta
* Streamování klienta na server
* Obousměrné streamování

### <a name="deadlinetimeouts-and-cancellation"></a>Konečný termín, vypršení časového limitu a zrušení

gRPC umožňuje klientům určit, jak dlouho mají být ochotni počkat na dokončení vzdáleného volání procedur (RPC). [Termín](https://grpc.io/blog/deadlines) se pošle na server a server se může rozhodnout, jakou akci chcete provést, pokud překročí konečný termín. Server může například zrušit probíhající požadavky gRPC/HTTP/Database na vypršení časového limitu.

Rozšiřování konečného termínu a zrušení prostřednictvím podřízených volání gRPC pomáhá vymáhat limity využití prostředků.

## <a name="grpc-recommended-scenarios"></a>Doporučené scénáře pro gRPC

gRPC je vhodný pro následující scénáře:

* **Mikroslužby** &ndash; gRPC je navržená pro zajištění nízké latence a komunikace s vysokou propustností. gRPC je ideální pro odlehčené mikroslužby, kde je efektivita nejdůležitější.
* **Komunikace v reálném čase Point-to-Point** &ndash; gRPC má skvělou podporu pro obousměrné streamování. služby gRPC mohou nabízet zprávy v reálném čase bez cyklického dotazování.
* **Prostředí Polyglot** &ndash; nástroje gRPC podporují všechny oblíbené vývojové jazyky a vytvářejí gRPC dobrou volbu pro prostředí s více jazyky.
* **Omezená prostředí sítě** &ndash; zprávy gRPC jsou serializovány pomocí Protobuf, což je odlehčený formát zprávy. Zpráva gRPC je vždy menší než ekvivalentní zpráva JSON.

## <a name="grpc-weaknesses"></a>gRPC slabé stránky

### <a name="limited-browser-support"></a>Omezená podpora prohlížeče

V dnešní době není možné přímo volat službu gRPC z prohlížeče. gRPC intenzivně používá funkce HTTP/2 a bez prohlížeče poskytuje úroveň řízení požadovaná pro webové požadavky na podporu klienta gRPC. Například prohlížeče neumožňují volajícímu vyžadovat, aby byl použit HTTP/2 nebo poskytoval přístup k podkladovým rámcům HTTP/2.

[gRPC-web](https://grpc.io/docs/tutorials/basic/web.html) je doplňková technologie od týmu gRPC, která poskytuje omezené podpory gRPC v prohlížeči. gRPC-web se skládá ze dvou částí: JavaScriptový klient, který podporuje všechny moderní prohlížeče, a webový proxy server gRPC na serveru. GRPC – webový klient volá proxy server a proxy server přepošle žádosti gRPC na server gRPC.

GRPC-web nepodporuje všechny funkce gRPC. Klient a obousměrné streamování se nepodporuje a je omezená podpora pro streamování serveru.

### <a name="not-human-readable"></a>Nečitelný člověk

Požadavky HTTP API se odesílají jako text a můžou je číst a vytvářet člověkem.

zprávy gRPC jsou ve výchozím nastavení kódované pomocí Protobuf. I když je Protobuf efektivní pro posílání a přijímání, jeho binární formát není čitelný lidmi. Protobuf vyžaduje, aby byl popis rozhraní zprávy zadaný v souboru *.* proč správně deserializován. K analýze datových částí Protobuf na lince a k vytváření požadavků rukou je potřeba další nástroje.

Funkce jako [reflexe serveru](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) a [Nástroj příkazového řádku gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) existují pro pomoc s binárními Protobuf zprávami. Také zprávy Protobuf podporují [Převod do formátu JSON a z](https://developers.google.com/protocol-buffers/docs/proto3#json)něj. Integrovaný převod JSON poskytuje efektivní způsob, jak převést zprávy Protobuf do nebo z lidského čitelného formuláře při ladění.

## <a name="alternative-framework-scenarios"></a>Scénáře alternativních platforem

Další architektury se doporučují přes gRPC v následujících scénářích:

* **Rozhraní API přístupných přes prohlížeč** &ndash; gRPC není v prohlížeči plně podporován. gRPC-web může nabízet podporu prohlížeče, ale má omezení a zavádí proxy serveru.
* **Vysílat komunikaci v reálném čase** &ndash; gRPC podporuje komunikaci v reálném čase prostřednictvím streamování, ale koncept vysílání zprávy do registrovaných připojení neexistuje. Například ve scénáři chatovací místnosti, kde mají být nové zprávy chatu odesílány všem klientům v chatovací místnosti, každé volání gRPC je požadováno k samostatnému streamování nových zpráv o konverzaci do klienta. [Návěstí](xref:signalr/introduction) je užitečnou architekturou pro tento scénář. Signalizace má koncept trvalých připojení a integrovanou podporu pro vysílání zpráv.
* **Komunikace mezi procesy** &ndash; Proces musí hostovat server HTTP/2, aby přijímal příchozí volání gRPC. Pro systém Windows je mezi procesy komunikačních [kanálů](/dotnet/standard/io/pipe-operations) rychlá a odlehčená metoda komunikace.

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
