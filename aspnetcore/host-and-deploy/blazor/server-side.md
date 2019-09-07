---
title: Hostování a nasazení ASP.NET Core Blazor na straně serveru
author: guardrex
description: Naučte se hostovat a nasazovat Blazor aplikaci na straně serveru pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8da71faf6abc5929d6cd43d42fd896e378d99ef6
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773577"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="52089-103">Hostování a nasazení Blazor na straně serveru</span><span class="sxs-lookup"><span data-stu-id="52089-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="52089-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="52089-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="52089-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="52089-105">Host configuration values</span></span>

<span data-ttu-id="52089-106">Aplikace na straně serveru, které používají [model hostování na straně serveru](xref:blazor/hosting-models#server-side) , mohou přijímat [hodnoty konfigurace obecného hostitele](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="52089-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="52089-107">Nasazení</span><span class="sxs-lookup"><span data-stu-id="52089-107">Deployment</span></span>

<span data-ttu-id="52089-108">S [modelem hostování na straně serveru](xref:blazor/hosting-models#server-side)je Blazor spuštěn na serveru z aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="52089-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="52089-109">Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes připojení k [signalizaci](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="52089-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="52089-110">Vyžaduje se webový server, který je hostitelem aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="52089-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="52089-111">Visual Studio obsahuje šablonu projektu **aplikace Blazor serveru** (`blazorserverside` šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ).</span><span class="sxs-lookup"><span data-stu-id="52089-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="connection-scale-out"></a><span data-ttu-id="52089-112">Horizontální navýšení kapacity připojení</span><span class="sxs-lookup"><span data-stu-id="52089-112">Connection scale out</span></span>

<span data-ttu-id="52089-113">Blazor aplikace na straně serveru vyžadují jedno aktivní připojení k signalizaci pro každého uživatele.</span><span class="sxs-lookup"><span data-stu-id="52089-113">Blazor server-side apps require one active SignalR connection for each user.</span></span> <span data-ttu-id="52089-114">Provozní Blazor nasazení na straně serveru vyžaduje řešení pro podporu tolika souběžných připojení, kolik vyžaduje aplikace.</span><span class="sxs-lookup"><span data-stu-id="52089-114">A production Blazor server-side deployment requires a solution for supporting as many concurrent connections as required by the app.</span></span> <span data-ttu-id="52089-115">[Služba signalizace Azure](/azure/azure-signalr/) zpracovává škálování připojení a doporučuje se jako řešení škálování pro Blazor aplikace na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="52089-115">The [Azure SignalR Service](/azure/azure-signalr/) handles the scaling of connections and is recommended as a scaling solution for Blazor server-side apps.</span></span> <span data-ttu-id="52089-116">Další informace naleznete v tématu <xref:signalr/publish-to-azure-web-app>.</span><span class="sxs-lookup"><span data-stu-id="52089-116">For more information, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="signalr-configuration"></a><span data-ttu-id="52089-117">Konfigurace signálu</span><span class="sxs-lookup"><span data-stu-id="52089-117">SignalR configuration</span></span>

<span data-ttu-id="52089-118">Služba Signal je nakonfigurována ASP.NET Core pro nejběžnější scénáře Blazor na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="52089-118">SignalR is configured by ASP.NET Core for the most common Blazor server-side scenarios.</span></span> <span data-ttu-id="52089-119">Vlastní a pokročilé scénáře najdete v článcích o signalizaci v části [Další zdroje informací](#additional-resources) .</span><span class="sxs-lookup"><span data-stu-id="52089-119">For custom and advanced scenarios, consult the SignalR articles in the [Additional resources](#additional-resources) section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52089-120">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="52089-120">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="52089-121">Dokumentace ke službě Azure Signal</span><span class="sxs-lookup"><span data-stu-id="52089-121">Azure SignalR Service Documentation</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="52089-122">Rychlý start: Vytvoření chatovací místnosti pomocí služby Signal</span><span class="sxs-lookup"><span data-stu-id="52089-122">Quickstart: Create a chat room by using SignalR Service</span></span>](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="52089-123">Nasazení verze Preview ASP.NET Core do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="52089-123">Deploy ASP.NET Core preview release to Azure App Service</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
