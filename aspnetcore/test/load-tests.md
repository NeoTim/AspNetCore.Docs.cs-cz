---
title: ASP.NET zátěžové/zátěžové testování jádra
author: Jeremy-Meng
description: Seznamte se s několika pozoruhodnými nástroji a přístupy pro zátěžové testování a zátěžové testování ASP.NET základních aplikacích.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664687"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET zátěžové/zátěžové testování jádra

Zátěžové testování a zátěžové testování jsou důležité pro zajištění výkonu a škálovatelnosti webové aplikace. Jejich cíle jsou různé, i když často sdílejí podobné testy.

**Zátěžové testy** &ndash; Vyzkoušejte, zda aplikace dokáže zpracovat zadané zatížení uživatelů pro určitý scénář a zároveň splnění cíle odpovědi. Aplikace je spuštěna za normálních podmínek.

**Zátěžové testy** &ndash; Test app stability při spuštění v extrémních podmínkách, často po dlouhou dobu. Testy umístit vysoké zatížení uživatele, buď špičky nebo postupně rostoucí zatížení, na aplikaci, nebo omezují výpočetní prostředky aplikace.

Zátěžové testy určují, zda aplikace pod napětím může zotavit z selhání a řádně vrátit k očekávané chování. Ve stresu aplikace není spuštěna za normálních podmínek.

Visual Studio 2019 je poslední verze Visual Studia s funkcemi zátěžového testu. Pro zákazníky, kteří v budoucnu vyžadují nástroje pro testování zatížení, doporučujeme alternativní nástroje, jako jsou Apache JMeter, Akamai CloudTest a BlazeMeter. Další informace naleznete v [poznámkách k verzi Visual Studia 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Nástroje sady Visual Studio

Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit výkon webu a zátěžové testy. K dispozici je možnost vytvářet testy záznamem akcí ve webovém prohlížeči.

Informace o tom, jak vytvořit, nakonfigurovat a spustit projekty zátěžového testu pomocí sady Visual Studio 2017, naleznete [v tématu Úvodní příručka: Vytvoření projektu zátěžového testu](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Zátěžové testy lze nakonfigurovat tak, aby se spouštělo místně nebo v cloudu pomocí Azure DevOps.

## <a name="third-party-tools"></a>Nástroje třetích stran

Následující seznam obsahuje nástroje pro výkon webu jiných výrobců s různými sadami funkcí:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [Západní vítr WebSurge](https://websurge.west-wind.com/)
* [Síťovky](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

