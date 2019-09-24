---
title: Hostování a škálování provozního signalizace ASP.NET Core
author: bradygaster
description: Naučte se, jak se vyhnout problémům s výkonem a škálováním v aplikacích, které používají ASP.NET Core Signal.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/scale
ms.openlocfilehash: 26b02cffdd472fc21dc4aee7052a0ba939b82c0f
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211737"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>Hostování a škálování signalizace ASP.NET Core

Autor [: Andrew Stanton – sestry](https://twitter.com/anurse), [Brady gastera](https://twitter.com/bradygaster)a [Dykstra](https://github.com/tdykstra);

Tento článek vysvětluje informace o hostování a škálování pro aplikace s vysokým provozem, které používají ASP.NET Core Signal.

## <a name="sticky-sessions"></a>Rychlé relace

Signál vyžaduje, aby všechny požadavky HTTP pro konkrétní připojení byly zpracovávány stejným procesem serveru. Když je signál spuštěn na serverové farmě (více serverů), musí se použít rychlé relace. U některých nástrojů pro vyrovnávání zatížení se také nazývají spřažení relací. Azure App Service pro směrování požadavků používá [Směrování žádostí na aplikace](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR). Povolením nastavení spřažení ARR v Azure App Service umožníte "rychlé relace". Jediné okolnosti, kdy nejsou vyžadovány rychlé relace, jsou:

1. Při hostování na jednom serveru v jednom procesu.
1. Při použití služby signalizace Azure.
1. Pokud jsou všichni klienti nakonfigurovaní tak, aby používali **jenom** objekty WebSockets, **a** v konfiguraci klienta je povolené [Nastavení SkipNegotiation](xref:signalr/configuration#configure-additional-options) .

Ve všech ostatních případech (včetně toho, kdy se používá Redis), musí být serverové prostředí nakonfigurované pro rychlé relace.

Pokyny ke konfiguraci Azure App Service pro Signal najdete v tématu <xref:signalr/publish-to-azure-web-app>.

## <a name="tcp-connection-resources"></a>Prostředky připojení TCP

Počet souběžných připojení TCP, která může webový server podporovat, je omezený. Standardní klienti HTTP používají *dočasné* připojení. Tato připojení se dají zavřít, když se klient přestane vycházet a znovu otevřít později. Na druhé straně je připojení k signalizaci *trvalé*. Připojení k signalizaci zůstávají otevřená i v případě, že klient přestane být činný. V aplikaci s vysokým provozem, která obsluhuje mnoho klientů, můžou tato trvalá připojení způsobit, že by servery dosáhly maximálního počtu připojení.

Trvalá připojení také využívají určitou další paměť, aby bylo možné sledovat jednotlivá připojení.

Těžké používání prostředků souvisejících s připojením pomocí signálů může mít vliv na další webové aplikace, které jsou hostovány na stejném serveru. Když se signál otevře a zobrazí poslední dostupná připojení TCP, další webové aplikace na stejném serveru také nemají k dispozici žádná další připojení.

Pokud server nemá připojení, zobrazí se chyby náhodného soketu a chyby resetování připojení. Příklad:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Aby bylo možné zabránit využití prostředků signalizace v jiných webových aplikacích, spouštějte signály na různých serverech, než jsou vaše jiné webové aplikace.

Aby bylo možné zabránit využití prostředků signalizace v aplikaci signalizace, nahorizontální navýšení kapacity a omezení počtu připojení, které server musí zpracovat.

## <a name="scale-out"></a>Škálování na více systémů

Aplikace, která používá signalizaci, musí sledovat všechna připojení, což vytváří problémy pro serverovou farmu. Přidejte server a získá nová připojení, o kterých ostatní servery nevědí. Například signál na každém serveru v následujícím diagramu neznáte připojení na ostatních serverech. Když signál na jednom ze serverů chce poslat zprávu všem klientům, zpráva se dostane jenom na klienty připojené k tomuto serveru.

![Škálování signálu bez plánu](scale/_static/scale-no-backplane.png)

Možnosti řešení tohoto problému jsou [služby signalizace Azure](#azure-signalr-service) a [Redis replánování](#redis-backplane).

## <a name="azure-signalr-service"></a>Služba Azure SignalR

Služba signalizace Azure je proxy místo pro plán. Pokaždé, když klient inicializuje připojení k serveru, klient se přesměruje, aby se připojil ke službě. Tento proces je znázorněný v následujícím diagramu:

![Navázání připojení ke službě Azure Signal](scale/_static/azure-signalr-service-one-connection.png)

Výsledkem je, že služba spravuje všechna připojení klientů, zatímco každý server potřebuje jenom malý konstantní počet připojení ke službě, jak je znázorněno v následujícím diagramu:

![Klienti připojení ke službě, servery připojené ke službě](scale/_static/azure-signalr-service-multiple-connections.png)

Tento přístup k horizontálnímu navýšení kapacity má oproti alternativním Redismu plánu několik výhod:

* Rychlé relace, označované také jako [Spřažení klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), se nevyžadují, protože klienti se při připojení okamžitě přesměrují do služby Azure Signal.
* Aplikace Signal se může škálovat na základě počtu odeslaných zpráv, zatímco služba signálu Azure se automaticky škáluje tak, aby zpracovávala libovolný počet připojení. Může se například jednat o tisíce klientů, ale pokud se pošle jenom pár zpráv za sekundu, aplikace Signal se nebude muset škálovat na víc serverů, aby se daly samotné připojení zvládnout.
* Aplikace Signaler nepoužívá podstatně více prostředků připojení, než je webová aplikace bez signalizace.

Z těchto důvodů doporučujeme službu Azure Signal Service pro všechny aplikace signalizace ASP.NET Core hostované v Azure, včetně App Service, virtuálních počítačů a kontejnerů.

Další informace najdete v [dokumentaci ke službě Azure Signal](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Propojovací rozhraní Redis

[Redis](https://redis.io/) je úložiště klíč-hodnota v paměti, které podporuje systém zasílání zpráv s modelem publikování/odběru. Redis pro vyřízení signálu používá funkci Pub/sub k posílání zpráv na jiné servery. Když klient vytvoří připojení, informace o připojení se předávají do plánu. Když server chce poslat zprávu všem klientům, pošle se do plánu. Schéma pro naplánování zná všechny připojené klienty a servery, na kterých se nachází. Pošle zprávu všem klientům přes jejich příslušné servery. Tento proces je znázorněný v následujícím diagramu:

![Redis replánování, zpráva odeslaná z jednoho serveru všem klientům](scale/_static/redis-backplane.png)

Redis replánování je doporučený postup pro horizontální navýšení kapacity pro aplikace hostované ve vaší vlastní infrastruktuře. Služba Azure Signaler není praktickou možností pro produkční použití s místními aplikacemi kvůli latenci připojení mezi datovým centrem a datovým centrem Azure.

Výše zmíněné výhody služby signálů Azure jsou nevýhody pro Redis replánování:

* Povinná relace, která se označuje také jako [Spřažení klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), je povinná. Po zahájení připojení na serveru musí připojení zůstat na tomto serveru.
* Aplikace Signal musí škálovat na základě počtu klientů i v případě, že je odesíláno několik zpráv.
* Aplikace Signaler používá podstatně větší prostředky připojení než u webové aplikace bez signalizace.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících materiálech:

* [Dokumentace ke službě Azure Signal](/azure/azure-signalr/signalr-overview)
* [Nastavení Redisho plánu](xref:signalr/redis-backplane)
