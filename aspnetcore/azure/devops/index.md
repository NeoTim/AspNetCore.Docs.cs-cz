---
title: DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Příručka, která poskytuje kompletní pokyny k vytvoření DevOps kanálu pro ASP.NET Core aplikaci hostovanou v Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: 10df1eab6de064b824ce067aff3ecc07e81c38fd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012264"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="ae7d3-103">DevOps s využitím ASP.NET Core a Azure</span><span class="sxs-lookup"><span data-stu-id="ae7d3-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="ae7d3-104">[![Titulní obrázek](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="ae7d3-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="ae7d3-105">Pomocí [Vačk Soper](https://twitter.com/camsoper) a [Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="ae7d3-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="ae7d3-106">Tato příručka je k dispozici jako [ke stažení e-knihy ve formátu PDF](https://aka.ms/devopsbook).</span><span class="sxs-lookup"><span data-stu-id="ae7d3-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="ae7d3-107">Vítáme vás</span><span class="sxs-lookup"><span data-stu-id="ae7d3-107">Welcome</span></span> 

<span data-ttu-id="ae7d3-108">Vítá vás Průvodce životním cyklem vývoje Azure pro .NET!</span><span class="sxs-lookup"><span data-stu-id="ae7d3-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="ae7d3-109">Tato příručka zavádí základní koncepty vytváření životního cyklu vývoje kolem Azure pomocí nástrojů a procesů .NET.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="ae7d3-110">Po dokončení tohoto průvodce budete těžit výhody DevOpse sada nástrojůu pro dospělé.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="ae7d3-111">Koho je tato příručka určena pro</span><span class="sxs-lookup"><span data-stu-id="ae7d3-111">Who this guide is for</span></span>

<span data-ttu-id="ae7d3-112">Měli byste být zkušeným ASP.NET Core vývojářem (úroveň 200-300).</span><span class="sxs-lookup"><span data-stu-id="ae7d3-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="ae7d3-113">Nemusíte nic dělat na Azure, protože v tomto úvodu pokryjeme.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="ae7d3-114">Tato příručka může být užitečná také pro inženýry DevOps, kteří se podrobněji zaměřují na operace než vývoj.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="ae7d3-115">Tato příručka cílí na vývojáře v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-115">This guide targets Windows developers.</span></span> <span data-ttu-id="ae7d3-116">Systémy Linux a macOS jsou však plně podporovány rozhraním .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="ae7d3-117">Pokud chcete tento průvodce upravit pro Linux/macOS, podívejte se na popisky pro rozdíly v systému Linux/macOS.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="ae7d3-118">Co tato příručka nepokrývá</span><span class="sxs-lookup"><span data-stu-id="ae7d3-118">What this guide doesn't cover</span></span>

<span data-ttu-id="ae7d3-119">Tato příručka se zaměřuje na ucelené prostředí průběžného nasazování pro vývojáře na platformě .NET.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="ae7d3-120">Není vyčerpávajícím průvodcem pro všechny věci Azure a nezaměřuje se na to rozsáhle rozhraní API .NET pro služby Azure.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="ae7d3-121">Důraz je vše kolem nepřetržité integrace, nasazení, monitorování a ladění.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="ae7d3-122">Na konci příručky jsou k dispozici doporučení pro další kroky.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="ae7d3-123">Součástí návrhů jsou služby platformy Azure, které jsou užitečné pro ASP.NET Core vývojářům.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="ae7d3-124">Co je v této příručce</span><span class="sxs-lookup"><span data-stu-id="ae7d3-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="ae7d3-125">Nástroje a soubory ke stažení</span><span class="sxs-lookup"><span data-stu-id="ae7d3-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="ae7d3-126">Zjistěte, kde získat nástroje používané v této příručce.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="ae7d3-127">Nasazení do App Service</span><span class="sxs-lookup"><span data-stu-id="ae7d3-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="ae7d3-128">Seznamte se s různými způsoby nasazení ASP.NET Core aplikace do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="ae7d3-129">Průběžná integrace a nasazování</span><span class="sxs-lookup"><span data-stu-id="ae7d3-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="ae7d3-130">Pomocí GitHubu, Azure DevOps Services a Azure můžete vytvořit komplexní řešení pro průběžnou integraci a nasazení pro vaši aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="ae7d3-131">Monitorování a ladění</span><span class="sxs-lookup"><span data-stu-id="ae7d3-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="ae7d3-132">Pomocí nástrojů Azure můžete monitorovat, řešit problémy a ladit svou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="ae7d3-133">Další kroky</span><span class="sxs-lookup"><span data-stu-id="ae7d3-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="ae7d3-134">Další studijní materiály pro Azure ASP.NET Core Learning pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="ae7d3-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="ae7d3-135">Dodatečné úvodní čtení</span><span class="sxs-lookup"><span data-stu-id="ae7d3-135">Additional introductory reading</span></span>

<span data-ttu-id="ae7d3-136">Pokud je to vaše první riziko pro cloud computing, tyto články vysvětlují základy.</span><span class="sxs-lookup"><span data-stu-id="ae7d3-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="ae7d3-137">Co je cloud computing?</span><span class="sxs-lookup"><span data-stu-id="ae7d3-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="ae7d3-138">Příklady cloud computingu</span><span class="sxs-lookup"><span data-stu-id="ae7d3-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="ae7d3-139">Co je IaaS?</span><span class="sxs-lookup"><span data-stu-id="ae7d3-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="ae7d3-140">Co je PaaS?</span><span class="sxs-lookup"><span data-stu-id="ae7d3-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
