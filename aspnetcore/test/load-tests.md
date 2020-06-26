---
title: ASP.NET Core zátěžové nebo zátěžové testování
author: Jeremy-Meng
description: Přečtěte si několik důležitých nástrojů a přístupů pro zátěžové testování a zátěžové testování ASP.NET Core aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: 5df2dd906d52aaec4fc13b07f3d92c87c802f37f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406508"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="33f78-103">ASP.NET Core zátěžové nebo zátěžové testování</span><span class="sxs-lookup"><span data-stu-id="33f78-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="33f78-104">Zátěžové testování a zátěžové testování jsou důležité k zajištění toho, aby byla webová aplikace výkonná a škálovatelná.</span><span class="sxs-lookup"><span data-stu-id="33f78-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="33f78-105">Jejich cíle se liší, i když často sdílejí podobné testy.</span><span class="sxs-lookup"><span data-stu-id="33f78-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="33f78-106">**Zátěžové testy**: Otestujte, jestli aplikace dokáže zpracovat zadané zatížení uživatelů pro určitý scénář a přitom stále vyhovuje cíli odpovědi.</span><span class="sxs-lookup"><span data-stu-id="33f78-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="33f78-107">Aplikace se spouští za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="33f78-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="33f78-108">**Zátěžové testy**: stabilita testovací aplikace při provozu za extrémních podmínek, často po dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="33f78-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="33f78-109">Testy zadávají vysoké uživatelské zatížení, buď špičky nebo postupně zvyšují zatížení, v aplikaci, nebo omezují výpočetní prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="33f78-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="33f78-110">Zátěžové testy určují, jestli se aplikace v rámci zátěže může zotavit z chyby a řádně se vrátit k očekávanému chování.</span><span class="sxs-lookup"><span data-stu-id="33f78-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="33f78-111">V případě zátěže není aplikace spouštěna za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="33f78-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="33f78-112">V rámci sady Visual Studio 2019 se nahlásily plány pro ukončení [zátěžového testování](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="33f78-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="33f78-113">Odpovídající služba cloudového zátěžového testování v Azure DevOps se zavřela.</span><span class="sxs-lookup"><span data-stu-id="33f78-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="33f78-114">Nástroje třetích stran</span><span class="sxs-lookup"><span data-stu-id="33f78-114">Third-party tools</span></span>

<span data-ttu-id="33f78-115">Následující seznam obsahuje nástroje webového výkonu jiných výrobců s různými sadami funkcí:</span><span class="sxs-lookup"><span data-stu-id="33f78-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="33f78-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="33f78-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="33f78-117">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="33f78-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="33f78-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="33f78-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="33f78-119">k6</span><span class="sxs-lookup"><span data-stu-id="33f78-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="33f78-120">Svatojánského</span><span class="sxs-lookup"><span data-stu-id="33f78-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="33f78-121">Webwind v západním větru</span><span class="sxs-lookup"><span data-stu-id="33f78-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="33f78-122">Netling</span><span class="sxs-lookup"><span data-stu-id="33f78-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="33f78-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="33f78-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)