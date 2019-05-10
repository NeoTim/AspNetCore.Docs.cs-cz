---
title: Hostování v technologii ASP.NET Core SignalR produkčního prostředí a škálování
author: bradygaster
description: Zjistěte, jak se vyhnout výkonu a škálování problémů v aplikacích, které používají funkce SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/scale
ms.openlocfilehash: 4ac4509acc89d0091a3757c7cfbc9981614f29ad
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901200"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>Hostování v technologii ASP.NET Core SignalR a škálování

Podle [Andrew Stanton sestry](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), a [Petr Dykstra](https://github.com/tdykstra),

Tento článek vysvětluje, hostování a škálování důležité informace týkající se vysokým provozem aplikací, které používají funkce SignalR technologie ASP.NET Core.

## <a name="tcp-connection-resources"></a>Prostředky připojení TCP

Počet souběžných připojení TCP, která podporuje webový server je omezený. Standardní HTTP používají *dočasné* připojení. Tato připojení lze uzavřít, když klient přejde nečinnosti a později znovu otevřít. Na druhé straně připojení SignalR je *trvalé*. Připojení SignalR zůstávají otevřené i v při, klient přejde nečinnosti. V aplikace s vysokým provozem, který poskytuje mnoho klientů může způsobit tyto trvalá připojení serverů k dosažení maximálního počtu připojení.

Trvalá připojení také využívat některé další paměť, ke sledování jednotlivých připojení.

Zátěž související připojení prostředků systémem SignalR může ovlivnit jiné webové aplikace, které jsou hostovány na stejném serveru. Když SignalR otevře a obsahuje poslední dostupné připojení TCP, jiné webové aplikace na stejném serveru také mít žádná další připojení, která je jim dostupná.

Pokud serveru připojení, uvidíte soketu náhodných chyb a chyb resetování připojení. Příklad:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Zabránit používání prostředků SignalR příčinou chyb v jiných webových aplikací, běžet na různých serverech než vaše webové aplikace SignalR.

Zabránit používání prostředků SignalR příčinou chyb v aplikaci s knihovnou SignalR, horizontální navýšení kapacity na omezit počet připojení, která je na serveru pro zpracování.

## <a name="scale-out"></a>Škálování na více systémů

Aplikaci, která využívá funkce SignalR musí udržovat přehled o všech jeho připojení vytváří problémy u serverové farmy. Přidání serveru a načte nová připojení, které nemusíte vědět ostatní servery. SignalR na každém serveru v následujícím diagramu je třeba vědět o připojení na jiných serverech. Když chce, aby funkce SignalR technologie jeden ze serverů pro odeslání zprávy na všechny klienty, zprávy pouze přejde na klienty připojené k tomuto serveru.

![Bez propojovací rozhraní škálování SignalR](scale/_static/scale-no-backplane.png)

Možnosti pro řešení tohoto problému jsou [služby Azure SignalR](#azure-signalr-service) a [Redis propojovací rozhraní systému](#redis-backplane).

## <a name="azure-signalr-service"></a>Služba Azure SignalR

Služby Azure SignalR je proxy server, nikoli propojovacího rozhraní. Pokaždé, když klient zahájí připojení k serveru, se klient přesměruje k připojení ke službě. Tento proces je znázorněn v následujícím diagramu:

![Připojení ke službě Azure SignalR](scale/_static/azure-signalr-service-one-connection.png)

Výsledkem je, že služba spravuje všechna připojení klientů, zatímco každý server potřebuje pouze malý konstantní počet připojení ke službě, jak je znázorněno v následujícím diagramu:

![Klienti se připojí ke službě, servery připojené ke službě](scale/_static/azure-signalr-service-multiple-connections.png)

Tento přístup pro horizontální navýšení kapacity má několik výhod oproti alternativní Redis propojovací rozhraní systému:

* Rychlé relace, označované také jako [spřažení klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), není vyžadováno, protože klienti budou přesměrovaní okamžitě ke službě Azure SignalR, když se připojují.
* SignalR, které aplikace můžete horizontálně navýšit kapacitu podle počtu zpráv odeslaných, zatímco služby Azure SignalR automaticky škáluje, aby zpracování libovolný počet připojení. Například může být tisíců klientů, ale pokud pouze několik zpráv za sekundu se odesílají, nebudete muset horizontální navýšení kapacity na několik serverů stačí pro zpracování připojení sami aplikace SignalR.
* Aplikace s knihovnou SignalR nepoužije podstatně více připojení prostředků než webové aplikace bez SignalR.

Z těchto důvodů doporučujeme, abyste službě Azure SignalR pro všechny funkce SignalR technologie ASP.NET Core aplikací hostovaných v Azure, včetně služby App Service, virtuální počítače a kontejnery.

Další informace najdete v článku [dokumentace ke službě Azure SignalR](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Propojovací rozhraní Redis

[Redis](https://redis.io/) je párů klíč hodnota v paměti, podporující systému zasílání zpráv s modelem publikování a přihlášení k odběru. Propojovací rozhraní systému SignalR Redis používá funkci pub/sub pro předávání zpráv na jiné servery. Když se klient připojí, informace o připojení je předán do propojovacího rozhraní. Když chce, aby server pro odeslání zprávy na všechny klienty, odešle do propojovacího rozhraní. Propojovacího rozhraní zná všechny připojené klienty a které servery, které jsou uvedené na. Odešle zprávu pro všechny klienty přes jejich příslušné servery. Tento proces je znázorněn v následujícím diagramu:

![Redis propojovací rozhraní systému, zpráv odesílaných z jednoho serveru na všechny klienty](scale/_static/redis-backplane.png)

Propojovací rozhraní systému Redis je doporučený postup škálování na víc systémů pro aplikace hostované na vlastní infrastrukturu. Službě Azure SignalR není vhodným řešením pro použití v produkčním prostředí pomocí místních aplikací kvůli latenci připojení mezi datovým centrem a datového centra Azure.

Nevýhody pro propojovací rozhraní systému Redis přináší výhody služby Azure SignalR jste si předtím poznamenali:

* Rychlé relace, označované také jako [spřažení klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), je povinný. Po připojení se zahájí na serveru, má připojení zůstat na tomto serveru.
* I v případě, že jsou odesílány zprávy několik, musí aplikace SignalR horizontální navýšení kapacity na základě počtu klientů.
* Aplikace SignalR používá výrazně více prostředků, připojení než webové aplikace bez SignalR.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících materiálech:

* [Dokumentace ke službě Azure SignalR služby](/azure/azure-signalr/signalr-overview)
* [Nastavit propojovací rozhraní Redis](xref:signalr/redis-backplane)
