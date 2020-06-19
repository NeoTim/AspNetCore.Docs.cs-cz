---
title: ASP.NET Core zátěžové nebo zátěžové testování
author: Jeremy-Meng
description: Přečtěte si několik důležitých nástrojů a přístupů pro zátěžové testování a zátěžové testování ASP.NET Core aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: 0ec69ad783a4e545ea95ddcb928d03ba6a2e0050
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074374"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="7eb0f-103">ASP.NET Core zátěžové nebo zátěžové testování</span><span class="sxs-lookup"><span data-stu-id="7eb0f-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="7eb0f-104">Zátěžové testování a zátěžové testování jsou důležité k zajištění toho, aby byla webová aplikace výkonná a škálovatelná.</span><span class="sxs-lookup"><span data-stu-id="7eb0f-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="7eb0f-105">Jejich cíle se liší, i když často sdílejí podobné testy.</span><span class="sxs-lookup"><span data-stu-id="7eb0f-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="7eb0f-106">**Zátěžové testy**: Otestujte, jestli aplikace dokáže zpracovat zadané zatížení uživatelů pro určitý scénář a přitom stále vyhovuje cíli odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7eb0f-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="7eb0f-107">Aplikace se spouští za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="7eb0f-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="7eb0f-108">**Zátěžové testy**: stabilita testovací aplikace při provozu za extrémních podmínek, často po dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="7eb0f-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="7eb0f-109">Testy zadávají vysoké uživatelské zatížení, buď špičky nebo postupně zvyšují zatížení, v aplikaci, nebo omezují výpočetní prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="7eb0f-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="7eb0f-110">Zátěžové testy určují, jestli se aplikace v rámci zátěže může zotavit z chyby a řádně se vrátit k očekávanému chování.</span><span class="sxs-lookup"><span data-stu-id="7eb0f-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="7eb0f-111">V případě zátěže není aplikace spouštěna za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="7eb0f-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="7eb0f-112">V rámci sady Visual Studio 2019 se nahlásily plány pro ukončení [zátěžového testování](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="7eb0f-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="7eb0f-113">Odpovídající služba cloudového zátěžového testování v Azure DevOps se zavřela.</span><span class="sxs-lookup"><span data-stu-id="7eb0f-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="7eb0f-114">Nástroje třetích stran</span><span class="sxs-lookup"><span data-stu-id="7eb0f-114">Third-party tools</span></span>

<span data-ttu-id="7eb0f-115">Následující seznam obsahuje nástroje webového výkonu jiných výrobců s různými sadami funkcí:</span><span class="sxs-lookup"><span data-stu-id="7eb0f-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="7eb0f-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="7eb0f-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="7eb0f-117">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="7eb0f-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="7eb0f-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="7eb0f-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="7eb0f-119">k6</span><span class="sxs-lookup"><span data-stu-id="7eb0f-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="7eb0f-120">Svatojánského</span><span class="sxs-lookup"><span data-stu-id="7eb0f-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="7eb0f-121">Webwind v západním větru</span><span class="sxs-lookup"><span data-stu-id="7eb0f-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="7eb0f-122">Netling</span><span class="sxs-lookup"><span data-stu-id="7eb0f-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="7eb0f-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="7eb0f-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)