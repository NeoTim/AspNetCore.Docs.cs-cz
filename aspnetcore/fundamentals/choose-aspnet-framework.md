---
title: Zvolte mezi ASP.NET 4.x a ASP.NET Core
author: rick-anderson
description: Vysvětluje ASP.NET Core vs. ASP.NET 4. x a jejich výběr.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: a7280b59578ee1d96edeeccf9c9df0b0e4eb4eb8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665240"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="e0357-103">Zvolte mezi ASP.NET 4.x a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0357-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="e0357-104">ASP.NET Core je verzí ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="e0357-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="e0357-105">V tomto článku jsou uvedeny rozdíly mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="e0357-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="e0357-106">Jádro ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e0357-106">ASP.NET Core</span></span>

<span data-ttu-id="e0357-107">ASP.NET Core je open source, napříč platformami platforma pro vytváření moderních cloudových webových aplikací ve Windows, macOS nebo Linuxu.</span><span class="sxs-lookup"><span data-stu-id="e0357-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="e0357-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="e0357-108">ASP.NET 4.x</span></span>

<span data-ttu-id="e0357-109">ASP.NET 4.x je vyspělá architektura, která poskytuje služby nezbytné k sestavení na podnikové úrovni, server webových aplikací založených na Windows.</span><span class="sxs-lookup"><span data-stu-id="e0357-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="e0357-110">Výběr rozhraní Framework</span><span class="sxs-lookup"><span data-stu-id="e0357-110">Framework selection</span></span>

<span data-ttu-id="e0357-111">Následující tabulka porovnává ASP.NET Core, ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="e0357-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="e0357-112">Jádro ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e0357-112">ASP.NET Core</span></span> | <span data-ttu-id="e0357-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="e0357-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="e0357-114">Sestavení pro Windows, macOS nebo Linux</span><span class="sxs-lookup"><span data-stu-id="e0357-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="e0357-115">Sestavení pro Windows</span><span class="sxs-lookup"><span data-stu-id="e0357-115">Build for Windows</span></span>|
|<span data-ttu-id="e0357-116">[Razor Pages](xref:razor-pages/index) je doporučený postup pro vytvoření webového uživatelského rozhraní pro ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="e0357-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="e0357-117">Viz také [MVC](xref:mvc/overview), [webové rozhraní API](xref:tutorials/first-web-api)a [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="e0357-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="e0357-118">Použití [webových formulářů](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [webového rozhraní API](/aspnet/web-api/), [webhooků](/aspnet/webhooks/)nebo [webových stránek](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="e0357-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="e0357-119">Více verzí na počítač</span><span class="sxs-lookup"><span data-stu-id="e0357-119">Multiple versions per machine</span></span>|<span data-ttu-id="e0357-120">Jedna verze na počítač</span><span class="sxs-lookup"><span data-stu-id="e0357-120">One version per machine</span></span>|
|<span data-ttu-id="e0357-121">Vývoj pomocí sady [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/)nebo [Visual Studio Code](https://code.visualstudio.com/) pomocí C# neboF#</span><span class="sxs-lookup"><span data-stu-id="e0357-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="e0357-122">Vývoj pomocí sady [Visual Studio](https://visualstudio.microsoft.com/vs/) pomocí C#, vb neboF#</span><span class="sxs-lookup"><span data-stu-id="e0357-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="e0357-123">Vyšší výkon než ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="e0357-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="e0357-124">Dobrý výkon</span><span class="sxs-lookup"><span data-stu-id="e0357-124">Good performance</span></span>|
|[<span data-ttu-id="e0357-125">Použití .NET Core Runtime</span><span class="sxs-lookup"><span data-stu-id="e0357-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="e0357-126">Použít modul runtime rozhraní .NET Framework</span><span class="sxs-lookup"><span data-stu-id="e0357-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="e0357-127">Informace o podpoře ASP.NET Core 2. x na .NET Framework najdete v tématu [ASP.NET Core cílení na .NET Framework](xref:index#target-framework) .</span><span class="sxs-lookup"><span data-stu-id="e0357-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="e0357-128">Scénáře ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0357-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="e0357-129">Websites</span><span class="sxs-lookup"><span data-stu-id="e0357-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="e0357-130">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e0357-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="e0357-131">V reálném čase</span><span class="sxs-lookup"><span data-stu-id="e0357-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="e0357-132">Nasazení aplikace ASP.NET Core do Azure</span><span class="sxs-lookup"><span data-stu-id="e0357-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="e0357-133">ASP.NET 4.x scénáře</span><span class="sxs-lookup"><span data-stu-id="e0357-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="e0357-134">Websites</span><span class="sxs-lookup"><span data-stu-id="e0357-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="e0357-135">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e0357-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="e0357-136">V reálném čase</span><span class="sxs-lookup"><span data-stu-id="e0357-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="e0357-137">Vytvoření webové aplikace ASP.NET 4. x v Azure</span><span class="sxs-lookup"><span data-stu-id="e0357-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="e0357-138">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e0357-138">Additional resources</span></span>

* [<span data-ttu-id="e0357-139">Úvod do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e0357-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="e0357-140">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0357-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
