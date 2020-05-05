---
title: Vyberte ASP.NET 4. x a ASP.NET Core
author: rick-anderson
description: Vysvětluje ASP.NET Core vs. ASP.NET 4. x a jejich výběr.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 1fb81d5a54cf332ca473af8fbe1841813a127be7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775879"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="e689d-103">Vyberte ASP.NET 4. x a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e689d-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="e689d-104">ASP.NET Core je přepracování ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="e689d-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="e689d-105">V tomto článku jsou uvedeny rozdíly mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="e689d-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="e689d-106">Jádro ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e689d-106">ASP.NET Core</span></span>

<span data-ttu-id="e689d-107">ASP.NET Core je open source architektura pro různé platformy pro vytváření moderních cloudových webových aplikací ve Windows, macOS nebo Linux.</span><span class="sxs-lookup"><span data-stu-id="e689d-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="e689d-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="e689d-108">ASP.NET 4.x</span></span>

<span data-ttu-id="e689d-109">ASP.NET 4. x je vyspělý rámec, který poskytuje služby potřebné k sestavení webových aplikací založených na podnikové úrovni v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="e689d-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="e689d-110">Výběr architektury</span><span class="sxs-lookup"><span data-stu-id="e689d-110">Framework selection</span></span>

<span data-ttu-id="e689d-111">Následující tabulka porovnává ASP.NET Core ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="e689d-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="e689d-112">Jádro ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e689d-112">ASP.NET Core</span></span> | <span data-ttu-id="e689d-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="e689d-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="e689d-114">Sestavení pro Windows, macOS nebo Linux</span><span class="sxs-lookup"><span data-stu-id="e689d-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="e689d-115">Sestavení pro Windows</span><span class="sxs-lookup"><span data-stu-id="e689d-115">Build for Windows</span></span>|
|<span data-ttu-id="e689d-116">Stránky jsou doporučeným přístupem k vytvoření webového uživatelského rozhraní ASP.NET Core 2. x. [ Razor ](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="e689d-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="e689d-117">Viz také [MVC](xref:mvc/overview), [webové rozhraní API](xref:tutorials/first-web-api)a [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="e689d-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="e689d-118">Použití [webových formulářů](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [webového rozhraní API](/aspnet/web-api/), [webhooků](/aspnet/webhooks/)nebo [webových stránek](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="e689d-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="e689d-119">Více verzí na počítač</span><span class="sxs-lookup"><span data-stu-id="e689d-119">Multiple versions per machine</span></span>|<span data-ttu-id="e689d-120">Jedna verze na počítač</span><span class="sxs-lookup"><span data-stu-id="e689d-120">One version per machine</span></span>|
|<span data-ttu-id="e689d-121">Vývoj pomocí sady [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/)nebo [Visual Studio Code](https://code.visualstudio.com/) pomocí C# nebo F #</span><span class="sxs-lookup"><span data-stu-id="e689d-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="e689d-122">Vývoj pomocí sady [Visual Studio](https://visualstudio.microsoft.com/vs/) pomocí jazyků C#, vb nebo F #</span><span class="sxs-lookup"><span data-stu-id="e689d-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="e689d-123">Vyšší výkon než ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="e689d-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="e689d-124">Dobrý výkon</span><span class="sxs-lookup"><span data-stu-id="e689d-124">Good performance</span></span>|
|[<span data-ttu-id="e689d-125">Použití .NET Core Runtime</span><span class="sxs-lookup"><span data-stu-id="e689d-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="e689d-126">Použití modulu runtime .NET Framework</span><span class="sxs-lookup"><span data-stu-id="e689d-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="e689d-127">Informace o podpoře ASP.NET Core 2. x na .NET Framework najdete v tématu [ASP.NET Core cílení na .NET Framework](xref:index#target-framework) .</span><span class="sxs-lookup"><span data-stu-id="e689d-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="e689d-128">ASP.NET Core scénáře</span><span class="sxs-lookup"><span data-stu-id="e689d-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="e689d-129">Websites</span><span class="sxs-lookup"><span data-stu-id="e689d-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="e689d-130">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e689d-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="e689d-131">Reálný čas</span><span class="sxs-lookup"><span data-stu-id="e689d-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="e689d-132">Nasazení aplikace ASP.NET Core do Azure</span><span class="sxs-lookup"><span data-stu-id="e689d-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="e689d-133">ASP.NET 4. x – scénáře</span><span class="sxs-lookup"><span data-stu-id="e689d-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="e689d-134">Websites</span><span class="sxs-lookup"><span data-stu-id="e689d-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="e689d-135">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e689d-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="e689d-136">Reálný čas</span><span class="sxs-lookup"><span data-stu-id="e689d-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="e689d-137">Vytvoření webové aplikace ASP.NET 4. x v Azure</span><span class="sxs-lookup"><span data-stu-id="e689d-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="e689d-138">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e689d-138">Additional resources</span></span>

* [<span data-ttu-id="e689d-139">Úvod do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e689d-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="e689d-140">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e689d-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
