---
title: ASP.NET Core zatížení a zátěžové testování
author: Jeremy-Meng
description: Přečtěte si o několika důležité nástroje a přístupy k testování zatížení a zátěžové testování aplikací pro ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: test/loadtests
ms.openlocfilehash: 0a8449ea2c9df0f2ac93058f03af0a1a2aa66508
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64903042"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="35d66-103">ASP.NET Core zatížení a zátěžové testování</span><span class="sxs-lookup"><span data-stu-id="35d66-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="35d66-104">Zátěžové testování a zátěžové testování je důležité zajistit, že webová aplikace představuje výkonné a škálovatelné.</span><span class="sxs-lookup"><span data-stu-id="35d66-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="35d66-105">Své cíle se liší, i když často sdílejí podobné testy.</span><span class="sxs-lookup"><span data-stu-id="35d66-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="35d66-106">**Zátěžové testy** &ndash; otestovat, zda aplikace dokáže zpracovat zadané zátěž uživatelů pro určité scénáře při stále nesplňujete cíl odpovědi.</span><span class="sxs-lookup"><span data-stu-id="35d66-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="35d66-107">Za normálních podmínek spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="35d66-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="35d66-108">**Zátěžové testy** &ndash; testovací stabilitu aplikace při spuštění v rámci extrémní podmínky, často dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="35d66-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="35d66-109">Testy vysokého uživatelského zatížení, provozní špičky nebo postupně zvyšující se zatížení, umístěte na aplikaci nebo omezují výpočetních prostředků aplikace.</span><span class="sxs-lookup"><span data-stu-id="35d66-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="35d66-110">Zátěžové testy určení, zda lze aplikaci vytížený obnovení po selhání a elegantně vrátí k očekávané chování.</span><span class="sxs-lookup"><span data-stu-id="35d66-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="35d66-111">Vytížený není aplikace spuštěna za normálních podmínek.</span><span class="sxs-lookup"><span data-stu-id="35d66-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="35d66-112">Visual Studio 2019 je poslední verzi sady Visual Studio pomocí funkce zátěžového testu.</span><span class="sxs-lookup"><span data-stu-id="35d66-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="35d66-113">Zákazníkům, kteří vyžadují nástroje v budoucnu testování zatížení doporučujeme alternativní nástrojů, jako je Apache JMeter Akamai CloudTest a BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="35d66-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="35d66-114">Další informace najdete v tématu [zpráva k vydání verze. 2019 Visual Studio](/visualstudio/releases/2019/release-notes#test-tools).</span><span class="sxs-lookup"><span data-stu-id="35d66-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes#test-tools).</span></span>

<span data-ttu-id="35d66-115">Zátěžového testování v Azure DevOps skončí platnost během 2020.</span><span class="sxs-lookup"><span data-stu-id="35d66-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="35d66-116">Další informace najdete v tématu [cloudového zátěžového testování služby konci životnosti](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="35d66-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="35d66-117">Nástroje sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35d66-117">Visual Studio tools</span></span>

<span data-ttu-id="35d66-118">Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit testy webového výkonu a zatížení.</span><span class="sxs-lookup"><span data-stu-id="35d66-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="35d66-119">Možnost je k dispozici pro vytvoření testů pomocí zaznamenávání akcí ve webovém prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="35d66-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="35d66-120">Informace o tom, jak vytvořit, nakonfigurovat a spustit zátěžový test projektů pomocí sady Visual Studio 2017 najdete v tématu [rychlý start: Vytvoření projektu zátěžového testu](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="35d66-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span> <span data-ttu-id="35d66-121">Další informace najdete v tématu [další prostředky](#additional-resources) oddílu.</span><span class="sxs-lookup"><span data-stu-id="35d66-121">For more information, see the [Additional resources](#additional-resources) section.</span></span>

<span data-ttu-id="35d66-122">Zátěžové testy lze nakonfigurovat ke spuštění v místním prostředí nebo spuštění v cloudu s využitím Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="35d66-122">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="35d66-123">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="35d66-123">Azure DevOps</span></span>

<span data-ttu-id="35d66-124">Spuštění zátěžového testu můžete začít používat [testovací plány Azure DevOps](/azure/devops/test/load-test/index?view=vsts) služby.</span><span class="sxs-lookup"><span data-stu-id="35d66-124">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Azure DevOps zátěžového testování cílová stránka](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="35d66-126">Tato služba podporuje následující formáty testu:</span><span class="sxs-lookup"><span data-stu-id="35d66-126">The service supports the following test formats:</span></span>

* <span data-ttu-id="35d66-127">Visual Studio &ndash; webového testu vytvořené v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="35d66-127">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="35d66-128">Archiv HTTP &ndash; přenosy HTTP zachycena uvnitř archivu je přehrály během testování.</span><span class="sxs-lookup"><span data-stu-id="35d66-128">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="35d66-129">[Na základě adresy URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; umožňuje zadat adresy URL načíst test, typy požadavků, hlaviček a řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="35d66-129">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="35d66-130">Nastavení parametrů, jako je například doba trvání spuštění vzor zatížení a počet uživatelů, je možné nakonfigurovat.</span><span class="sxs-lookup"><span data-stu-id="35d66-130">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="35d66-131">[Apache JMeter](https://jmeter.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="35d66-131">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="35d66-132">portál Azure</span><span class="sxs-lookup"><span data-stu-id="35d66-132">Azure portal</span></span>

<span data-ttu-id="35d66-133">[Azure portal umožňuje nastavení a spuštění zátěžového testování webových aplikací](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) přímo z **výkonu** kartu služby App Service na webu Azure portal.</span><span class="sxs-lookup"><span data-stu-id="35d66-133">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![Azure App Service na webu Azure portal](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="35d66-135">Test může být manuálního testu se zadanou adresu URL nebo soubor webový Test Visual Studio, které můžete testovat více adres URL.</span><span class="sxs-lookup"><span data-stu-id="35d66-135">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Nový Test výkonnosti stránky na webu Azure portal](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="35d66-137">Na konci testu zobrazit sestavy generované charakteristiky výkonu aplikace.</span><span class="sxs-lookup"><span data-stu-id="35d66-137">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="35d66-138">Příklad statistiky patří:</span><span class="sxs-lookup"><span data-stu-id="35d66-138">Example statistics include:</span></span>

* <span data-ttu-id="35d66-139">Průměrná doba odezvy</span><span class="sxs-lookup"><span data-stu-id="35d66-139">Average response time</span></span>
* <span data-ttu-id="35d66-140">Maximální propustnost: počet požadavků za sekundu</span><span class="sxs-lookup"><span data-stu-id="35d66-140">Max throughput: requests per second</span></span>
* <span data-ttu-id="35d66-141">Procento selhání</span><span class="sxs-lookup"><span data-stu-id="35d66-141">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="35d66-142">Nástroje třetích stran</span><span class="sxs-lookup"><span data-stu-id="35d66-142">Third-party tools</span></span>

<span data-ttu-id="35d66-143">Následující seznam obsahuje nástroje výkonnosti webu třetích stran s různými sadami funkcí:</span><span class="sxs-lookup"><span data-stu-id="35d66-143">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="35d66-144">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="35d66-144">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="35d66-145">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="35d66-145">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="35d66-146">Gatling</span><span class="sxs-lookup"><span data-stu-id="35d66-146">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="35d66-147">Locust</span><span class="sxs-lookup"><span data-stu-id="35d66-147">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="35d66-148">WebSurge větru – západ</span><span class="sxs-lookup"><span data-stu-id="35d66-148">West Wind WebSurge</span></span>](http://websurge.west-wind.com/)
* [<span data-ttu-id="35d66-149">Netling</span><span class="sxs-lookup"><span data-stu-id="35d66-149">Netling</span></span>](https://github.com/hallatore/Netling)

## <a name="additional-resources"></a><span data-ttu-id="35d66-150">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="35d66-150">Additional resources</span></span>

* [<span data-ttu-id="35d66-151">Načíst Test blogovou sérii</span><span class="sxs-lookup"><span data-stu-id="35d66-151">Load Test blog series</span></span>](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
