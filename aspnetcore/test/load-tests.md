---
title: ASP.NET Core zátěžové nebo zátěžové testování
author: Jeremy-Meng
description: Přečtěte si několik důležitých nástrojů a přístupů pro zátěžové testování a zátěžové testování ASP.NET Core aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: b433c29b0c959925b996142ccfab177c27183cc4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021910"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="c857f-103">ASP.NET Core zátěžové nebo zátěžové testování</span><span class="sxs-lookup"><span data-stu-id="c857f-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="c857f-104">Zátěžové testování a zátěžové testování jsou důležité k zajištění toho, aby byla webová aplikace výkonná a škálovatelná.</span><span class="sxs-lookup"><span data-stu-id="c857f-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="c857f-105">Jejich cíle se liší, i když často sdílejí podobné testy.</span><span class="sxs-lookup"><span data-stu-id="c857f-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="c857f-106">**Zátěžové testy**: Otestujte, jestli aplikace dokáže zpracovat zadané zatížení uživatelů pro určitý scénář a přitom stále vyhovuje cíli odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c857f-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="c857f-107">Aplikace se spouští za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="c857f-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="c857f-108">**Zátěžové testy**: stabilita testovací aplikace při provozu za extrémních podmínek, často po dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="c857f-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="c857f-109">Testy zadávají vysoké uživatelské zatížení, buď špičky nebo postupně zvyšují zatížení, v aplikaci, nebo omezují výpočetní prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="c857f-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="c857f-110">Zátěžové testy určují, jestli se aplikace v rámci zátěže může zotavit z chyby a řádně se vrátit k očekávanému chování.</span><span class="sxs-lookup"><span data-stu-id="c857f-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="c857f-111">V případě zátěže není aplikace spouštěna za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="c857f-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="c857f-112">V rámci sady Visual Studio 2019 se nahlásily plány pro ukončení [zátěžového testování](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="c857f-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="c857f-113">Odpovídající služba cloudového zátěžového testování v Azure DevOps se zavřela.</span><span class="sxs-lookup"><span data-stu-id="c857f-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="c857f-114">Nástroje třetích stran</span><span class="sxs-lookup"><span data-stu-id="c857f-114">Third-party tools</span></span>

<span data-ttu-id="c857f-115">Následující seznam obsahuje nástroje webového výkonu jiných výrobců s různými sadami funkcí:</span><span class="sxs-lookup"><span data-stu-id="c857f-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="c857f-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="c857f-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="c857f-117">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="c857f-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="c857f-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="c857f-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="c857f-119">k6</span><span class="sxs-lookup"><span data-stu-id="c857f-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="c857f-120">Svatojánského</span><span class="sxs-lookup"><span data-stu-id="c857f-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="c857f-121">Webwind v západním větru</span><span class="sxs-lookup"><span data-stu-id="c857f-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="c857f-122">Netling</span><span class="sxs-lookup"><span data-stu-id="c857f-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="c857f-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="c857f-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)