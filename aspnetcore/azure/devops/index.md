---
title: DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Příručka, která poskytuje kompletní pokyny k vytvoření DevOps kanálu pro ASP.NET Core aplikaci hostovanou v Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: devx-track-csharp, mvc, seodec18
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/index
ms.openlocfilehash: af8e130fe63a266eabacd70a9c84aac56ac1c4c0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625359"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="623a4-103">DevOps s využitím ASP.NET Core a Azure</span><span class="sxs-lookup"><span data-stu-id="623a4-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="623a4-104">[![Titulní obrázek](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="623a4-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="623a4-105">Pomocí [Vačk Soper](https://twitter.com/camsoper) a [Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="623a4-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="623a4-106">Tato příručka je k dispozici jako [ke stažení e-knihy ve formátu PDF](https://aka.ms/devopsbook).</span><span class="sxs-lookup"><span data-stu-id="623a4-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="623a4-107">Vítáme vás</span><span class="sxs-lookup"><span data-stu-id="623a4-107">Welcome</span></span> 

<span data-ttu-id="623a4-108">Vítá vás Průvodce životním cyklem vývoje Azure pro .NET!</span><span class="sxs-lookup"><span data-stu-id="623a4-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="623a4-109">Tato příručka zavádí základní koncepty vytváření životního cyklu vývoje kolem Azure pomocí nástrojů a procesů .NET.</span><span class="sxs-lookup"><span data-stu-id="623a4-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="623a4-110">Po dokončení tohoto průvodce budete těžit výhody DevOpse sada nástrojůu pro dospělé.</span><span class="sxs-lookup"><span data-stu-id="623a4-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="623a4-111">Koho je tato příručka určena pro</span><span class="sxs-lookup"><span data-stu-id="623a4-111">Who this guide is for</span></span>

<span data-ttu-id="623a4-112">Měli byste být zkušeným ASP.NET Core vývojářem (úroveň 200-300).</span><span class="sxs-lookup"><span data-stu-id="623a4-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="623a4-113">Nemusíte nic dělat na Azure, protože v tomto úvodu pokryjeme.</span><span class="sxs-lookup"><span data-stu-id="623a4-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="623a4-114">Tato příručka může být užitečná také pro inženýry DevOps, kteří se podrobněji zaměřují na operace než vývoj.</span><span class="sxs-lookup"><span data-stu-id="623a4-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="623a4-115">Tato příručka cílí na vývojáře v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="623a4-115">This guide targets Windows developers.</span></span> <span data-ttu-id="623a4-116">Systémy Linux a macOS jsou však plně podporovány rozhraním .NET Core.</span><span class="sxs-lookup"><span data-stu-id="623a4-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="623a4-117">Pokud chcete tento průvodce upravit pro Linux/macOS, podívejte se na popisky pro rozdíly v systému Linux/macOS.</span><span class="sxs-lookup"><span data-stu-id="623a4-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="623a4-118">Co tato příručka nepokrývá</span><span class="sxs-lookup"><span data-stu-id="623a4-118">What this guide doesn't cover</span></span>

<span data-ttu-id="623a4-119">Tato příručka se zaměřuje na ucelené prostředí průběžného nasazování pro vývojáře na platformě .NET.</span><span class="sxs-lookup"><span data-stu-id="623a4-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="623a4-120">Není vyčerpávajícím průvodcem pro všechny věci Azure a nezaměřuje se na to rozsáhle rozhraní API .NET pro služby Azure.</span><span class="sxs-lookup"><span data-stu-id="623a4-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="623a4-121">Důraz je vše kolem nepřetržité integrace, nasazení, monitorování a ladění.</span><span class="sxs-lookup"><span data-stu-id="623a4-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="623a4-122">Na konci příručky jsou k dispozici doporučení pro další kroky.</span><span class="sxs-lookup"><span data-stu-id="623a4-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="623a4-123">Součástí návrhů jsou služby platformy Azure, které jsou užitečné pro ASP.NET Core vývojářům.</span><span class="sxs-lookup"><span data-stu-id="623a4-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="623a4-124">Co je v této příručce</span><span class="sxs-lookup"><span data-stu-id="623a4-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="623a4-125">Nástroje a soubory ke stažení</span><span class="sxs-lookup"><span data-stu-id="623a4-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="623a4-126">Zjistěte, kde získat nástroje používané v této příručce.</span><span class="sxs-lookup"><span data-stu-id="623a4-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="623a4-127">Nasazení do App Service</span><span class="sxs-lookup"><span data-stu-id="623a4-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="623a4-128">Seznamte se s různými způsoby nasazení ASP.NET Core aplikace do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="623a4-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="623a4-129">Průběžná integrace a nasazování</span><span class="sxs-lookup"><span data-stu-id="623a4-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="623a4-130">Pomocí GitHubu, Azure DevOps Services a Azure můžete vytvořit komplexní řešení pro průběžnou integraci a nasazení pro vaši aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="623a4-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="623a4-131">Monitorování a ladění</span><span class="sxs-lookup"><span data-stu-id="623a4-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="623a4-132">Pomocí nástrojů Azure můžete monitorovat, řešit problémy a ladit svou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="623a4-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="623a4-133">Další kroky</span><span class="sxs-lookup"><span data-stu-id="623a4-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="623a4-134">Další studijní materiály pro Azure ASP.NET Core Learning pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="623a4-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="623a4-135">Dodatečné úvodní čtení</span><span class="sxs-lookup"><span data-stu-id="623a4-135">Additional introductory reading</span></span>

<span data-ttu-id="623a4-136">Pokud je to vaše první riziko pro cloud computing, tyto články vysvětlují základy.</span><span class="sxs-lookup"><span data-stu-id="623a4-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="623a4-137">Co je cloud computing?</span><span class="sxs-lookup"><span data-stu-id="623a4-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="623a4-138">Příklady cloud computingu</span><span class="sxs-lookup"><span data-stu-id="623a4-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="623a4-139">Co je IaaS?</span><span class="sxs-lookup"><span data-stu-id="623a4-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="623a4-140">Co je PaaS?</span><span class="sxs-lookup"><span data-stu-id="623a4-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
