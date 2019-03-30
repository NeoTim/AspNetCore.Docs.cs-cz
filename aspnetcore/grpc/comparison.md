---
title: Porovnání služeb gRPC pomocí rozhraní HTTP API
author: jamesnk
description: Zjistěte, jak gRPC porovná s HTTP rozhraní API a je rozdělená doporučujeme, abyste se scénáře.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/26/2019
uid: grpc/comparison
ms.openlocfilehash: 280d0c2be2a83e5d80cedeaa472e33c28ac983f9
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750496"
---
# <a name="comparing-grpc-and-http-apis"></a>Porovnání gRPC a rozhraní API HTTP

Podle [James Newton – King](https://twitter.com/jamesnk)

Tento článek uvádí srovnání mezi [gRPC](https://grpc.io/docs/guides/) a rozhraní API HTTP a doporučuje scénáře použití gRPC přes jiné technologie.

#### <a name="overview"></a>Přehled

|    Funkce             |    gRPC                                                 |    Rozhraní API HTTP s JSON                       |
|------------------------|---------------------------------------------------------|----------------------------------------------|
|    Kontrakt            |    Vyžaduje (`*.proto`)                                 |    Nepovinné (OpenAPI)                        |
|    Přenos           |    HTTP/2                                               |    HTTP                                      |
|    datová část             |    [Protobuf (malá, binární soubor)](#performance)             |    JSON (velké lidsky čitelné)              |
|    Prescriptiveness    |    [Striktní specifikace](#strict-specification)        |    Volné. Platí všechny HTTP                  |
|    Streamování           |    [Klient, server, obousměrné](#streaming)         |    Klient, server                            |
|    Podpora prohlížeče     |    [Ne (vyžaduje webovou grpc)](#limited-browser-support)   |    Ano                                       |
|    Zabezpečení            |    Přenosu (HTTPS)                                    |    Přenosu (HTTPS)                         |
|    Klient kód – obecné     |    [Ano](#code-generation)                              |    OpenAPI a nástroje třetích stran             |

## <a name="grpc-strengths"></a>gRPC síly

### <a name="performance"></a>Výkon

gRPC zprávy se serializují pomocí [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), efektivní binárního formátu. Protobuf serializuje velmi rychle na serveru a klienta. Protobuf serializace výsledků v krátké zprávy vytížení, důležité ve scénářích s omezenou šířkou pásma, jako jsou mobilní aplikace.

gRPC je navržená pro HTTP/2, hlavní revize protokolu HTTP, která poskytuje výhody výkonu přes protokol HTTP 1.x:

* Binární rámce a provádí kompresi. Protokol HTTP/2 je kompaktního a efektivního v odesílání a příjem.
* Multiplexing přes jedno připojení TCP více volání HTTP/2. Multiplexing eliminuje [head řádku blokování](https://en.wikipedia.org/wiki/Head-of-line_blocking).

### <a name="code-generation"></a>Vytvoření kódu

Všechna rozhraní gRPC poskytovat prvotřídní podporu pro generování kódu. Základní soubor pro vývoj gRPC je [ `*.proto` souboru](https://developers.google.com/protocol-buffers/docs/proto3), určující kontakt gRPC služeb a zprávy. Z tohoto souboru gRPC rozhraní kód vygeneruje základní třídu služby, zprávy a kompletní klienta.

Při sdílení `*.proto` souborů mezi serverem a klienta, zprávy a klientský kód se dá vygenerovat na ukončení do konce. Generování kódu klienta eliminuje duplicitních zpráv na klientovi a serveru a vytvoří klienta silného typu za vás. Nebudete muset psát klienta šetří čas významné vývoje v aplikacích s řadou služeb.

### <a name="strict-specification"></a>Striktní specifikace

gRPC šetří čas pro vývojáře prostřednictvím jeho jednoduchost. [GRPC specifikace](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) je doporučený o vypadá metody gRPC služby. Neexistuje žádné oficiální smlouvu, jaká rozhraní API protokolu HTTP s JSON by měl vypadat takto. Chybějící smlouva vytvoří diskuse nad formát adresy URL, příkazy HTTP a kódů odpovědi. gRPC eliminuje diskusi specifikace, která uvádí, co musí metoda gRPC vypadat.

### <a name="streaming"></a>Streamování

HTTP/2 poskytuje základ pro datové proudy s dlouhým poločasem rozpadu komunikaci v reálném čase. gRPC poskytuje prvotřídní podporu pro vysílání datového proudu prostřednictvím protokolu HTTP/2.

GRPC service podporuje všechny kombinace streamování:

* Unární (žádné streamování)
* Serveru ke klientovi streamování
* Klienta na server streamování
* Obousměrné streamování

### <a name="deadlinetimeouts-and-cancellation"></a>Konečný termín nebo vypršení časového limitu a zrušení

gRPC umožňuje klientům k určení, jak dlouho jsou ochotni čekání na dokončení vzdáleného volání Procedur. [Konečný termín](https://grpc.io/blog/deadlines) je odeslána na server a server můžete rozhodnout, jaká akce se má provést pokud překročí konečného termínu. Například server může zrušit požadavků v průběhu gRPC/HTTP/databáze na vypršení časového limitu.

Probíhá šíření termínu a zrušení prostřednictvím gRPC podřízené volání pomáhá vynutit limity využití prostředků.

## <a name="grpc-recommended-scenarios"></a>gRPC Doporučené scénáře

gRPC se skvěle hodí pro následující scénáře:

* **Mikroslužby** -gRPC je navržený s nízkou latencí a vysokou propustnost komunikace. gRPC se skvěle hodí pro lightweight mikroslužeb kde efektivita je velmi důležité.
* **Komunikace v reálném čase Point-to-Point** -gRPC má skvělou podporu pro obousměrné streamování. gRPC služby můžete nabízet zprávy bez dotazování v reálném čase.
* **Prostředí Polygot** -gRPC nástrojů podporuje všechny oblíbené vývojářské jazyky, provádění gRPC dobrou volbou pro vícejazyčné prostředí.
* **Síťové prostředí omezené** -gRPC zprávy jsou serializovat s příznakem Protobuf, zjednodušené formátu. GRPC zpráva bude vždy menší než ekvivalentní zprávy JSON.

## <a name="grpc-weaknesses"></a>gRPC slabá místa

### <a name="limited-browser-support"></a>Podpora omezené prohlížeče

Není možné přímo volat službu gRPC z prohlížeče ještě dnes. gRPC výrazně využívá funkce HTTP/2, a žádný prohlížeč vyžaduje přes webové požadavky pro podporu klienta gRPC řízení. Například prohlížeče umožňují volajícímu vyžaduje, aby se používané HTTP/2, ani poskytují přístup k podkladové HTTP/2 rámce.

[gRPC webové](https://grpc.io/docs/tutorials/basic/web.html) je další technologie od gRPC týmu, který podporuje omezenou gRPC v prohlížeči. gRPC – Web se skládá ze dvou částí: JavaScript klienta, který podporuje všechny moderní prohlížeče a gRPC webový proxy server na serveru. GRPC webový klient volá proxy serveru a na gRPC požadavky na gRPC server bude předávat proxy serveru.

Ne všechny funkce gRPC jsou podporovány gRPC – Web. Klient a obousměrné streamování nepodporuje a je dostupná omezená podpora pro server streamování.

### <a name="not-human-readable"></a>Není lidské čitelná

Žádosti protokolu HTTP rozhraní API se odesílají jako text a může číst a je vytvořen člověku.

gRPC zprávy jsou zakódovány Protobuf ve výchozím nastavení. Protobuf je efektivní odesílat a přijímat, jeho binárním formátu není lidské čitelné. Popis rozhraní zprávy podle vyžaduje Protobuf `*.proto` soubor správně deserializovat. Další nástroje musí být používán k analýze datové části Protobuf na lince a k vytvoření žádosti o ručně.
Funkce, jako jsou [server reflexe](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) a [nástroj příkazového řádku gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) slouží k získání vyhnout uvedeným potížím.
Navíc Protobuf zprávy podporu [převodu do a z JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). Předdefinovaný převod JSON poskytuje vhodný způsob, jak převést protobuf zprávy v podobě čitelné člověkem při ladění.

## <a name="alternative-framework-scenarios"></a>Alternativní Framework scénáře

Další architektury se doporučuje přes gRPC v následujících scénářích:

* **Dostupná rozhraní API Browser** -gRPC není plně podporované v prohlížeči. gRPC – Web může nabídnout podpora prohlížeče, ale má omezení a zavádí proxy serveru.
* **Vysílání komunikaci v reálném čase** – gRPC podporuje komunikaci prostřednictvím datových proudů v reálném čase, ale nemá koncept všesměrové vysílání zpráv navýšení kapacity na připojeních. Například ve scénáři chatovací místnosti, které by měla být odeslána nové zprávy chatu pro všechny klienty v chatovací místnosti, by každé volání gRPC muset jednotlivě datový proud stream nové zprávy chatu do klienta. [Funkce SignalR](xref:signalr/introduction) je vhodné rozhraní pro tento scénář. Má koncept trvalým připojením a integrovanou podporu pro vysílání zpráv.
* **Komunikace mezi zpracovat** – proces by bylo potřeba hostovat server HTTP/2 gRPC tak, aby přijímal příchozí volání. Pro Windows komunikaci mezi zpracovat [pojmenované kanály s použitím technologie WCF](/dotnet/framework/wcf/feature-details/choosing-a-transport#when-to-use-the-named-pipe-transport) je rychlý a jednoduchý způsob komunikace.

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
