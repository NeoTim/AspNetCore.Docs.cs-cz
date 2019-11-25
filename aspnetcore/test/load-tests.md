---
title: ASP.NET Core zátěžové nebo zátěžové testování
author: Jeremy-Meng
description: Přečtěte si několik důležitých nástrojů a přístupů pro zátěžové testování a zátěžové testování ASP.NET Core aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: edaa9e873e8e489f0c560c1736f81358ca1720d0
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289028"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core zátěžové nebo zátěžové testování

Zátěžové testování a zátěžové testování jsou důležité k zajištění toho, aby byla webová aplikace výkonná a škálovatelná. Jejich cíle se liší, i když často sdílejí podobné testy.

**Zátěžové testy** &ndash; otestujete, jestli aplikace dokáže zpracovat zadané zatížení uživatelů pro určitý scénář a přitom stále vyhovuje cíli odpovědi. Aplikace se spouští za běžných podmínek.

**Zátěžové testy** &ndash; stabilitu testovací aplikace při provozu za extrémních podmínek, často po dlouhou dobu. Testy zadávají vysoké uživatelské zatížení, buď špičky nebo postupně zvyšují zatížení, v aplikaci, nebo omezují výpočetní prostředky aplikace.

Zátěžové testy určují, jestli se aplikace v rámci zátěže může zotavit z chyby a řádně se vrátit k očekávanému chování. V případě zátěže není aplikace spouštěna za běžných podmínek.

Visual Studio 2019 je poslední verzí sady Visual Studio s funkcemi zátěžového testu. Pro zákazníky, kteří potřebují nástroje pro testování zatížení v budoucnu, doporučujeme použít alternativní nástroje, jako je Apache JMeter, Akamai CloudTest a BlazeMeter. Další informace naleznete v [poznámkách k verzi sady Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Nástroje sady Visual Studio

Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit testy výkonu a zátěže webu. Možnost je k dispozici pro vytváření testů pomocí zaznamenávání akcí ve webovém prohlížeči.

Informace o tom, jak vytvořit, nakonfigurovat a spustit projekty zátěžového testu pomocí sady Visual Studio 2017, naleznete v tématu [rychlý Start: vytvoření projektu zátěžového testu](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Zátěžové testy lze nakonfigurovat tak, aby běžely místně nebo běžely v cloudu s využitím Azure DevOps.

## <a name="third-party-tools"></a>Nástroje třetích stran

Následující seznam obsahuje nástroje webového výkonu jiných výrobců s různými sadami funkcí:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (AB)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Svatojánského](https://locust.io/)
* [Webwind v západním větru](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)
