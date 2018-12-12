---
uid: signalr/overview/older-versions/scaleout-with-redis
title: Škálování aplikace SignalR službou Redis (SignalR 1.x) | Dokumentace Microsoftu
author: MikeWasson
description: ''
ms.author: riande
ms.date: 05/01/2013
ms.assetid: 6abecf80-8ffa-41ba-b0d9-1d9edbe7687b
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-redis
msc.type: authoredcontent
ms.openlocfilehash: 3f70536a4bda90a0298f6bb4f7ef47b8a257175b
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287543"
---
<a name="signalr-scaleout-with-redis-signalr-1x"></a>Škálování aplikace SignalR službou Redis (SignalR 1.x)
====================
podle [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

V tomto kurzu budete používat [Redis](http://redis.io/) k distribuci zpráv do aplikace SignalR, který je nasazen na dvou samostatných instancí služby IIS.

Redis je úložiště klíč / hodnota v paměti. Podporuje také systému zasílání zpráv s modelem publikování a přihlášení k odběru. Propojovací rozhraní systému SignalR Redis používá funkci pub/sub pro předávání zpráv na jiné servery.

![](scaleout-with-redis/_static/image1.png)

Pro účely tohoto kurzu budete používat tři servery:

- Dva servery se systémem Windows, který použijete k nasazení aplikace SignalR.
- Jeden server se systémem Linux, který použijete ke spuštění Redis. Snímky obrazovky v tomto kurzu můžu použít Ubuntu 12.04 TLS.

Pokud nemáte tří fyzických serverů pro použití, můžete vytvořit virtuální počítače v Hyper-V. Další možností je vytvořit virtuální počítače v Azure.

I když v tomto kurzu používá oficiální implementace Redis, k dispozici je také [Windows port Redis](https://github.com/MSOpenTech/redis) z MSOpenTech. Instalace a konfigurace se liší, ale jinak kroky jsou stejné.

> [!NOTE] 
> 
> Škálování aplikace SignalR službou Redis nepodporuje clustery Redis.


## <a name="overview"></a>Přehled

Předtím, než získáme podrobný kurz, zde je rychlý přehled toho, co budete dělat.

1. Nainstalujte Redis a spusťte Redis server.
2. Přidejte tyto balíčky NuGet pro vaši aplikaci: 

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.Redis](http://nuget.org/packages/Microsoft.AspNet.SignalR.Redis)
3. Vytvoření aplikace SignalR.
4. Přidejte následující kód do souboru Global.asax konfigurace propojovacího rozhraní: 

    [!code-csharp[Main](scaleout-with-redis/samples/sample1.cs)]

## <a name="ubuntu-on-hyper-v"></a>Ubuntu na Hyper-V

Pomocí Windows Hyper-V, můžete snadno vytvořit virtuální počítač s Ubuntu ve Windows serveru.

Stáhněte si bitovou kopii ISO se systémem Ubuntu z [ http://www.ubuntu.com ](http://www.ubuntu.com/).

Technologie Hyper-V přidejte nový virtuální počítač. V **připojit virtuální pevný Disk** kroku, vyberte **virtuální pevný disk vytvoříte**.

![](scaleout-with-redis/_static/image2.png)

V **možnosti instalace** kroku, vyberte **soubor bitové kopie (.iso)**, klikněte na tlačítko **Procházet**a přejděte do instalační Ubuntu ISO.

![](scaleout-with-redis/_static/image3.png)

## <a name="install-redis"></a>Nainstalujte Redis

Postupujte podle kroků uvedených v [ http://redis.io/download ](http://redis.io/download) ke stažení a sestavení Redis.

[!code-console[Main](scaleout-with-redis/samples/sample2.cmd)]

Tento postup sestaví binární soubory Redis `src` adresáře.

Ve výchozím nastavení Redis, aby heslo. Chcete-li nastavit heslo, upravte `redis.conf` soubor, který je umístěn v kořenovém adresáři zdrojového kódu. (Před zahájením úprav, vytvořili záložní kopii souboru!) Přidejte následující direktivy k `redis.conf`:

[!code-powershell[Main](scaleout-with-redis/samples/sample3.ps1)]

Nyní spusťte Redis server:

[!code-css[Main](scaleout-with-redis/samples/sample4.css)]

![](scaleout-with-redis/_static/image4.png)

Otevřený port 6379, což je výchozí port, který Redis naslouchá. (Můžete změnit číslo portu v konfiguračním souboru.)

## <a name="create-the-signalr-application"></a>Vytvoření aplikace SignalR

Vytvoření aplikace SignalR pomocí některé z těchto kurzů:

- [Začínáme s knihovnou SignalR](../getting-started/tutorial-getting-started-with-signalr.md)
- [Začínáme s knihovnou SignalR a MVC 4](tutorial-getting-started-with-signalr-and-mvc-4.md)

V dalším kroku upravíme, aby byl chatovací aplikaci, aby podporovala škálování s využitím Redis. Nejprve přidejte balíček SignalR.Redis NuGet do projektu. V aplikaci Visual Studio z **nástroje** příkaz **Správce balíčků NuGet**, vyberte **konzoly Správce balíčků**. V okně konzoly Správce balíčků zadejte následující příkaz:

[!code-powershell[Main](scaleout-with-redis/samples/sample5.ps1)]

Dále otevřete soubor Global.asax. Přidejte následující kód, který **aplikace\_Start** metody:

[!code-csharp[Main](scaleout-with-redis/samples/sample6.cs)]

- "server" je název serveru, na kterém běží Redis.
- *port* je číslo portu
- "password" je heslo, které jste definovali v souboru redis.conf.
- "AppName" je jakýkoli řetězec. Funkce SignalR vytvoří kanál pub/sub Redis s tímto názvem.

Příklad:

[!code-csharp[Main](scaleout-with-redis/samples/sample7.cs)]

## <a name="deploy-and-run-the-application"></a>Nasazení a spuštění aplikace

Příprava vašich instancí Windows serveru k nasazení aplikace SignalR.

Přidejte roli služby IIS. Zahrnují funkce "Vývoj aplikací", včetně protokolu WebSocket.

![](scaleout-with-redis/_static/image5.png)

Zahrnují taky službu správy (uvedené v části "Nástroje pro správu").

![](scaleout-with-redis/_static/image6.png)

**Nainstalovat Web Deploy 3.0.** Když spustíte Správce služby IIS, vás vyzve k instalaci webové platformy společnosti Microsoft, nebo se dají [stáhnout intstaller](https://go.microsoft.com/fwlink/?LinkId=255386). Do platformy hledání pro nasazení webu a nainstalovat nasazení webu 3.0

![](scaleout-with-redis/_static/image7.png)

Zkontrolujte, že je spuštěná služby webové správy. Pokud ne, spusťte službu. (Pokud nevidíte v seznamu služeb Windows služby webové správy, ujistěte se, že jste nainstalovali službu pro správu při přidání IIS role.)

Ve výchozím nastavení služby webové správy naslouchá na TCP port 8172. V bráně Windows Firewall vytvořte nové pravidlo, které povoluje provoz TCP na port 8172. Další informace najdete v tématu [konfigurace pravidel brány Firewall](https://technet.microsoft.com/library/dd448559(WS.10).aspx). (Pokud jsou hostiteli virtuálních počítačů v Azure, můžete provést přímo na webu Azure portal. Zobrazit [jak nastavit koncové body k virtuálnímu počítači](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).)

Nyní jste připraveni k nasazení projektu sady Visual Studio z vývojového počítače k serveru. V Průzkumníku řešení klikněte pravým tlačítkem myši na řešení a klikněte na tlačítko **publikovat**.

Podrobnější dokumentaci k nasazení webu, najdete v článku [mapa obsahu webové nasazení pro Visual Studio a ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).

Pokud nasadíte aplikaci do dvou serverů, můžete otevřít každou instanci v samostatném okně prohlížeče a zobrazit, že každá z jiných příjem zpráv SignalR. (Samozřejmě v produkčním prostředí, dva servery strávil za nástrojem pro vyrovnávání zatížení.)

![](scaleout-with-redis/_static/image8.png)

Pokud vás to zajímá, zobrazíte zprávy, které se odesílají do Redis, můžete použít **rozhraní příkazového řádku redis** klienta, který se nainstaluje s využitím Redis.

[!code-xml[Main](scaleout-with-redis/samples/sample8.xml)]

![](scaleout-with-redis/_static/image9.png)
