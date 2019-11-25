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
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="9df69-103">ASP.NET Core zátěžové nebo zátěžové testování</span><span class="sxs-lookup"><span data-stu-id="9df69-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="9df69-104">Zátěžové testování a zátěžové testování jsou důležité k zajištění toho, aby byla webová aplikace výkonná a škálovatelná.</span><span class="sxs-lookup"><span data-stu-id="9df69-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="9df69-105">Jejich cíle se liší, i když často sdílejí podobné testy.</span><span class="sxs-lookup"><span data-stu-id="9df69-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="9df69-106">**Zátěžové testy** &ndash; otestujete, jestli aplikace dokáže zpracovat zadané zatížení uživatelů pro určitý scénář a přitom stále vyhovuje cíli odpovědi.</span><span class="sxs-lookup"><span data-stu-id="9df69-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="9df69-107">Aplikace se spouští za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="9df69-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="9df69-108">**Zátěžové testy** &ndash; stabilitu testovací aplikace při provozu za extrémních podmínek, často po dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="9df69-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="9df69-109">Testy zadávají vysoké uživatelské zatížení, buď špičky nebo postupně zvyšují zatížení, v aplikaci, nebo omezují výpočetní prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="9df69-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="9df69-110">Zátěžové testy určují, jestli se aplikace v rámci zátěže může zotavit z chyby a řádně se vrátit k očekávanému chování.</span><span class="sxs-lookup"><span data-stu-id="9df69-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="9df69-111">V případě zátěže není aplikace spouštěna za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="9df69-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="9df69-112">Visual Studio 2019 je poslední verzí sady Visual Studio s funkcemi zátěžového testu.</span><span class="sxs-lookup"><span data-stu-id="9df69-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="9df69-113">Pro zákazníky, kteří potřebují nástroje pro testování zatížení v budoucnu, doporučujeme použít alternativní nástroje, jako je Apache JMeter, Akamai CloudTest a BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="9df69-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="9df69-114">Další informace naleznete v [poznámkách k verzi sady Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="9df69-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="9df69-115">Nástroje sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9df69-115">Visual Studio tools</span></span>

<span data-ttu-id="9df69-116">Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit testy výkonu a zátěže webu.</span><span class="sxs-lookup"><span data-stu-id="9df69-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="9df69-117">Možnost je k dispozici pro vytváření testů pomocí zaznamenávání akcí ve webovém prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="9df69-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="9df69-118">Informace o tom, jak vytvořit, nakonfigurovat a spustit projekty zátěžového testu pomocí sady Visual Studio 2017, naleznete v tématu [rychlý Start: vytvoření projektu zátěžového testu](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="9df69-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="9df69-119">Zátěžové testy lze nakonfigurovat tak, aby běžely místně nebo běžely v cloudu s využitím Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="9df69-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="9df69-120">Nástroje třetích stran</span><span class="sxs-lookup"><span data-stu-id="9df69-120">Third-party tools</span></span>

<span data-ttu-id="9df69-121">Následující seznam obsahuje nástroje webového výkonu jiných výrobců s různými sadami funkcí:</span><span class="sxs-lookup"><span data-stu-id="9df69-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="9df69-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="9df69-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="9df69-123">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="9df69-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="9df69-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="9df69-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="9df69-125">Svatojánského</span><span class="sxs-lookup"><span data-stu-id="9df69-125">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="9df69-126">Webwind v západním větru</span><span class="sxs-lookup"><span data-stu-id="9df69-126">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="9df69-127">Netling</span><span class="sxs-lookup"><span data-stu-id="9df69-127">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="9df69-128">Vegeta</span><span class="sxs-lookup"><span data-stu-id="9df69-128">Vegeta</span></span>](https://github.com/tsenart/vegeta)
