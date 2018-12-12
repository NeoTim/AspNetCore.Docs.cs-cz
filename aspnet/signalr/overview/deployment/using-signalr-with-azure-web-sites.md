---
uid: signalr/overview/deployment/using-signalr-with-azure-web-sites
title: Použití aplikace SignalR s webovými aplikacemi ve službě Azure App Service | Dokumentace Microsoftu
author: pfletcher
description: Tento dokument popisuje, jak nakonfigurovat aplikaci s knihovnou SignalR, která běží na Microsoft Azure. V tomto kurzu použili verze softwaru, Visual Studio 2013 nebo Vis....
ms.author: riande
ms.date: 07/01/2015
ms.assetid: 2a7517a0-b88c-4162-ade3-9bf6ca7062fd
msc.legacyurl: /signalr/overview/deployment/using-signalr-with-azure-web-sites
msc.type: authoredcontent
ms.openlocfilehash: da69e1aba1b56d69ad8e710cddd2b492168f1255
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287752"
---
<a name="using-signalr-with-web-apps-in-azure-app-service"></a>Použití aplikace SignalR s webovými aplikacemi ve službě Azure App Service
====================
podle [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento dokument popisuje, jak nakonfigurovat aplikaci s knihovnou SignalR, která běží na Microsoft Azure.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013) nebo Visual Studio 2012
> - .NET 4.5
> - Funkce SignalR verze 2
> - Azure SDK 2.3 pro Visual Studio 2013 nebo 2012
>
>
>
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR), [StackOverflow.com](http://stackoverflow.com/), nebo [fóra Microsoft Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?category=windowsazureplatform).


## <a name="table-of-contents"></a>Obsah

- [Úvod](#introduction)
- [Nasazení funkce SignalR webové aplikace do Azure App Service](#deploying)
- [Povolení WebSockets ve službě Azure App Service](#websocket)
- [Pomocí propojovací rozhraní Azure Redis Cache](#backplane)
- [Další kroky](#nextsteps)

<a id="introduction"></a>
## <a name="introduction"></a>Úvod

Funkce SignalR technologie ASP.NET je možné uvést novou úroveň interakce mezi serverem a web nebo klienty .NET. Když jsou hostované v Azure, SignalR můžou aplikace využít vysoce dostupných, škálovatelných a výkonných prostředí, který běží v cloudu poskytuje.

<a id="deploying"></a>
## <a name="deploying-a-signalr-web-app-to-azure-app-service"></a>Nasazení funkce SignalR webové aplikace do Azure App Service

Funkce SignalR nepřidává žádné konkrétní komplikací pro nasazení aplikace do Azure a nasazením na místním serveru. Aplikace, která používá SignalR je možné hostovat v Azure bez jakýchkoli změn v konfiguraci nebo jiná nastavení (i když protokoly Websocket podporu najdete na webu [WebSockets umožňuje ve službě Azure App Service](#websocket) níže.) Pro účely tohoto kurzu budete nasazovat aplikace vytvořená v [kurz Začínáme](../getting-started/tutorial-getting-started-with-signalr.md) do Azure.

**Požadavky**

- Visual Studio 2013. Pokud nemáte Visual Studio, Visual Studio 2013 Express pro Web je součástí instalace sady Azure SDK.
- [Azure SDK 2.3 pro Visual Studio 2013](https://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) nebo [Azure SDK 2.3 pro sadu Visual Studio 2012](https://go.microsoft.com/fwlink/p/?linkid=323511).
- K dokončení tohoto kurzu, budete potřebovat předplatné Azure. Je možné [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), nebo [si zaregistrovat zkušební předplatné](https://azure.microsoft.com/pricing/free-trial/).

### <a name="deploying-a-signalr-web-app-to-azure"></a>Nasazení funkce SignalR webové aplikace do Azure

1. Dokončení [kurz Začínáme](../getting-started/tutorial-getting-started-with-signalr.md), nebo stáhnout dokončený projekt z [Galerie kódu na](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).
2. V sadě Visual Studio, vyberte **sestavení**, **publikovat SignalR Chat**.
3. V dialogovém okně "Publikovat Web" Vyberte "Windows Azure Web Sites".

    ![Vyberte model weby Azure](using-signalr-with-azure-web-sites/_static/image1.png)
4. Pokud nejste přihlášení k účtu Microsoft, klikněte na tlačítko **přihlásit...**  v dialogovém okně "Vyberte existující web" a přihlaste se.

    ![Vyberte existující web](using-signalr-with-azure-web-sites/_static/image2.png)    ![Přihlášení k Azure](using-signalr-with-azure-web-sites/_static/image3.png)
5. V dialogovém okně "Vyberte existující webovou stránku" klikněte na tlačítko **nový**.

    ![Nový web](using-signalr-with-azure-web-sites/_static/image4.png)
6. V dialogovém okně "Při vytváření webu v Windows Azure" Zadejte jedinečný název aplikace. Vyberte oblast co nejblíže k vám v rozevírací nabídce oblasti. Klikněte na možnost **Vytvořit**.

    ![Vytvoření webu v Azure](using-signalr-with-azure-web-sites/_static/image5.png)
7. V dialogovém okně "Publikovat Web" klikněte na tlačítko **publikovat**.

    ![publikování webu](using-signalr-with-azure-web-sites/_static/image6.png)
8. Po dokončení publikování aplikace SignalR chatovací aplikaci hostované v Azure App Service Web Apps se otevře v prohlížeči.

    ![Web se otevře v prohlížeči](using-signalr-with-azure-web-sites/_static/image7.png)

<a id="websocket"></a>
### <a name="enabling-websockets-on-azure-app-service-web-apps"></a>Povolení WebSockets ve službě Azure App Service Web Apps

Objekty Websocket je potřeba explicitně povolit ve webové aplikaci pro použití v aplikace SignalR. v opačném případě se použije jiné protokoly (viz [přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports) podrobnosti).

Chcete-li použít objekty Websocket na Azure App Service Web Apps, povolte v části Konfigurace webové aplikace. Chcete-li to provést, otevřete vaší webové aplikace v [portálu pro správu Azure](https://manage.windowsazure.com/)a vyberte konfigurovat.

![Karta Konfigurace](using-signalr-with-azure-web-sites/_static/image8.png)

V horní části stránky konfigurace zajistěte, aby používal rozhraní .NET 4.5 pro vaši webovou aplikaci.

![Nastavení rozhraní .NET framework verze 4.5](using-signalr-with-azure-web-sites/_static/image9.png)

Na stránce konfigurace v **objekty Websocket** vyberte **na**.

![Protokoly Websocket nastavení: On](using-signalr-with-azure-web-sites/_static/image10.png)

V dolní části stránky konfigurace, vyberte **Uložit** uložte provedené změny.

![Uložit nastavení](using-signalr-with-azure-web-sites/_static/image11.png)

<a id="backplane"></a>
## <a name="using-the-azure-redis-cache-backplane"></a>Pomocí propojovací rozhraní Azure Redis Cache

Pokud používáte více instancí pro vaši webovou aplikaci a uživatelé tyto instance musí komunikovat mezi sebou (tak, aby zprávy chatu vytvořené v jedné instanci může například dosáhnout uživatelé připojení k jiné instance), [Azure Redis Cache Propojovací rozhraní systému](../performance/scaleout-with-redis.md) musí být implementován ve vaší aplikaci.

<a id="nextsteps"></a>
## <a name="next-steps"></a>Další kroky

Další informace o Web Apps ve službě Azure App Service najdete v tématu [přehled Web Apps](https://azure.microsoft.com/documentation/articles/app-service-web-overview/).
