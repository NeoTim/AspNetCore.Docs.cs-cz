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
# <a name="load-and-stress-testing-aspnet-core"></a><span data-ttu-id="52588-103">Zátěžové testování ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52588-103">Load and stress testing ASP.NET Core</span></span>

<span data-ttu-id="52588-104">Zátěžové testování a zátěžové testování je důležité zajistit, že webová aplikace představuje výkonné a škálovatelné.</span><span class="sxs-lookup"><span data-stu-id="52588-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="52588-105">Své cíle se liší i často sdílejí podobné testy.</span><span class="sxs-lookup"><span data-stu-id="52588-105">Their goals are different even they often share similar tests.</span></span>

<span data-ttu-id="52588-106">**Zátěžové testy**: Ověřuje, zda aplikace dokáže zpracovat zadané zátěž uživatelů pro určité scénáře při stále nesplňujete cíl odpovědi.</span><span class="sxs-lookup"><span data-stu-id="52588-106">**Load tests**: Tests whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="52588-107">Za normálních podmínek spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="52588-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="52588-108">**Zátěžové testy**: Testy aplikace stability při spuštění v extrémní podmínky a často dlouhou dobu:</span><span class="sxs-lookup"><span data-stu-id="52588-108">**Stress tests**: Tests app stability when running under extreme conditions and often a long period of time:</span></span>

* <span data-ttu-id="52588-109">Vysokého uživatelského zatížení – špičky nebo postupně zvyšuje.</span><span class="sxs-lookup"><span data-stu-id="52588-109">High user load – either spikes or gradually increasing.</span></span>
* <span data-ttu-id="52588-110">Omezené výpočetních prostředků.</span><span class="sxs-lookup"><span data-stu-id="52588-110">Limited computing resources.</span></span>

<span data-ttu-id="52588-111">Pod zátěží můžete aplikaci obnovení po selhání a elegantně vrátí k očekávané chování?</span><span class="sxs-lookup"><span data-stu-id="52588-111">Under stress, can the app recover from failure and gracefully return to expected behavior?</span></span> <span data-ttu-id="52588-112">Pod zátěží, aplikace je *není* spustit za normálních podmínek.</span><span class="sxs-lookup"><span data-stu-id="52588-112">Under stress, the app is *not* run under normal conditions.</span></span>

<span data-ttu-id="52588-113">Visual Studio 2019 bude poslední verzí, která obsahuje funkce zátěžového testu.</span><span class="sxs-lookup"><span data-stu-id="52588-113">Visual Studio 2019 will be the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="52588-114">Zákazníkům, kteří vyžadují nástroje pro zátěžové testování, doporučujeme použít alternativní nástroje pro tyto testy, jako jsou Apache JMeter, Akamai CloudTest nebo Blazemeter.</span><span class="sxs-lookup"><span data-stu-id="52588-114">For customers requiring load testing tools, we recommend using alternate load testing tools such as Apache JMeter, Akamai CloudTest, Blazemeter.</span></span> <span data-ttu-id="52588-115">Další informace najdete v tématu [Visual Studio. 2019 ve verzi Preview – poznámky k](/visualstudio/releases/2019/release-notes-preview#test-tools).</span><span class="sxs-lookup"><span data-stu-id="52588-115">For more information, see the [Visual Studio 2019 Preview Release Notes](/visualstudio/releases/2019/release-notes-preview#test-tools).</span></span>

<span data-ttu-id="52588-116">Zátěžového testování v Azure DevOps skončí platnost během 2020.</span><span class="sxs-lookup"><span data-stu-id="52588-116">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="52588-117">Další informace najdete v části [cloudového zátěžového testování služby konci životnosti](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="52588-117">For more information see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="52588-118">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="52588-118">Visual Studio Tools</span></span>

<span data-ttu-id="52588-119">Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit testy webového výkonu a zatížení.</span><span class="sxs-lookup"><span data-stu-id="52588-119">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="52588-120">Možnost je k dispozici pro vytvoření testů pomocí zaznamenávání akcí ve webovém prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="52588-120">An option is available to create tests by recording actions in web browser.</span></span>

<span data-ttu-id="52588-121">[Rychlý start: Vytvoření projektu zátěžového testu](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) ukazuje, jak vytvořit, nakonfigurovat a spustit zátěžový test projektů pomocí sady Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="52588-121">[Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) shows how to create, configure, and run a load test projects using Visual Studio 2017.</span></span>

<span data-ttu-id="52588-122">Zobrazit [další prostředky](#add) Další informace.</span><span class="sxs-lookup"><span data-stu-id="52588-122">See [Additional Resources](#add) for more information.</span></span>

<span data-ttu-id="52588-123">Zátěžové testy lze spustit v místní nebo v cloudu s využitím Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="52588-123">Load tests can be configured to run in on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="52588-124">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="52588-124">Azure DevOps</span></span>

<span data-ttu-id="52588-125">Spuštění zátěžového testu můžete začít používat [testovací plány Azure DevOps](/azure/devops/test/load-test/index?view=vsts) služby.</span><span class="sxs-lookup"><span data-stu-id="52588-125">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Azure DevOps zátěžového testování cílová stránka](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="52588-127">Služba podporuje následující typy formát testů:</span><span class="sxs-lookup"><span data-stu-id="52588-127">The service supports the following types of test format:</span></span>

* <span data-ttu-id="52588-128">Visual Studio test – webového testu vytvořené v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="52588-128">Visual Studio test – web test created in Visual Studio.</span></span>
* <span data-ttu-id="52588-129">Během testování je znovu přehrát testů založené na protokolu HTTP archivu – zachycená data protokolu HTTP v archivu.</span><span class="sxs-lookup"><span data-stu-id="52588-129">HTTP Archive-based test – captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="52588-130">[Na základě adresy URL testu](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – umožňuje zadat adresy URL načíst test, typy požadavků, hlaviček a řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="52588-130">[URL-based test](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="52588-131">Vzor zatížení, počet uživatelů atd., můžete nakonfigurovat nastavení parametrů, jako je například doba trvání spuštění.</span><span class="sxs-lookup"><span data-stu-id="52588-131">Run setting parameters such as duration, load pattern, number of users, etc., can be configured.</span></span>
* <span data-ttu-id="52588-132">[Apache JMeter](https://jmeter.apache.org/) testování.</span><span class="sxs-lookup"><span data-stu-id="52588-132">[Apache JMeter](https://jmeter.apache.org/) test.</span></span>

## <a name="azure-portal"></a><span data-ttu-id="52588-133">portál Azure</span><span class="sxs-lookup"><span data-stu-id="52588-133">Azure portal</span></span>

<span data-ttu-id="52588-134">[Azure portal umožňuje nastavení a spuštění zátěžového testování webových aplikací,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) přímo z karty výkonu služby App Service na webu Azure portal.</span><span class="sxs-lookup"><span data-stu-id="52588-134">[Azure portal allows setting up and running load testing of Web Apps,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the Performance tab of the App Service in Azure portal.</span></span>

![Azure App Service na webu Azure Portal](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="52588-136">Test může být manuálního testu se zadanou adresu URL nebo soubor webový Test Visual Studio, které můžete testovat více adres URL.</span><span class="sxs-lookup"><span data-stu-id="52588-136">The test can be a manual test with a specified URL, or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Nový Test výkonnosti stránky na webu Azure Portal](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="52588-138">Na konci testu jsou generovány sestavy zobrazíte charakteristiky výkonu aplikace.</span><span class="sxs-lookup"><span data-stu-id="52588-138">At end of the test, reports are generated to show the performance characteristics of the app.</span></span> <span data-ttu-id="52588-139">Příklad statistiky patří:</span><span class="sxs-lookup"><span data-stu-id="52588-139">Example statistics include:</span></span>

* <span data-ttu-id="52588-140">Průměrná doba odezvy</span><span class="sxs-lookup"><span data-stu-id="52588-140">Average response time</span></span>
* <span data-ttu-id="52588-141">Maximální propustnost: počet požadavků za sekundu</span><span class="sxs-lookup"><span data-stu-id="52588-141">Max throughput: requests per second</span></span>
* <span data-ttu-id="52588-142">Procento selhání</span><span class="sxs-lookup"><span data-stu-id="52588-142">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="52588-143">Nástroje třetích stran</span><span class="sxs-lookup"><span data-stu-id="52588-143">Third-party Tools</span></span>

<span data-ttu-id="52588-144">Následující seznam obsahuje nástroje výkonnosti webu třetích stran s různými sadami funkcí:</span><span class="sxs-lookup"><span data-stu-id="52588-144">The following list contains third-party web performance tools with various feature sets:</span></span>

* <span data-ttu-id="52588-145">[Apache JMeter](https://jmeter.apache.org/) : Kompletní vybranou sadu nástrojů pro testování zatížení.</span><span class="sxs-lookup"><span data-stu-id="52588-145">[Apache JMeter](https://jmeter.apache.org/) : Full featured suite of load testing tools.</span></span> <span data-ttu-id="52588-146">Vázané na vlákno: potřebujete jedno vlákno na jeden uživatel.</span><span class="sxs-lookup"><span data-stu-id="52588-146">Thread-bound: need one thread per user.</span></span>
* [<span data-ttu-id="52588-147">AB – Apache HTTP server, nástroj pro srovnávací testy</span><span class="sxs-lookup"><span data-stu-id="52588-147">ab - Apache HTTP server benchmarking tool</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* <span data-ttu-id="52588-148">[Gatling](https://gatling.io/) : Klasické pracovní plochy nástroje s grafickým uživatelským rozhraním a testování zapisovače.</span><span class="sxs-lookup"><span data-stu-id="52588-148">[Gatling](https://gatling.io/) : Desktop tool with a GUI and test recorders.</span></span> <span data-ttu-id="52588-149">Výkonnější než JMeter.</span><span class="sxs-lookup"><span data-stu-id="52588-149">More performant than JMeter.</span></span>
* <span data-ttu-id="52588-150">[Locust.io](https://locust.io/) : Není ohraničené vlákna.</span><span class="sxs-lookup"><span data-stu-id="52588-150">[Locust.io](https://locust.io/) : Not bounded by threads.</span></span>

<a name="add"></a>

## <a name="additional-resources"></a><span data-ttu-id="52588-151">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="52588-151">Additional Resources</span></span>

<span data-ttu-id="52588-152">[Načíst Test blogovou sérii](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) Charles sterling.</span><span class="sxs-lookup"><span data-stu-id="52588-152">[Load Test blog series](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) by Charles Sterling.</span></span> <span data-ttu-id="52588-153">S datem, ale většina témata jsou stále relevantní.</span><span class="sxs-lookup"><span data-stu-id="52588-153">Dated but most of the topics are still relevant.</span></span>
