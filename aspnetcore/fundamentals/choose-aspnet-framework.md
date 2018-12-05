---
title: Zvolte mezi ASP.NET 4.x a ASP.NET Core
author: rick-anderson
description: Vysvětluje, ASP.NET Core vs. ASP.NET 4.x a jak si vybrat mezi nimi.
ms.author: riande
ms.date: 09/11/2018
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 79e56e10b756677431ceff289300c251e54bf632
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861612"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="5efb0-103">Zvolte mezi ASP.NET 4.x a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5efb0-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="5efb0-104">ASP.NET Core je verzí ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="5efb0-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="5efb0-105">V tomto článku jsou uvedeny rozdíly mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="5efb0-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="5efb0-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5efb0-106">ASP.NET Core</span></span>

<span data-ttu-id="5efb0-107">ASP.NET Core je open source, napříč platformami platforma pro vytváření moderních cloudových webových aplikací ve Windows, macOS nebo Linuxu.</span><span class="sxs-lookup"><span data-stu-id="5efb0-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="5efb0-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="5efb0-108">ASP.NET 4.x</span></span>

<span data-ttu-id="5efb0-109">ASP.NET 4.x je vyspělá architektura, která poskytuje služby nezbytné k sestavení na podnikové úrovni, server webových aplikací založených na Windows.</span><span class="sxs-lookup"><span data-stu-id="5efb0-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="5efb0-110">Výběr rozhraní Framework</span><span class="sxs-lookup"><span data-stu-id="5efb0-110">Framework selection</span></span>

<span data-ttu-id="5efb0-111">Následující tabulka porovnává ASP.NET Core, ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="5efb0-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="5efb0-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5efb0-112">ASP.NET Core</span></span> | <span data-ttu-id="5efb0-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="5efb0-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="5efb0-114">Sestavení pro Windows, macOS nebo Linux</span><span class="sxs-lookup"><span data-stu-id="5efb0-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="5efb0-115">Sestavení pro Windows</span><span class="sxs-lookup"><span data-stu-id="5efb0-115">Build for Windows</span></span>|
|<span data-ttu-id="5efb0-116">[Stránky Razor](xref:razor-pages/index) je doporučený postup pro vytváření webového uživatelského rozhraní k ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="5efb0-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="5efb0-117">Viz také [MVC](xref:mvc/overview), [webové rozhraní API](xref:tutorials/first-web-api), a [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="5efb0-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="5efb0-118">Použití [webových formulářů](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [webové rozhraní API](/aspnet/web-api/), [Webhooky](/aspnet/webhooks/), nebo [webové stránky](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="5efb0-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="5efb0-119">Více verzí na počítač</span><span class="sxs-lookup"><span data-stu-id="5efb0-119">Multiple versions per machine</span></span>|<span data-ttu-id="5efb0-120">Jedna verze na počítač</span><span class="sxs-lookup"><span data-stu-id="5efb0-120">One version per machine</span></span>|
|<span data-ttu-id="5efb0-121">Vývoj s využitím sady Visual Studio [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/), nebo [Visual Studio Code](https://code.visualstudio.com/) pomocí C# neboF#</span><span class="sxs-lookup"><span data-stu-id="5efb0-121">Develop with Visual Studio, [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="5efb0-122">Vývoj s využitím sady Visual Studio C#, VB, neboF#</span><span class="sxs-lookup"><span data-stu-id="5efb0-122">Develop with Visual Studio using C#, VB, or F#</span></span>|
|<span data-ttu-id="5efb0-123">Vyšší výkon než ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="5efb0-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="5efb0-124">Dobrý výkon</span><span class="sxs-lookup"><span data-stu-id="5efb0-124">Good performance</span></span>|
|[<span data-ttu-id="5efb0-125">Zvolte rozhraní .NET Framework nebo .NET Core runtime</span><span class="sxs-lookup"><span data-stu-id="5efb0-125">Choose .NET Framework or .NET Core runtime</span></span>](/dotnet/articles/standard/choosing-core-framework-server)|<span data-ttu-id="5efb0-126">Použít modul runtime rozhraní .NET Framework</span><span class="sxs-lookup"><span data-stu-id="5efb0-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="5efb0-127">Zobrazit [ASP.NET Core, které cílí na rozhraní .NET Framework](xref:index#target-framework) informace o podpoře ASP.NET Core 2.x na rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5efb0-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="5efb0-128">Scénáře ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5efb0-128">ASP.NET Core scenarios</span></span>

* <span data-ttu-id="5efb0-129">[Stránky Razor](xref:razor-pages/index) je doporučený postup pro vytváření webového uživatelského rozhraní k ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="5efb0-129">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span>
* [<span data-ttu-id="5efb0-130">Weby</span><span class="sxs-lookup"><span data-stu-id="5efb0-130">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="5efb0-131">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="5efb0-131">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="5efb0-132">V reálném čase</span><span class="sxs-lookup"><span data-stu-id="5efb0-132">Real-time</span></span>](xref:signalr/index)
* [<span data-ttu-id="5efb0-133">Nasazení aplikace ASP.NET Core do Azure</span><span class="sxs-lookup"><span data-stu-id="5efb0-133">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="5efb0-134">ASP.NET 4.x scénáře</span><span class="sxs-lookup"><span data-stu-id="5efb0-134">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="5efb0-135">Weby</span><span class="sxs-lookup"><span data-stu-id="5efb0-135">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="5efb0-136">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="5efb0-136">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="5efb0-137">V reálném čase</span><span class="sxs-lookup"><span data-stu-id="5efb0-137">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="5efb0-138">Vytvoření webové aplikace ASP.NET 4.x v Azure</span><span class="sxs-lookup"><span data-stu-id="5efb0-138">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="5efb0-139">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5efb0-139">Additional resources</span></span>

* [<span data-ttu-id="5efb0-140">Úvod do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="5efb0-140">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="5efb0-141">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5efb0-141">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>

> [!NOTE]
> <span data-ttu-id="5efb0-142">Testujeme použitelnost navrhované nové struktury pro obsah ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5efb0-142">We’re testing the usability of a proposed new structure for the ASP.NET Core table of contents.</span></span>  <span data-ttu-id="5efb0-143">Pokud máte pár minut a chcete si vyzkoušet cvičení, které spočívá ve vyhledání 7 různých témat v aktuálním nebo navrhovaném obsahu, [klikněte prosím sem a zúčastněte se studie](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span><span class="sxs-lookup"><span data-stu-id="5efb0-143">If you have a few minutes to try an exercise of finding 7 different topics in the current or proposed table of contents, please [click here to participate in the study](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span></span>