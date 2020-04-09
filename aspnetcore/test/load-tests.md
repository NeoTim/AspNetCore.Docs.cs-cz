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
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="ab66d-103">ASP.NET zátěžové/zátěžové testování jádra</span><span class="sxs-lookup"><span data-stu-id="ab66d-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="ab66d-104">Zátěžové testování a zátěžové testování jsou důležité pro zajištění výkonu a škálovatelnosti webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="ab66d-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="ab66d-105">Jejich cíle jsou různé, i když často sdílejí podobné testy.</span><span class="sxs-lookup"><span data-stu-id="ab66d-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="ab66d-106">**Zátěžové testy** &ndash; Vyzkoušejte, zda aplikace dokáže zpracovat zadané zatížení uživatelů pro určitý scénář a zároveň splnění cíle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="ab66d-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="ab66d-107">Aplikace je spuštěna za normálních podmínek.</span><span class="sxs-lookup"><span data-stu-id="ab66d-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="ab66d-108">**Zátěžové testy** &ndash; Test app stability při spuštění v extrémních podmínkách, často po dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="ab66d-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="ab66d-109">Testy umístit vysoké zatížení uživatele, buď špičky nebo postupně rostoucí zatížení, na aplikaci, nebo omezují výpočetní prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="ab66d-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="ab66d-110">Zátěžové testy určují, zda aplikace pod napětím může zotavit z selhání a řádně vrátit k očekávané chování.</span><span class="sxs-lookup"><span data-stu-id="ab66d-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="ab66d-111">Ve stresu aplikace není spuštěna za normálních podmínek.</span><span class="sxs-lookup"><span data-stu-id="ab66d-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="ab66d-112">Visual Studio 2019 je poslední verze Visual Studia s funkcemi zátěžového testu.</span><span class="sxs-lookup"><span data-stu-id="ab66d-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="ab66d-113">Pro zákazníky, kteří v budoucnu vyžadují nástroje pro testování zatížení, doporučujeme alternativní nástroje, jako jsou Apache JMeter, Akamai CloudTest a BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="ab66d-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="ab66d-114">Další informace naleznete v [poznámkách k verzi Visual Studia 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="ab66d-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="ab66d-115">Nástroje sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab66d-115">Visual Studio tools</span></span>

<span data-ttu-id="ab66d-116">Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit výkon webu a zátěžové testy.</span><span class="sxs-lookup"><span data-stu-id="ab66d-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="ab66d-117">K dispozici je možnost vytvářet testy záznamem akcí ve webovém prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="ab66d-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="ab66d-118">Informace o tom, jak vytvořit, nakonfigurovat a spustit projekty zátěžového testu pomocí sady Visual Studio 2017, naleznete [v tématu Úvodní příručka: Vytvoření projektu zátěžového testu](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="ab66d-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="ab66d-119">Zátěžové testy lze nakonfigurovat tak, aby se spouštělo místně nebo v cloudu pomocí Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="ab66d-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="ab66d-120">Nástroje třetích stran</span><span class="sxs-lookup"><span data-stu-id="ab66d-120">Third-party tools</span></span>

<span data-ttu-id="ab66d-121">Následující seznam obsahuje nástroje pro výkon webu jiných výrobců s různými sadami funkcí:</span><span class="sxs-lookup"><span data-stu-id="ab66d-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="ab66d-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="ab66d-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="ab66d-123">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="ab66d-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="ab66d-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="ab66d-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="ab66d-125">k6</span><span class="sxs-lookup"><span data-stu-id="ab66d-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="ab66d-126">Locust</span><span class="sxs-lookup"><span data-stu-id="ab66d-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="ab66d-127">Západní vítr WebSurge</span><span class="sxs-lookup"><span data-stu-id="ab66d-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="ab66d-128">Síťovky</span><span class="sxs-lookup"><span data-stu-id="ab66d-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="ab66d-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="ab66d-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

