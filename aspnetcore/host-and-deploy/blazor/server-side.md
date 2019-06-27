---
title: Hostitelství a nasazení ASP.NET Core Blazor na straně serveru
author: guardrex
description: Zjistěte, jak hostovat a nasazení aplikace na straně serveru Blazor pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8b332c2fb439e9832d604ed26f972b266eed2507
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406125"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="f66b0-103">Hostitelství a nasazení Blazor na straně serveru</span><span class="sxs-lookup"><span data-stu-id="f66b0-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="f66b0-104">Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f66b0-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="f66b0-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="f66b0-105">Host configuration values</span></span>

<span data-ttu-id="f66b0-106">Serverové aplikace, které používají [model hostingu na straně serveru](xref:blazor/hosting-models#server-side) může přijmout [obecný hostitele konfigurační hodnoty](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="f66b0-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="f66b0-107">Nasazení</span><span class="sxs-lookup"><span data-stu-id="f66b0-107">Deployment</span></span>

<span data-ttu-id="f66b0-108">S [model hostingu na straně serveru](xref:blazor/hosting-models#server-side), Blazor provádí na serveru z v rámci aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f66b0-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="f66b0-109">Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="f66b0-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="f66b0-110">Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný.</span><span class="sxs-lookup"><span data-stu-id="f66b0-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="f66b0-111">Visual Studio obsahuje **Blazor (serverové)** šablonu projektu (`blazorserverside` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).</span><span class="sxs-lookup"><span data-stu-id="f66b0-111">Visual Studio includes the **Blazor (server-side)** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="connection-scale-out"></a><span data-ttu-id="f66b0-112">Připojení pro horizontální navýšení kapacity</span><span class="sxs-lookup"><span data-stu-id="f66b0-112">Connection scale out</span></span>

<span data-ttu-id="f66b0-113">Blazor serverové aplikace vyžadují jedno aktivní připojení SignalR pro každého uživatele.</span><span class="sxs-lookup"><span data-stu-id="f66b0-113">Blazor server-side apps require one active SignalR connection for each user.</span></span> <span data-ttu-id="f66b0-114">Produkční nasazení na straně serveru Blazor vyžaduje řešení pro podporu tolik souběžných připojení podle potřeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="f66b0-114">A production Blazor server-side deployment requires a solution for supporting as many concurrent connections as required by the app.</span></span> <span data-ttu-id="f66b0-115">[Služby Azure SignalR](/azure/azure-signalr/) zpracovává škálování připojení a doporučuje se jako škálování řešení pro Blazor aplikace na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="f66b0-115">The [Azure SignalR Service](/azure/azure-signalr/) handles the scaling of connections and is recommended as a scaling solution for Blazor server-side apps.</span></span> <span data-ttu-id="f66b0-116">Další informace naleznete v tématu <xref:signalr/publish-to-azure-web-app>.</span><span class="sxs-lookup"><span data-stu-id="f66b0-116">For more information, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="signalr-configuration"></a><span data-ttu-id="f66b0-117">Konfigurace funkce SignalR</span><span class="sxs-lookup"><span data-stu-id="f66b0-117">SignalR configuration</span></span>

<span data-ttu-id="f66b0-118">SignalR je konfigurována pomocí ASP.NET Core pro nejběžnější scénáře Blazor na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="f66b0-118">SignalR is configured by ASP.NET Core for the most common Blazor server-side scenarios.</span></span> <span data-ttu-id="f66b0-119">Pro vlastní a pokročilé scénáře, najdete v článcích SignalR v [další prostředky](#additional-resources) oddílu.</span><span class="sxs-lookup"><span data-stu-id="f66b0-119">For custom and advanced scenarios, consult the SignalR articles in the [Additional resources](#additional-resources) section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f66b0-120">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f66b0-120">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="f66b0-121">Dokumentace ke službě Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="f66b0-121">Azure SignalR Service Documentation</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="f66b0-122">Rychlý start: Vytvoření chatovací místnosti s použitím služby SignalR</span><span class="sxs-lookup"><span data-stu-id="f66b0-122">Quickstart: Create a chat room by using SignalR Service</span></span>](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="f66b0-123">Nasazení ve verzi preview ASP.NET Core do služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f66b0-123">Deploy ASP.NET Core preview release to Azure App Service</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
