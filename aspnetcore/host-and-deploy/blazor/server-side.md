---
title: Hostitelství a nasazení Blazor na straně serveru
author: guardrex
description: Zjistěte, jak hostovat a nasazení aplikace na straně serveru Blazor pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 39e4f0634d40e8c720fa5a6a76bc0843487a4e3c
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982585"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="75143-103">Hostitelství a nasazení Blazor na straně serveru</span><span class="sxs-lookup"><span data-stu-id="75143-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="75143-104">Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="75143-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="75143-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="75143-105">Host configuration values</span></span>

<span data-ttu-id="75143-106">Serverové aplikace, které používají [model hostingu na straně serveru](xref:blazor/hosting-models#server-side) může přijmout [obecný hostitele konfigurační hodnoty](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="75143-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="75143-107">Nasazení</span><span class="sxs-lookup"><span data-stu-id="75143-107">Deployment</span></span>

<span data-ttu-id="75143-108">S [model hostingu na straně serveru](xref:blazor/hosting-models#server-side), Blazor provádí na serveru z v rámci aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75143-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="75143-109">Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="75143-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="75143-110">Aplikace je součástí aplikace ASP.NET Core v publikované výstupní a dvou aplikací se nasazují společně.</span><span class="sxs-lookup"><span data-stu-id="75143-110">The app is included with the ASP.NET Core app in the published output, and the two apps are deployed together.</span></span> <span data-ttu-id="75143-111">Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný.</span><span class="sxs-lookup"><span data-stu-id="75143-111">A web server that's capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="75143-112">Pro nasazení na straně serveru, Visual Studio obsahuje **Blazor (serverové)** šablonu projektu (`blazorserverside` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).</span><span class="sxs-lookup"><span data-stu-id="75143-112">For a server-side deployment, Visual Studio includes the **Blazor (server-side)** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

<span data-ttu-id="75143-113">Další informace o nasazení a hostování aplikací ASP.NET Core najdete v tématu <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="75143-113">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="75143-114">Informace o nasazení do služby Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="75143-114">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>
