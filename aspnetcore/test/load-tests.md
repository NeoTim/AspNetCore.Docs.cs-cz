---
title: ASP.NET Core zatížení a zátěžové testování
author: Jeremy-Meng
description: Popisuje několik významných nástroje a přístupy k testování zatížení a zátěžové testování aplikací pro ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2019
uid: test/loadtests
ms.openlocfilehash: 39632af2c92dac548c03e24d35a5e8a03e00890d
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209830"
---
# <a name="load-and-stress-testing-aspnet-core"></a>Zátěžové testování ASP.NET Core

Zátěžové testování a zátěžové testování je důležité zajistit, že webová aplikace představuje výkonné a škálovatelné. Své cíle se liší i často sdílejí podobné testy.

**Zátěžové testy**: Ověřuje, zda aplikace dokáže zpracovat zadané zátěž uživatelů pro určité scénáře při stále nesplňujete cíl odpovědi. Za normálních podmínek spuštění aplikace.

**Zátěžové testy**: Testy aplikace stability při spuštění v extrémní podmínky a často dlouhou dobu:

* Vysokého uživatelského zatížení – špičky nebo postupně zvyšuje.
* Omezené výpočetních prostředků.

Pod zátěží můžete aplikaci obnovení po selhání a elegantně vrátí k očekávané chování? Pod zátěží, aplikace je *není* spustit za normálních podmínek.

Visual Studio 2019 bude poslední verzí, která obsahuje funkce zátěžového testu. Zákazníkům, kteří vyžadují nástroje pro zátěžové testování, doporučujeme použít alternativní nástroje pro tyto testy, jako jsou Apache JMeter, Akamai CloudTest nebo Blazemeter. Další informace najdete v tématu [Visual Studio. 2019 ve verzi Preview – poznámky k](/visualstudio/releases/2019/release-notes-preview#test-tools).

Zátěžového testování v Azure DevOps skončí platnost během 2020. Další informace najdete v části [cloudového zátěžového testování služby konci životnosti](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).

## <a name="visual-studio-tools"></a>Visual Studio Tools

Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit testy webového výkonu a zatížení. Možnost je k dispozici pro vytvoření testů pomocí zaznamenávání akcí ve webovém prohlížeči.

[Rychlý start: Vytvoření projektu zátěžového testu](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) ukazuje, jak vytvořit, nakonfigurovat a spustit zátěžový test projektů pomocí sady Visual Studio 2017.

Zobrazit [další prostředky](#add) Další informace.

Zátěžové testy lze spustit v místní nebo v cloudu s využitím Azure DevOps.

## <a name="azure-devops"></a>Azure DevOps

Spuštění zátěžového testu můžete začít používat [testovací plány Azure DevOps](/azure/devops/test/load-test/index?view=vsts) služby.

![Azure DevOps zátěžového testování cílová stránka](./load-tests/_static/azure-devops-load-test.png)

Služba podporuje následující typy formát testů:

* Visual Studio test – webového testu vytvořené v sadě Visual Studio.
* Během testování je znovu přehrát testů založené na protokolu HTTP archivu – zachycená data protokolu HTTP v archivu.
* [Na základě adresy URL testu](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – umožňuje zadat adresy URL načíst test, typy požadavků, hlaviček a řetězce dotazu. Vzor zatížení, počet uživatelů atd., můžete nakonfigurovat nastavení parametrů, jako je například doba trvání spuštění.
* [Apache JMeter](https://jmeter.apache.org/) testování.

## <a name="azure-portal"></a>portál Azure

[Azure portal umožňuje nastavení a spuštění zátěžového testování webových aplikací,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) přímo z karty výkonu služby App Service na webu Azure portal.

![Azure App Service na webu Azure Portal](./load-tests/_static/azure-appservice-perf-test.png)

Test může být manuálního testu se zadanou adresu URL nebo soubor webový Test Visual Studio, které můžete testovat více adres URL.

![Nový Test výkonnosti stránky na webu Azure Portal](./load-tests/_static/azure-appservice-perf-test-config.png)

Na konci testu jsou generovány sestavy zobrazíte charakteristiky výkonu aplikace. Příklad statistiky patří:

* Průměrná doba odezvy
* Maximální propustnost: počet požadavků za sekundu
* Procento selhání

## <a name="third-party-tools"></a>Nástroje třetích stran

Následující seznam obsahuje nástroje výkonnosti webu třetích stran s různými sadami funkcí:

* [Apache JMeter](https://jmeter.apache.org/) : Kompletní vybranou sadu nástrojů pro testování zatížení. Vázané na vlákno: potřebujete jedno vlákno na jeden uživatel.
* [AB – Apache HTTP server, nástroj pro srovnávací testy](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/) : Klasické pracovní plochy nástroje s grafickým uživatelským rozhraním a testování zapisovače. Výkonnější než JMeter.
* [Locust.io](https://locust.io/) : Není ohraničené vlákna.

<a name="add"></a>

## <a name="additional-resources"></a>Další prostředky

[Načíst Test blogovou sérii](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) Charles sterling. S datem, ale většina témata jsou stále relevantní.
