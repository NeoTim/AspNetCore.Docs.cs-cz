---
title: ASP.NET Core zatížení a zátěžové testování
author: Jeremy-Meng
description: Popisuje několik významných nástroje a přístupy k testování zatížení a zátěžové testování aplikací pro ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2019
uid: test/loadtests
ms.openlocfilehash: 0a53405cba19435a74b398ba42a05456c50bdc72
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099539"
---
# <a name="load-and-stress-testing-aspnet-core"></a><span data-ttu-id="9e86e-103">Zátěžové testování ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e86e-103">Load and stress testing ASP.NET Core</span></span>

<span data-ttu-id="9e86e-104">Zátěžové testování a zátěžové testování je důležité zajistit, že webová aplikace představuje výkonné a škálovatelné.</span><span class="sxs-lookup"><span data-stu-id="9e86e-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="9e86e-105">Své cíle se liší i často sdílejí podobné testy.</span><span class="sxs-lookup"><span data-stu-id="9e86e-105">Their goals are different even they often share similar tests.</span></span>

<span data-ttu-id="9e86e-106">**Zátěžové testy**: Ověřuje, zda aplikace dokáže zpracovat zadané zátěž uživatelů pro určité scénáře při stále nesplňujete cíl odpovědi.</span><span class="sxs-lookup"><span data-stu-id="9e86e-106">**Load tests**: Tests whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="9e86e-107">Za normálních podmínek spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="9e86e-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="9e86e-108">**Zátěžové testy**: Testy aplikace stability při spuštění v extrémní podmínky a často dlouhou dobu:</span><span class="sxs-lookup"><span data-stu-id="9e86e-108">**Stress tests**: Tests app stability when running under extreme conditions and often a long period of time:</span></span>

* <span data-ttu-id="9e86e-109">Vysokého uživatelského zatížení – špičky nebo postupně zvyšuje.</span><span class="sxs-lookup"><span data-stu-id="9e86e-109">High user load – either spikes or gradually increasing.</span></span>
* <span data-ttu-id="9e86e-110">Omezené výpočetních prostředků.</span><span class="sxs-lookup"><span data-stu-id="9e86e-110">Limited computing resources.</span></span>  

<span data-ttu-id="9e86e-111">Pod zátěží můžete aplikaci obnovení po selhání a elegantně vrátí k očekávané chování?</span><span class="sxs-lookup"><span data-stu-id="9e86e-111">Under stress, can the app recover from failure and gracefully return to expected behavior?</span></span> <span data-ttu-id="9e86e-112">Za normálních podmínek spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="9e86e-112">The app is run under normal conditions.</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="9e86e-113">Nástroje sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e86e-113">Visual Studio Tools</span></span>

<span data-ttu-id="9e86e-114">Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit testy webového výkonu a zatížení.</span><span class="sxs-lookup"><span data-stu-id="9e86e-114">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="9e86e-115">Možnost je k dispozici pro vytvoření testů pomocí zaznamenávání akcí ve webovém prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="9e86e-115">An option is available to create tests by recording actions in web browser.</span></span>

<span data-ttu-id="9e86e-116">[Rychlý start: Vytvoření projektu zátěžového testu](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) ukazuje, jak vytvořit, nakonfigurovat a spustit zátěžový test projektů pomocí sady Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="9e86e-116">[Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) shows how to create, configure, and run a load test projects using Visual Studio 2017.</span></span>

<span data-ttu-id="9e86e-117">Zobrazit [další prostředky](#add) Další informace.</span><span class="sxs-lookup"><span data-stu-id="9e86e-117">See [Additional Resources](#add) for more information.</span></span>

<span data-ttu-id="9e86e-118">Zátěžové testy lze spustit v místní nebo v cloudu s využitím Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="9e86e-118">Load tests can be configured to run in on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="9e86e-119">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="9e86e-119">Azure DevOps</span></span>

<span data-ttu-id="9e86e-120">Spuštění zátěžového testu můžete začít používat [testovací plány Azure DevOps](/azure/devops/test/load-test/index?view=vsts) služby.</span><span class="sxs-lookup"><span data-stu-id="9e86e-120">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="9e86e-121">Služba podporuje následující typy formát testů:</span><span class="sxs-lookup"><span data-stu-id="9e86e-121">The service supports the following types of test format:</span></span>

- <span data-ttu-id="9e86e-122">Visual Studio test – webového testu vytvořené v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9e86e-122">Visual Studio test – web test created in Visual Studio.</span></span>
- <span data-ttu-id="9e86e-123">Během testování je znovu přehrát testů založené na protokolu HTTP archivu – zachycená data protokolu HTTP v archivu.</span><span class="sxs-lookup"><span data-stu-id="9e86e-123">HTTP Archive-based test – captured HTTP traffic inside archive is replayed during testing.</span></span>
- <span data-ttu-id="9e86e-124">[Na základě adresy URL testu](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – umožňuje zadat adresy URL načíst test, typy požadavků, hlaviček a řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="9e86e-124">[URL-based test](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="9e86e-125">Vzor zatížení, počet uživatelů atd., můžete nakonfigurovat nastavení parametrů, jako je například doba trvání spuštění.</span><span class="sxs-lookup"><span data-stu-id="9e86e-125">Run setting parameters such as duration, load pattern, number of users, etc., can be configured.</span></span>
- <span data-ttu-id="9e86e-126">[Apache JMeter](https://jmeter.apache.org/) testování.</span><span class="sxs-lookup"><span data-stu-id="9e86e-126">[Apache JMeter](https://jmeter.apache.org/) test.</span></span>

## <a name="azure-portal"></a><span data-ttu-id="9e86e-127">portál Azure</span><span class="sxs-lookup"><span data-stu-id="9e86e-127">Azure portal</span></span>

<span data-ttu-id="9e86e-128">[Azure portal umožňuje nastavení a spuštění zátěžového testování webových aplikací,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) přímo z karty výkonu služby App Service na webu Azure portal.</span><span class="sxs-lookup"><span data-stu-id="9e86e-128">[Azure portal allows setting up and running load testing of Web Apps,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the Performance tab of the App Service in Azure portal.</span></span>

![](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="9e86e-129">Test může být manuálního testu se zadanou adresu URL nebo soubor webový Test Visual Studio, které můžete testovat více adres URL.</span><span class="sxs-lookup"><span data-stu-id="9e86e-129">The test can be a manual test with a specified URL, or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="9e86e-130">Na konci testu jsou generovány sestavy zobrazíte charakteristiky výkonu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9e86e-130">At end of the test, reports are generated to show the performance characteristics of the app.</span></span> <span data-ttu-id="9e86e-131">Příklad statistiky patří:</span><span class="sxs-lookup"><span data-stu-id="9e86e-131">Example statistics include:</span></span>

- <span data-ttu-id="9e86e-132">Průměrná doba odezvy</span><span class="sxs-lookup"><span data-stu-id="9e86e-132">Average response time</span></span>
- <span data-ttu-id="9e86e-133">Maximální propustnost: počet požadavků za sekundu</span><span class="sxs-lookup"><span data-stu-id="9e86e-133">Max throughput: requests per second</span></span>
- <span data-ttu-id="9e86e-134">Procento selhání</span><span class="sxs-lookup"><span data-stu-id="9e86e-134">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="9e86e-135">Nástroje třetích stran</span><span class="sxs-lookup"><span data-stu-id="9e86e-135">Third-party Tools</span></span>

<span data-ttu-id="9e86e-136">Následující seznam obsahuje nástroje výkonnosti webu třetích stran s různými sadami funkcí:</span><span class="sxs-lookup"><span data-stu-id="9e86e-136">The following list contains third-party web performance tools with various feature sets:</span></span>

- <span data-ttu-id="9e86e-137">[Apache JMeter](https://jmeter.apache.org/) : Kompletní vybranou sadu nástrojů pro testování zatížení.</span><span class="sxs-lookup"><span data-stu-id="9e86e-137">[Apache JMeter](https://jmeter.apache.org/) : Full featured suite of load testing tools.</span></span> <span data-ttu-id="9e86e-138">Vázané na vlákno: potřebujete jedno vlákno na jeden uživatel.</span><span class="sxs-lookup"><span data-stu-id="9e86e-138">Thread-bound: need one thread per user.</span></span>
- [<span data-ttu-id="9e86e-139">AB – Apache HTTP server, nástroj pro srovnávací testy</span><span class="sxs-lookup"><span data-stu-id="9e86e-139">ab - Apache HTTP server benchmarking tool</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
- <span data-ttu-id="9e86e-140">[Gatling](https://gatling.io/) : Klasické pracovní plochy nástroje s grafickým uživatelským rozhraním a testování zapisovače.</span><span class="sxs-lookup"><span data-stu-id="9e86e-140">[Gatling](https://gatling.io/) : Desktop tool with a GUI and test recorders.</span></span> <span data-ttu-id="9e86e-141">Výkonnější než JMeter.</span><span class="sxs-lookup"><span data-stu-id="9e86e-141">More performant than JMeter.</span></span>
- <span data-ttu-id="9e86e-142">[Locust.IO](https://locust.io/) : Není ohraničené vlákna.</span><span class="sxs-lookup"><span data-stu-id="9e86e-142">[Locust.io](https://locust.io/) : Not bounded by threads.</span></span>

<a name="add"></a>
## <a name="additional-resources"></a><span data-ttu-id="9e86e-143">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="9e86e-143">Additional Resources</span></span>

<span data-ttu-id="9e86e-144">[Načíst Test blogovou sérii](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) Charles sterling.</span><span class="sxs-lookup"><span data-stu-id="9e86e-144">[Load Test blog series](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) by Charles Sterling.</span></span> <span data-ttu-id="9e86e-145">S datem, ale většina témata jsou stále relevantní.</span><span class="sxs-lookup"><span data-stu-id="9e86e-145">Dated but most of the topics are still relevant.</span></span>