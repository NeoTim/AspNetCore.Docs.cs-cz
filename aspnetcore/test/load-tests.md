---
title: ASP.NET Core zátěžové nebo zátěžové testování
author: Jeremy-Meng
description: Přečtěte si několik důležitých nástrojů a přístupů pro zátěžové testování a zátěžové testování ASP.NET Core aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 7a9dfc1fedf747ab26daa573b61ed01c31709058
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975249"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core zátěžové nebo zátěžové testování

Zátěžové testování a zátěžové testování jsou důležité k zajištění toho, aby byla webová aplikace výkonná a škálovatelná. Jejich cíle se liší, i když často sdílejí podobné testy.

**Zátěžové testy** &ndash; Otestujte, jestli aplikace může zpracovat zadané zatížení uživatelů pro určitý scénář a přitom stále vyhovuje cíli odpovědi. Aplikace se spouští za běžných podmínek.

**Zátěžové testy** &ndash; Testování stability aplikace při provozu za extrémních podmínek, často po dlouhou dobu. Testy zadávají vysoké uživatelské zatížení, buď špičky nebo postupně zvyšují zatížení, v aplikaci, nebo omezují výpočetní prostředky aplikace.

Zátěžové testy určují, jestli se aplikace v rámci zátěže může zotavit z chyby a řádně se vrátit k očekávanému chování. V případě zátěže není aplikace spouštěna za běžných podmínek.

Visual Studio 2019 je poslední verzí sady Visual Studio s funkcemi zátěžového testu. Pro zákazníky, kteří potřebují nástroje pro testování zatížení v budoucnu, doporučujeme použít alternativní nástroje, jako je Apache JMeter, Akamai CloudTest a BlazeMeter. Další informace naleznete v poznámkách k [verzi sady Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

Služba zátěžové testování ve službě Azure DevOps končí na 2020. Další informace najdete v tématu [cloudová služba zátěžového testování – konec životnosti](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).

## <a name="visual-studio-tools"></a>Nástroje sady Visual Studio

Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit testy výkonu a zátěže webu. Možnost je k dispozici pro vytváření testů pomocí zaznamenávání akcí ve webovém prohlížeči.

Informace o tom, jak vytvořit, nakonfigurovat a spustit projekty zátěžového testu pomocí sady Visual Studio 2017, najdete [v tématu rychlý Start: Vytvořte projekt](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)zátěžového testu.

Zátěžové testy lze nakonfigurovat tak, aby běžely místně nebo běžely v cloudu s využitím Azure DevOps.

## <a name="azure-devops"></a>Azure DevOps

Běh zátěžového testu lze spustit pomocí služby [Azure DevOps test Plans](/azure/devops/test/load-test/index?view=vsts) .

![Cílová stránka pro zátěžové testování Azure DevOps](./load-tests/_static/azure-devops-load-test.png)

Služba podporuje následující formáty testů:

* Webový test &ndash; sady Visual Studio vytvořený v aplikaci Visual Studio.
* Během testování &ndash; se v průběhu testování přehraje archivní přenos protokolu HTTP v archivu.
* [Na základě adresy URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Umožňuje zadat adresy URL pro zátěžový test, typy požadavků, záhlaví a řetězce dotazu. Je možné nakonfigurovat parametry nastavení, jako je například doba trvání, vzorek zatížení a počet uživatelů.
* [Apache JMeter](https://jmeter.apache.org/)

## <a name="azure-portal"></a>portál Azure

[Azure Portal umožňuje nastavit a spustit zátěžové testování webových aplikací](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) přímo z karty **výkon** App Service v Azure Portal.

![Azure App Service v Azure Portal](./load-tests/_static/azure-appservice-perf-test.png)

Test může být manuální test se zadanou adresou URL nebo souborem webového testu sady Visual Studio, který může testovat více adres URL.

![Nová stránka testu výkonu na Azure Portal](./load-tests/_static/azure-appservice-perf-test-config.png)

Na konci testu zobrazuje vygenerované sestavy charakteristiky výkonu aplikace. Příklady statistik zahrnují:

* Průměrná doba odezvy
* Maximální propustnost: počet požadavků za sekundu
* Procento selhání

## <a name="third-party-tools"></a>Nástroje třetích stran

Následující seznam obsahuje nástroje webového výkonu jiných výrobců s různými sadami funkcí:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (AB)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Svatojánského](https://locust.io/)
* [Webwind v západním větru](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)
