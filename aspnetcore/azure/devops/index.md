---
title: DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Průvodce, který poskytuje komplexní pokyny pro vytváření kanálu DevOps pro ASP.NET aplikace Core hostované v Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: mvc, seodec18
uid: azure/devops/index
ms.openlocfilehash: f45bb2a5dd4b3d1a820085ede7ce3219045ed80b
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658079"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="4f017-103">DevOps s využitím ASP.NET Core a Azure</span><span class="sxs-lookup"><span data-stu-id="4f017-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="4f017-104">[![Obrázek obálky](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="4f017-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="4f017-105">Podle [Cam Soper](https://twitter.com/camsoper) a [Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="4f017-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="4f017-106">Tato příručka je k dispozici jako [pdf e-kniha ke stažení](https://aka.ms/devopsbook).</span><span class="sxs-lookup"><span data-stu-id="4f017-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="4f017-107">Vítáme vás</span><span class="sxs-lookup"><span data-stu-id="4f017-107">Welcome</span></span> 

<span data-ttu-id="4f017-108">Vítá vás průvodce životním cyklem vývoje Azure pro rozhraní .NET!</span><span class="sxs-lookup"><span data-stu-id="4f017-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="4f017-109">Tato příručka představuje základní koncepty vytváření životního cyklu vývoje kolem Azure pomocí nástrojů a procesů .NET.</span><span class="sxs-lookup"><span data-stu-id="4f017-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="4f017-110">Po dokončení této příručky budete využívat výhod vyspělé řetězce nástrojů DevOps.</span><span class="sxs-lookup"><span data-stu-id="4f017-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="4f017-111">Pro koho je tato příručka určena</span><span class="sxs-lookup"><span data-stu-id="4f017-111">Who this guide is for</span></span>

<span data-ttu-id="4f017-112">Měli byste být zkušený ASP.NET core vývojář (200-300 úroveň).</span><span class="sxs-lookup"><span data-stu-id="4f017-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="4f017-113">O Azure nemusíte nic vědět, protože to v tomto úvodu pokryjeme.</span><span class="sxs-lookup"><span data-stu-id="4f017-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="4f017-114">Tato příručka může být také užitečné pro inženýry DevOps, kteří jsou více zaměřeny na operace než vývoj.</span><span class="sxs-lookup"><span data-stu-id="4f017-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="4f017-115">Tato příručka se zaměřuje na vývojáře systému Windows.</span><span class="sxs-lookup"><span data-stu-id="4f017-115">This guide targets Windows developers.</span></span> <span data-ttu-id="4f017-116">Linux a macOS jsou však plně podporovány .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4f017-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="4f017-117">Chcete-li přizpůsobit tuto příručku pro Linux / macOS, podívejte se na popisky pro rozdíly mezi Linuxem a macOS.</span><span class="sxs-lookup"><span data-stu-id="4f017-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="4f017-118">Co tato příručka nepokrývá</span><span class="sxs-lookup"><span data-stu-id="4f017-118">What this guide doesn't cover</span></span>

<span data-ttu-id="4f017-119">Tato příručka je zaměřena na komplexní průběžné nasazení pro vývojáře rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="4f017-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="4f017-120">Není to vyčerpávající průvodce pro všechny věci Azure a nezaměřuje se značně na rozhraní API .NET pro služby Azure.</span><span class="sxs-lookup"><span data-stu-id="4f017-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="4f017-121">Důraz je všude kolem průběžné integrace, nasazení, monitorování a ladění.</span><span class="sxs-lookup"><span data-stu-id="4f017-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="4f017-122">Na konci průvodce jsou nabízena doporučení pro další kroky.</span><span class="sxs-lookup"><span data-stu-id="4f017-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="4f017-123">Součástí návrhů jsou služby platformy Azure, které jsou užitečné pro ASP.NET vývojářům Core.</span><span class="sxs-lookup"><span data-stu-id="4f017-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="4f017-124">Co je v této příručce</span><span class="sxs-lookup"><span data-stu-id="4f017-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="4f017-125">Nástroje a soubory ke stažení</span><span class="sxs-lookup"><span data-stu-id="4f017-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="4f017-126">Přečtěte si, kde získat nástroje použité v této příručce.</span><span class="sxs-lookup"><span data-stu-id="4f017-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="4f017-127">Nasazení do App Service</span><span class="sxs-lookup"><span data-stu-id="4f017-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="4f017-128">Seznamte se s různými způsoby nasazení aplikace ASP.NET Core do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="4f017-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="4f017-129">Průběžná integrace a nasazování</span><span class="sxs-lookup"><span data-stu-id="4f017-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="4f017-130">Vytvořte komplexní řešení průběžné integrace a nasazení pro vaši aplikaci ASP.NET Core s GitHubem, službami Azure DevOps services a Azure.</span><span class="sxs-lookup"><span data-stu-id="4f017-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="4f017-131">Sledování a ladění</span><span class="sxs-lookup"><span data-stu-id="4f017-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="4f017-132">Pomocí nástrojů Azure můžete sledovat, řešit problémy a vyladit vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4f017-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="4f017-133">Další kroky</span><span class="sxs-lookup"><span data-stu-id="4f017-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="4f017-134">Další výukové trasy pro ASP.NET vývojáře Core, který se učí Azure.</span><span class="sxs-lookup"><span data-stu-id="4f017-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="4f017-135">Dodatečné úvodní čtení</span><span class="sxs-lookup"><span data-stu-id="4f017-135">Additional introductory reading</span></span>

<span data-ttu-id="4f017-136">Pokud je toto vaše první expozice cloud computingu, tyto články vysvětlují základy.</span><span class="sxs-lookup"><span data-stu-id="4f017-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="4f017-137">Co je cloud computing?</span><span class="sxs-lookup"><span data-stu-id="4f017-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="4f017-138">Příklady cloud computingu</span><span class="sxs-lookup"><span data-stu-id="4f017-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="4f017-139">Co je IaaS?</span><span class="sxs-lookup"><span data-stu-id="4f017-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="4f017-140">Co je PaaS?</span><span class="sxs-lookup"><span data-stu-id="4f017-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
