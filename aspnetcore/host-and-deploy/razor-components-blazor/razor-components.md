---
title: Hostitelství a nasazení součásti syntaxe Razor
author: guardrex
description: Zjistěte, jak hostovat a nasazení pomocí ASP.NET Core Razor součásti aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: host-and-deploy/razor-components-blazor/razor-components
ms.openlocfilehash: 18b09dfc80e2f517c7750ce0fe1510641624aea7
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59069774"
---
# <a name="host-and-deploy-razor-components"></a><span data-ttu-id="fd9d0-103">Hostitelství a nasazení součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="fd9d0-103">Host and deploy Razor Components</span></span>

<span data-ttu-id="fd9d0-104">Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="fd9d0-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="fd9d0-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="fd9d0-105">Host configuration values</span></span>

<span data-ttu-id="fd9d0-106">Součásti aplikace Razor, používající [model hostingu na straně serveru](xref:razor-components/hosting-models#server-side-hosting-model) může přijmout [obecný hostitele konfigurační hodnoty](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="fd9d0-106">Razor Components apps that use the [server-side hosting model](xref:razor-components/hosting-models#server-side-hosting-model) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="fd9d0-107">Nasazení</span><span class="sxs-lookup"><span data-stu-id="fd9d0-107">Deployment</span></span>

<span data-ttu-id="fd9d0-108">S [model hostingu na straně serveru](xref:razor-components/hosting-models#server-side-hosting-model), Razor komponent je provedeno na serveru z v rámci aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd9d0-108">With the [server-side hosting model](xref:razor-components/hosting-models#server-side-hosting-model), Razor Components is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="fd9d0-109">Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="fd9d0-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="fd9d0-110">Aplikace je součástí aplikace ASP.NET Core v publikované výstupní a dvou aplikací se nasazují společně.</span><span class="sxs-lookup"><span data-stu-id="fd9d0-110">The app is included with the ASP.NET Core app in the published output, and the two apps are deployed together.</span></span> <span data-ttu-id="fd9d0-111">Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný.</span><span class="sxs-lookup"><span data-stu-id="fd9d0-111">A web server that's capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="fd9d0-112">Pro nasazení na straně serveru, Visual Studio obsahuje **Razor komponenty** šablonu projektu (`razorcomponents` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).</span><span class="sxs-lookup"><span data-stu-id="fd9d0-112">For a server-side deployment, Visual Studio includes the **Razor Components** project template (`razorcomponents` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

<span data-ttu-id="fd9d0-113">Další informace o nasazení a hostování aplikací ASP.NET Core najdete v tématu <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="fd9d0-113">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="fd9d0-114">Informace o nasazení do služby Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="fd9d0-114">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>
