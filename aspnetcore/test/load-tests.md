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
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="f866f-103">ASP.NET Core zátěžové nebo zátěžové testování</span><span class="sxs-lookup"><span data-stu-id="f866f-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="f866f-104">Zátěžové testování a zátěžové testování jsou důležité k zajištění toho, aby byla webová aplikace výkonná a škálovatelná.</span><span class="sxs-lookup"><span data-stu-id="f866f-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="f866f-105">Jejich cíle se liší, i když často sdílejí podobné testy.</span><span class="sxs-lookup"><span data-stu-id="f866f-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="f866f-106">**Zátěžové testy** &ndash; Otestujte, jestli aplikace může zpracovat zadané zatížení uživatelů pro určitý scénář a přitom stále vyhovuje cíli odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f866f-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="f866f-107">Aplikace se spouští za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="f866f-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="f866f-108">**Zátěžové testy** &ndash; Testování stability aplikace při provozu za extrémních podmínek, často po dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="f866f-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="f866f-109">Testy zadávají vysoké uživatelské zatížení, buď špičky nebo postupně zvyšují zatížení, v aplikaci, nebo omezují výpočetní prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="f866f-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="f866f-110">Zátěžové testy určují, jestli se aplikace v rámci zátěže může zotavit z chyby a řádně se vrátit k očekávanému chování.</span><span class="sxs-lookup"><span data-stu-id="f866f-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="f866f-111">V případě zátěže není aplikace spouštěna za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="f866f-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="f866f-112">Visual Studio 2019 je poslední verzí sady Visual Studio s funkcemi zátěžového testu.</span><span class="sxs-lookup"><span data-stu-id="f866f-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="f866f-113">Pro zákazníky, kteří potřebují nástroje pro testování zatížení v budoucnu, doporučujeme použít alternativní nástroje, jako je Apache JMeter, Akamai CloudTest a BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="f866f-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="f866f-114">Další informace naleznete v poznámkách k [verzi sady Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="f866f-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

<span data-ttu-id="f866f-115">Služba zátěžové testování ve službě Azure DevOps končí na 2020.</span><span class="sxs-lookup"><span data-stu-id="f866f-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="f866f-116">Další informace najdete v tématu [cloudová služba zátěžového testování – konec životnosti](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="f866f-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="f866f-117">Nástroje sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f866f-117">Visual Studio tools</span></span>

<span data-ttu-id="f866f-118">Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit testy výkonu a zátěže webu.</span><span class="sxs-lookup"><span data-stu-id="f866f-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="f866f-119">Možnost je k dispozici pro vytváření testů pomocí zaznamenávání akcí ve webovém prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="f866f-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="f866f-120">Informace o tom, jak vytvořit, nakonfigurovat a spustit projekty zátěžového testu pomocí sady Visual Studio 2017, najdete [v tématu rychlý Start: Vytvořte projekt](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)zátěžového testu.</span><span class="sxs-lookup"><span data-stu-id="f866f-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="f866f-121">Zátěžové testy lze nakonfigurovat tak, aby běžely místně nebo běžely v cloudu s využitím Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="f866f-121">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="f866f-122">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="f866f-122">Azure DevOps</span></span>

<span data-ttu-id="f866f-123">Běh zátěžového testu lze spustit pomocí služby [Azure DevOps test Plans](/azure/devops/test/load-test/index?view=vsts) .</span><span class="sxs-lookup"><span data-stu-id="f866f-123">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Cílová stránka pro zátěžové testování Azure DevOps](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="f866f-125">Služba podporuje následující formáty testů:</span><span class="sxs-lookup"><span data-stu-id="f866f-125">The service supports the following test formats:</span></span>

* <span data-ttu-id="f866f-126">Webový test &ndash; sady Visual Studio vytvořený v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f866f-126">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="f866f-127">Během testování &ndash; se v průběhu testování přehraje archivní přenos protokolu HTTP v archivu.</span><span class="sxs-lookup"><span data-stu-id="f866f-127">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="f866f-128">[Na základě adresy URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Umožňuje zadat adresy URL pro zátěžový test, typy požadavků, záhlaví a řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f866f-128">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="f866f-129">Je možné nakonfigurovat parametry nastavení, jako je například doba trvání, vzorek zatížení a počet uživatelů.</span><span class="sxs-lookup"><span data-stu-id="f866f-129">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="f866f-130">[Apache JMeter](https://jmeter.apache.org/)</span><span class="sxs-lookup"><span data-stu-id="f866f-130">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="f866f-131">portál Azure</span><span class="sxs-lookup"><span data-stu-id="f866f-131">Azure portal</span></span>

<span data-ttu-id="f866f-132">[Azure Portal umožňuje nastavit a spustit zátěžové testování webových aplikací](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) přímo z karty **výkon** App Service v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="f866f-132">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![Azure App Service v Azure Portal](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="f866f-134">Test může být manuální test se zadanou adresou URL nebo souborem webového testu sady Visual Studio, který může testovat více adres URL.</span><span class="sxs-lookup"><span data-stu-id="f866f-134">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Nová stránka testu výkonu na Azure Portal](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="f866f-136">Na konci testu zobrazuje vygenerované sestavy charakteristiky výkonu aplikace.</span><span class="sxs-lookup"><span data-stu-id="f866f-136">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="f866f-137">Příklady statistik zahrnují:</span><span class="sxs-lookup"><span data-stu-id="f866f-137">Example statistics include:</span></span>

* <span data-ttu-id="f866f-138">Průměrná doba odezvy</span><span class="sxs-lookup"><span data-stu-id="f866f-138">Average response time</span></span>
* <span data-ttu-id="f866f-139">Maximální propustnost: počet požadavků za sekundu</span><span class="sxs-lookup"><span data-stu-id="f866f-139">Max throughput: requests per second</span></span>
* <span data-ttu-id="f866f-140">Procento selhání</span><span class="sxs-lookup"><span data-stu-id="f866f-140">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="f866f-141">Nástroje třetích stran</span><span class="sxs-lookup"><span data-stu-id="f866f-141">Third-party tools</span></span>

<span data-ttu-id="f866f-142">Následující seznam obsahuje nástroje webového výkonu jiných výrobců s různými sadami funkcí:</span><span class="sxs-lookup"><span data-stu-id="f866f-142">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="f866f-143">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="f866f-143">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="f866f-144">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="f866f-144">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="f866f-145">Gatling</span><span class="sxs-lookup"><span data-stu-id="f866f-145">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="f866f-146">Svatojánského</span><span class="sxs-lookup"><span data-stu-id="f866f-146">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="f866f-147">Webwind v západním větru</span><span class="sxs-lookup"><span data-stu-id="f866f-147">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="f866f-148">Netling</span><span class="sxs-lookup"><span data-stu-id="f866f-148">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="f866f-149">Vegeta</span><span class="sxs-lookup"><span data-stu-id="f866f-149">Vegeta</span></span>](https://github.com/tsenart/vegeta)
