---
title: ASP.NET Core zatížení a zátěžové testování
author: Jeremy-Meng
description: Přečtěte si o několika důležité nástroje a přístupy k testování zatížení a zátěžové testování aplikací pro ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: test/loadtests
ms.openlocfilehash: 4b07dd1af7e0c1d3ce9baa167b69fd8f80df204a
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815176"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core zatížení a zátěžové testování

Zátěžové testování a zátěžové testování je důležité zajistit, že webová aplikace představuje výkonné a škálovatelné. Své cíle se liší, i když často sdílejí podobné testy.

**Zátěžové testy** &ndash; otestovat, zda aplikace dokáže zpracovat zadané zátěž uživatelů pro určité scénáře při stále nesplňujete cíl odpovědi. Za normálních podmínek spuštění aplikace.

**Zátěžové testy** &ndash; testovací stabilitu aplikace při spuštění v rámci extrémní podmínky, často dlouhou dobu. Testy vysokého uživatelského zatížení, provozní špičky nebo postupně zvyšující se zatížení, umístěte na aplikaci nebo omezují výpočetních prostředků aplikace.

Zátěžové testy určení, zda lze aplikaci vytížený obnovení po selhání a elegantně vrátí k očekávané chování. Vytížený není aplikace spuštěna za normálních podmínek.

Visual Studio 2019 je poslední verzi sady Visual Studio pomocí funkce zátěžového testu. Zákazníkům, kteří vyžadují nástroje v budoucnu testování zatížení doporučujeme alternativní nástrojů, jako je Apache JMeter Akamai CloudTest a BlazeMeter. Další informace najdete v tématu [zpráva k vydání verze. 2019 Visual Studio](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

Zátěžového testování v Azure DevOps skončí platnost během 2020. Další informace najdete v tématu [cloudového zátěžového testování služby konci životnosti](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).

## <a name="visual-studio-tools"></a>Nástroje sady Visual Studio

Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit testy webového výkonu a zatížení. Možnost je k dispozici pro vytvoření testů pomocí zaznamenávání akcí ve webovém prohlížeči.

Informace o tom, jak vytvořit, nakonfigurovat a spustit zátěžový test projektů pomocí sady Visual Studio 2017 najdete v tématu [rychlý start: Vytvoření projektu zátěžového testu](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017). Další informace najdete v tématu [další prostředky](#additional-resources) oddílu.

Zátěžové testy lze nakonfigurovat ke spuštění v místním prostředí nebo spuštění v cloudu s využitím Azure DevOps.

## <a name="azure-devops"></a>Azure DevOps

Spuštění zátěžového testu můžete začít používat [testovací plány Azure DevOps](/azure/devops/test/load-test/index?view=vsts) služby.

![Azure DevOps zátěžového testování cílová stránka](./load-tests/_static/azure-devops-load-test.png)

Tato služba podporuje následující formáty testu:

* Visual Studio &ndash; webového testu vytvořené v sadě Visual Studio.
* Archiv HTTP &ndash; přenosy HTTP zachycena uvnitř archivu je přehrály během testování.
* [Na základě adresy URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; umožňuje zadat adresy URL načíst test, typy požadavků, hlaviček a řetězce dotazu. Nastavení parametrů, jako je například doba trvání spuštění vzor zatížení a počet uživatelů, je možné nakonfigurovat.
* [Apache JMeter](https://jmeter.apache.org/).

## <a name="azure-portal"></a>portál Azure

[Azure portal umožňuje nastavení a spuštění zátěžového testování webových aplikací](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) přímo z **výkonu** kartu služby App Service na webu Azure portal.

![Azure App Service na webu Azure portal](./load-tests/_static/azure-appservice-perf-test.png)

Test může být manuálního testu se zadanou adresu URL nebo soubor webový Test Visual Studio, které můžete testovat více adres URL.

![Nový Test výkonnosti stránky na webu Azure portal](./load-tests/_static/azure-appservice-perf-test-config.png)

Na konci testu zobrazit sestavy generované charakteristiky výkonu aplikace. Příklad statistiky patří:

* Průměrná doba odezvy
* Maximální propustnost: počet požadavků za sekundu
* Procento selhání

## <a name="third-party-tools"></a>Nástroje třetích stran

Následující seznam obsahuje nástroje výkonnosti webu třetích stran s různými sadami funkcí:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Locust](https://locust.io/)
* [WebSurge větru – západ](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

## <a name="additional-resources"></a>Další zdroje

* [Načíst Test blogovou sérii](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
