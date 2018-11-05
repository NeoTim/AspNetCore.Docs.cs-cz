---
uid: signalr/overview/performance/signalr-connection-density-testing-with-crank
title: Funkce SignalR testování hustoty připojení nástrojem Crank | Dokumentace Microsoftu
author: Rick-Anderson
description: Testování hustoty připojení nástrojem Crank
ms.author: riande
ms.date: 02/22/2015
ms.assetid: 148d9ca7-1af1-44b6-a9fb-91e261b9b463
msc.legacyurl: /signalr/overview/performance/signalr-connection-density-testing-with-crank
msc.type: authoredcontent
ms.openlocfilehash: 556accb1bcc18e9e4d1f813a87fc6f4b67bda088
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021479"
---
<a name="signalr-connection-density-testing-with-crank"></a>Testování hustoty připojení nástrojem Crank
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek popisuje, jak nástrojem Crank k testování aplikace s Simulovaná více klientů.


Jakmile vaše aplikace běží ve své hostitelské prostředí (buď Azure webová role, služby IIS, nebo v místním prostředí pomocí Owin), můžete otestovat odpověď vysoký stupeň hustoty připojení nástrojem Crank vaší aplikace. Hostitelské prostředí může být server Internetové informační služby (IIS), hostitele služby Owin nebo webové role Azure. (Poznámka: čítače výkonu nejsou dostupné v Azure App Service Web Apps, tak nebude možné získat údaje o výkonu z testu hustoty připojení.)

Hustoty připojení odkazuje na počet současných připojení TCP, které lze navázat na serveru. Každé připojení TCP vlastní náročnější a otevření velkého počtu nečinných připojení po se nakonec vytvoří kritickým bodem paměti.

[Funkce SignalR codebase](https://github.com/signalr/signalr) zahrnuje zátěžové testování nástroj zvaný **klikové**. Nejnovější verzi Crank lze nalézt v [poboček dev.](https://github.com/SignalR/signalr/tree/dev) na Githubu. Můžete si stáhnout Zip kódová základna archivu poboček dev. funkce signalr [tady](https://github.com/SignalR/SignalR/archive/dev.zip).

Crank můžou sloužit k plně saturate paměti serveru, aby bylo možné vypočítat celkový počet nečinných připojení po na serverový hardware. Alternativně můžete také použít Crank pro zátěžový test serveru v rámci určité množství tlaku na paměť, podle ramping připojení, dokud nebude dosaženo konkrétní počet nebo mezní hodnoty pro konkrétní paměť.

Při testování, je důležité použít vzdálené klientů, aby žádné soutěže pro prostředky (například připojení TCP a paměť). Monitorování klientů k zajištění, že nedosahují případné kritické body, které může server zabránit v dosažení jeho kapacita (paměť nebo procesor). Potřebujete zvýšit počet klientů, které pokud chcete plně zatížení serveru.

### <a name="running-a-connection-density-test"></a>Spuštění testu hustoty připojení

Tato část popisuje kroky potřebné ke spuštění testu hustoty připojení na žádost SignalR.

1. Stáhněte si a sestavte [poboček dev. funkce signalr codebase](https://github.com/SignalR/SignalR/archive/dev.zip). V příkazovém řádku přejděte do &lt;adresáře projektu&gt;\src\Microsoft.AspNet.SignalR.Crank\bin\debug.
2. Nasazení aplikace na jeho odpovídající hostitelské prostředí. Poznamenejte si koncový bod, který vaše aplikace používá; například aplikace vytvořené v [kurzu Začínáme](../getting-started/tutorial-getting-started-with-signalr.md), je koncový bod `http://<yourhost>:8080/signalr`.
3. Nainstalujte [čítačů výkonu SignalR](signalr-performance.md#perfcounters) na serveru. Pokud vaše aplikace běží v Azure, přečtěte si téma [pomocí čítačů výkonu SignalR webové role Azure](using-signalr-performance-counters-in-an-azure-web-role.md).

Jakmile jste stáhli a integrované základu kódu a nainstalovat čítače výkonu hostitele, nástroj příkazového řádku Crank najdete v `src\Microsoft.AspNet.SignalR.Crank\bin\Debug` složky.

Mezi dostupné možnosti pro nástroj Crank patří:

- **/?** : Zobrazí obrazovka s nápovědou. Dostupné možnosti se zobrazí také v případě **Url** parametr se vynechá.
- **/ Adresa Url**: adresa URL pro připojení SignalR. Tento parametr je povinný. V případě použití výchozí mapování aplikace SignalR cestu skončí za "/ signalr".
- **/ Přenosu**: název přenosu použít. Výchozí hodnota je `auto`, který vybere nejlepší dostupný protokol. Mezi možnosti patří `WebSockets`, `ServerSentEvents`, a `LongPolling` (`ForeverFrame` není možné zvolit pro Crank, od klientů .NET spíše než Internet Explorer se používá). Další informace o způsobu SignalR vybere přenosy, naleznete v tématu [přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports).
- **/ BatchSize**: počet klientů přidaných v každé dávce. Výchozí hodnota je 50.
- **/ ConnectInterval**: interval v milisekundách mezi přidáním připojení. Výchozí hodnota je 500.
- **/ Připojení**: počet připojení používaná pro zátěžový test aplikace. Výchozí hodnota je 100 000.
- **/ ConnectTimeout**: časový limit v sekundách před přerušením testu. Výchozí hodnota je 300.
- **MinServerMBytes**: minimální server megabajtů k dosažení. Výchozí hodnota je 500.
- **SendBytes**: velikost datová část odeslaná na server v bajtech. Výchozí hodnota je 0.
- **SendInterval**: zpoždění v milisekundách mezi zprávy na server. Výchozí hodnota je 500.
- **SendTimeout**: časový limit v milisekundách pro zprávy na server. Výchozí hodnota je 300.
- **ControllerUrl**: adresa Url, kde jeden klient bude hostitelem centra kontroleru. Výchozí hodnota je null (žádné Centrum kontroleru). Centrum kontroleru je spuštěn při spuštění relace Crank; dál je navázáno spojení mezi centrem kontroleru a Crank.
- **NumClients**: počet simulovaných klientům připojit se k aplikaci. Výchozí hodnota je jeden.
- **Soubor protokolu**: název souboru pro soubor protokolu pro testovací běh. Výchozí hodnota je `crank.csv`.
- **SampleInterval**: čas v milisekundách mezi vzorky čítače výkonu. Výchozí hodnota je 1000.
- **SignalRInstance**: název instance čítačů výkonu na serveru. Výchozí hodnota je používání stavu připojení klienta.

### <a name="example"></a>Příklad

Následující příkaz otestuje lokality volá `pfsignalr` v Azure, který je hostitelem aplikace na portu 8080 s centrem s názvem "ControllerHub", pokud použijete 100 připojení.

`crank /Connections:100 /Url:http://pfsignalr.cloudapp.net:8080/signalr`
