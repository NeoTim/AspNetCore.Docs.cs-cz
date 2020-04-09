---
title: Vyberte si mezi ASP.NET 4.x a ASP.NET Core
author: rick-anderson
description: Vysvětluje ASP.NET Core vs. ASP.NET 4.x a jak si vybrat mezi nimi.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: a7280b59578ee1d96edeeccf9c9df0b0e4eb4eb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665240"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="29f56-103">Vyberte si mezi ASP.NET 4.x a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29f56-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="29f56-104">ASP.NET Core je redesign ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="29f56-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="29f56-105">Tento článek uvádí rozdíly mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="29f56-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="29f56-106">Jádro ASP.NET</span><span class="sxs-lookup"><span data-stu-id="29f56-106">ASP.NET Core</span></span>

<span data-ttu-id="29f56-107">ASP.NET Core je open source, multiplatformní framework pro vytváření moderních cloudových webových aplikací ve Windows, macOS nebo Linuxu.</span><span class="sxs-lookup"><span data-stu-id="29f56-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="29f56-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="29f56-108">ASP.NET 4.x</span></span>

<span data-ttu-id="29f56-109">ASP.NET 4.x je vyspělý framework, který poskytuje služby potřebné k vytváření webových aplikací na podnikové úrovni založených na serveru v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="29f56-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="29f56-110">Výběr rámce</span><span class="sxs-lookup"><span data-stu-id="29f56-110">Framework selection</span></span>

<span data-ttu-id="29f56-111">Následující tabulka porovnává ASP.NET Core s ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="29f56-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="29f56-112">Jádro ASP.NET</span><span class="sxs-lookup"><span data-stu-id="29f56-112">ASP.NET Core</span></span> | <span data-ttu-id="29f56-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="29f56-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="29f56-114">Sestavení pro Windows, macOS nebo Linux</span><span class="sxs-lookup"><span data-stu-id="29f56-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="29f56-115">Sestavení pro Windows</span><span class="sxs-lookup"><span data-stu-id="29f56-115">Build for Windows</span></span>|
|<span data-ttu-id="29f56-116">[Razor Pages](xref:razor-pages/index) je doporučený přístup k vytvoření webového uživatelského uživatelského panelu od ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="29f56-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="29f56-117">Viz také [MVC](xref:mvc/overview), [SignalR](xref:signalr/introduction)Web [API](xref:tutorials/first-web-api)a .</span><span class="sxs-lookup"><span data-stu-id="29f56-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="29f56-118">Použití [webových formulářů](/aspnet/web-forms) [SignalR](/aspnet/signalr), , [MVC](/aspnet/mvc), [webového rozhraní API](/aspnet/web-api/), [webhooků](/aspnet/webhooks/)nebo [webových stránek](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="29f56-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="29f56-119">Více verzí na počítač</span><span class="sxs-lookup"><span data-stu-id="29f56-119">Multiple versions per machine</span></span>|<span data-ttu-id="29f56-120">Jedna verze na počítač</span><span class="sxs-lookup"><span data-stu-id="29f56-120">One version per machine</span></span>|
|<span data-ttu-id="29f56-121">Vývoj pomocí [sady Visual Studio](https://visualstudio.microsoft.com/vs/), Visual Studio for [Mac](https://visualstudio.microsoft.com/vs/mac/)nebo Kódu sady [Visual Studio](https://code.visualstudio.com/) pomocí jazyka C# nebo F #</span><span class="sxs-lookup"><span data-stu-id="29f56-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="29f56-122">Vývoj pomocí [sady Visual Studio](https://visualstudio.microsoft.com/vs/) pomocí jazyka C#, VB nebo F #</span><span class="sxs-lookup"><span data-stu-id="29f56-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="29f56-123">Vyšší výkon než ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="29f56-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="29f56-124">Dobrý výkon</span><span class="sxs-lookup"><span data-stu-id="29f56-124">Good performance</span></span>|
|[<span data-ttu-id="29f56-125">Použití runtime jádra .NET</span><span class="sxs-lookup"><span data-stu-id="29f56-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="29f56-126">Použití runtime rozhraní .NET Framework</span><span class="sxs-lookup"><span data-stu-id="29f56-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="29f56-127">Informace o podpoře jádra 2.x ASP.NET ASP.NET [jádra .NET Framework](xref:index#target-framework) v rozhraní .NET Framework naleznete v tématu ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="29f56-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="29f56-128">ASP.NET základní scénáře</span><span class="sxs-lookup"><span data-stu-id="29f56-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="29f56-129">Websites</span><span class="sxs-lookup"><span data-stu-id="29f56-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="29f56-130">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="29f56-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="29f56-131">Reálný čas</span><span class="sxs-lookup"><span data-stu-id="29f56-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="29f56-132">Nasazení aplikace ASP.NET Core do Azure</span><span class="sxs-lookup"><span data-stu-id="29f56-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="29f56-133">ASP.NET scénáře 4.x</span><span class="sxs-lookup"><span data-stu-id="29f56-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="29f56-134">Websites</span><span class="sxs-lookup"><span data-stu-id="29f56-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="29f56-135">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="29f56-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="29f56-136">Reálný čas</span><span class="sxs-lookup"><span data-stu-id="29f56-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="29f56-137">Vytvoření webové aplikace ASP.NET 4.x v Azure</span><span class="sxs-lookup"><span data-stu-id="29f56-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="29f56-138">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="29f56-138">Additional resources</span></span>

* [<span data-ttu-id="29f56-139">Úvod do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="29f56-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="29f56-140">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29f56-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
