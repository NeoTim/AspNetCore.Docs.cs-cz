---
title: ASP.NET Core SignalR hostování a škálování v produkčním prostředí
author: bradygaster
description: Naučte se, jak se vyhnout problémům s výkonem a škálováním v aplikacích, které používají ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/scale
no-loc:
- SignalR
ms.openlocfilehash: a215ce489746a7585cf4e72d4f04e0eac588b44c
ms.sourcegitcommit: a7bbe3890befead19440075b05b9674351f98872
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905727"
---
# <a name="aspnet-core-opno-locsignalr-hosting-and-scaling"></a>ASP.NET Core SignalR hostování a škálování

Autor [: Andrew Stanton – sestry](https://twitter.com/anurse), [Brady gastera](https://twitter.com/bradygaster)a [Dykstra](https://github.com/tdykstra);

Tento článek vysvětluje, jaké jsou informace o hostování a škálování pro aplikace s vysokým provozem, které používají ASP.NET Core SignalR.

## <a name="sticky-sessions"></a>Rychlé relace

SignalR vyžaduje, aby všechny požadavky HTTP pro konkrétní připojení byly zpracovávány stejným procesem serveru. Když SignalR běží na serverové farmě (více serverů), musí se použít "rychlé relace". U některých nástrojů pro vyrovnávání zatížení se také nazývají spřažení relací. Azure App Service pro směrování požadavků používá [Směrování žádostí na aplikace](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR). Povolením nastavení spřažení ARR v Azure App Service umožníte "rychlé relace". Jediné okolnosti, kdy nejsou vyžadovány rychlé relace, jsou:

1. Při hostování na jednom serveru v jednom procesu.
1. Při použití služby Azure SignalR.
1. Pokud jsou všichni klienti nakonfigurovaní tak, aby používali **jenom** objekty WebSockets, **a** v konfiguraci klienta je povolené [Nastavení SkipNegotiation](xref:signalr/configuration#configure-additional-options) .

Ve všech ostatních případech (včetně toho, kdy se používá Redis), musí být serverové prostředí nakonfigurované pro rychlé relace.

Pokyny ke konfiguraci Azure App Service pro SignalRnajdete v tématu <xref:signalr/publish-to-azure-web-app>.

## <a name="tcp-connection-resources"></a>Prostředky připojení TCP

Počet souběžných připojení TCP, která může webový server podporovat, je omezený. Standardní klienti HTTP používají *dočasné* připojení. Tato připojení se dají zavřít, když se klient přestane vycházet a znovu otevřít později. Na druhé straně je připojení SignalR *trvalé*. SignalR připojení zůstávají otevřená i v případě, že klient přestane být činný. V aplikaci s vysokým provozem, která obsluhuje mnoho klientů, můžou tato trvalá připojení způsobit, že by servery dosáhly maximálního počtu připojení.

Trvalá připojení také využívají určitou další paměť, aby bylo možné sledovat jednotlivá připojení.

Těžké využívání prostředků souvisejících s připojením pomocí SignalR může ovlivnit další webové aplikace, které jsou hostovány na stejném serveru. Když se SignalR otevře a zobrazí poslední dostupná připojení TCP, další webové aplikace na stejném serveru také nemají k dispozici žádná další připojení.

Pokud server nemá připojení, zobrazí se chyby náhodného soketu a chyby resetování připojení. Příklad:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Pokud chcete zachovat SignalR využití prostředků v jiných webových aplikacích, spusťte SignalR na různých serverech, než jsou vaše jiné webové aplikace.

Aby SignalR využití prostředků nezpůsobilo chyby v SignalR aplikaci, nahorizontální navýšení kapacity a omezení počtu připojení, které server musí zpracovat.

## <a name="scale-out"></a>Škálování na více systémů

Aplikace, která používá SignalR potřebuje udržet přehled o všech připojeních, což vytváří problémy pro serverovou farmu. Přidejte server a získá nová připojení, o kterých ostatní servery nevědí. Například SignalR na každém serveru v následujícím diagramu nevědí o připojeních na ostatních serverech. Když SignalR na jednom ze serverů chce poslat zprávu všem klientům, zpráva se dostane jenom na klienty připojené k tomuto serveru.

![Škálování [! Evřít. NO – LOC (Signal)] bez plánu](scale/_static/scale-no-backplane.png)

Možnosti řešení tohoto problému jsou [služby Azure SignalR](#azure-signalr-service) a [Redis replánování](#redis-backplane).

## <a name="azure-opno-locsignalr-service"></a>Služba Azure SignalR

Služba Azure SignalR je proxy místo pro plán. Pokaždé, když klient inicializuje připojení k serveru, klient se přesměruje, aby se připojil ke službě. Tento proces je znázorněný v následujícím diagramu:

![Navazování připojení k Azure [! Evřít. Služba NO-LOC (Signal)]](scale/_static/azure-signalr-service-one-connection.png)

Výsledkem je, že služba spravuje všechna připojení klientů, zatímco každý server potřebuje jenom malý konstantní počet připojení ke službě, jak je znázorněno v následujícím diagramu:

![Klienti připojení ke službě, servery připojené ke službě](scale/_static/azure-signalr-service-multiple-connections.png)

Tento přístup k horizontálnímu navýšení kapacity má oproti alternativním Redismu plánu několik výhod:

* Relace s rychlým připojením, označované také jako [Spřažení klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), nejsou vyžadovány, protože klienti jsou při připojení okamžitě přesměrováni na službu Azure SignalR.
* Aplikace SignalR se může škálovat na základě počtu odeslaných zpráv, zatímco služba Azure SignalR se automaticky škáluje tak, aby zpracovávala libovolný počet připojení. Může to být třeba tisíce klientů, ale pokud se pošle jenom pár zpráv za sekundu, SignalR aplikace nebude muset škálovat na víc serverů, aby se mohla zpracovávat samotná připojení.
* Aplikace SignalR nepoužívá podstatně více prostředků připojení, než je webová aplikace bez SignalR.

Z těchto důvodů doporučujeme službu Azure SignalR pro všechny aplikace ASP.NET Core SignalR hostované v Azure, včetně App Service, virtuálních počítačů a kontejnerů.

Další informace najdete v [dokumentaci ke službě Azure SignalR](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Propojovací rozhraní Redis

[Redis](https://redis.io/) je úložiště klíč-hodnota v paměti, které podporuje systém zasílání zpráv s modelem publikování/odběru. SignalR Redis replánování používá funkci Pub/sub k posílání zpráv na jiné servery. Když klient vytvoří připojení, informace o připojení se předávají do plánu. Když server chce poslat zprávu všem klientům, pošle se do plánu. Schéma pro naplánování zná všechny připojené klienty a servery, na kterých se nachází. Pošle zprávu všem klientům přes jejich příslušné servery. Tento proces je znázorněný v následujícím diagramu:

![Redis replánování, zpráva odeslaná z jednoho serveru všem klientům](scale/_static/redis-backplane.png)

Redis replánování je doporučený postup pro horizontální navýšení kapacity pro aplikace hostované ve vaší vlastní infrastruktuře. Služba Azure SignalR není praktickou možností pro produkční použití s místními aplikacemi kvůli latenci připojení mezi datovým centrem a datovým centrem Azure.

Výše zmíněné výhody služby Azure SignalR jsou nevýhody pro Redis replánování:

* Povinná relace, která se označuje také jako [Spřažení klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), je povinná. Po zahájení připojení na serveru musí připojení zůstat na tomto serveru.
* SignalR aplikace musí škálovat na základě počtu klientů i v případě, že je odesíláno několik zpráv.
* SignalR aplikace používá podstatně více prostředků připojení, než je webová aplikace bez SignalR.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících materiálech:

* [Dokumentace ke službě Azure SignalR](/azure/azure-signalr/signalr-overview)
* [Nastavení Redisho plánu](xref:signalr/redis-backplane)
