---
title: Hostování a nasazení ASP.NET Core Blazor serveru
author: guardrex
description: Naučte se hostovat a nasazovat aplikace Blazor serveru pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: aedef7fe695dd4a0cbf04d3f3e9947f33f7afa40
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211604"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="16eea-103">Hostování a nasazení serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="16eea-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="16eea-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="16eea-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="16eea-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="16eea-105">Host configuration values</span></span>

<span data-ttu-id="16eea-106">[Serverové aplikace Blazor](xref:blazor/hosting-models#blazor-server) můžou přijímat [hodnoty konfigurace obecného hostitele](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="16eea-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="16eea-107">Nasazení</span><span class="sxs-lookup"><span data-stu-id="16eea-107">Deployment</span></span>

<span data-ttu-id="16eea-108">Pomocí [modelu hostování serveru Blazor](xref:blazor/hosting-models#blazor-server)se Blazor spouští na serveru z aplikace v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="16eea-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="16eea-109">Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes připojení [](xref:signalr/introduction) k signalizaci.</span><span class="sxs-lookup"><span data-stu-id="16eea-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="16eea-110">Vyžaduje se webový server, který je hostitelem aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="16eea-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="16eea-111">Visual Studio obsahuje šablonu projektu **aplikace Blazor serveru** (`blazorserverside` šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ).</span><span class="sxs-lookup"><span data-stu-id="16eea-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="16eea-112">Škálovatelnost</span><span class="sxs-lookup"><span data-stu-id="16eea-112">Scalability</span></span>

<span data-ttu-id="16eea-113">Naplánujte nasazení, aby se zajistilo co nejlepší využití dostupné infrastruktury pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="16eea-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="16eea-114">Podívejte se na následující zdroje informací pro řešení škálovatelnosti aplikace serveru Blazor:</span><span class="sxs-lookup"><span data-stu-id="16eea-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="16eea-115">Základy aplikací serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="16eea-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="16eea-116">Server nasazení</span><span class="sxs-lookup"><span data-stu-id="16eea-116">Deployment server</span></span>

<span data-ttu-id="16eea-117">Při zvažování škálovatelnosti jednoho serveru (horizontální navýšení kapacity) je pravděpodobným zdrojem dostupným pro aplikaci první prostředek, který bude aplikace vyčerpat při zvýšení požadavků uživatelů.</span><span class="sxs-lookup"><span data-stu-id="16eea-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="16eea-118">Dostupná paměť na serveru má vliv na:</span><span class="sxs-lookup"><span data-stu-id="16eea-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="16eea-119">Počet aktivních okruhů, které může server podporovat.</span><span class="sxs-lookup"><span data-stu-id="16eea-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="16eea-120">Latence uživatelského rozhraní u klienta.</span><span class="sxs-lookup"><span data-stu-id="16eea-120">UI latency on the client.</span></span>

<span data-ttu-id="16eea-121">Pokyny k vytváření zabezpečených a škálovatelných aplikací Blazor serveru najdete <xref:security/blazor/server>v tématu.</span><span class="sxs-lookup"><span data-stu-id="16eea-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="16eea-122">Každý okruh používá pro minimální aplikaci *Hello World*ve stylu přibližně 250 kB paměti.</span><span class="sxs-lookup"><span data-stu-id="16eea-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="16eea-123">Velikost okruhu závisí na kódu aplikace a požadavcích na údržbu stavu přidružených k jednotlivým součástem.</span><span class="sxs-lookup"><span data-stu-id="16eea-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="16eea-124">Doporučujeme změřit požadavky na prostředky během vývoje vaší aplikace a infrastruktury, ale následující směrný plán může být výchozím bodem při plánování nasazení cíle: Pokud očekáváte, že aplikace bude podporovat 5 000 souběžných uživatelů, zvažte rozpočtování do aplikace minimálně 1,3 GB paměti serveru (nebo ~ 273 KB na uživatele).</span><span class="sxs-lookup"><span data-stu-id="16eea-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="16eea-125">Konfigurace signálu</span><span class="sxs-lookup"><span data-stu-id="16eea-125">SignalR configuration</span></span>

<span data-ttu-id="16eea-126">Aplikace serveru Blazor používají ke komunikaci s prohlížečem ASP.NET Core signál.</span><span class="sxs-lookup"><span data-stu-id="16eea-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="16eea-127">[Podmínky hostování a škálování signalizace](xref:signalr/publish-to-azure-web-app) se vztahují na Blazor serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="16eea-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="16eea-128">Blazor funguje nejlépe při použití WebSockets jako přenosu signálu z důvodu nižší latence, spolehlivosti a [zabezpečení](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="16eea-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="16eea-129">Pokud nejsou objekty WebSockets dostupné nebo když je aplikace explicitně nakonfigurovaná tak, aby používala dlouhé cyklické dotazování, používá se pro ně signalizace dlouhé.</span><span class="sxs-lookup"><span data-stu-id="16eea-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="16eea-130">Při nasazování do Azure App Service nakonfigurujte aplikaci tak, aby používala objekty WebSocket v nastaveních Azure Portal služby.</span><span class="sxs-lookup"><span data-stu-id="16eea-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="16eea-131">Podrobnosti o konfiguraci aplikace pro Azure App Service najdete v [pokynech pro publikování signálu](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="16eea-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

<span data-ttu-id="16eea-132">Pro aplikace serveru Blazor doporučujeme používat [službu Azure signaler](/azure/azure-signalr) .</span><span class="sxs-lookup"><span data-stu-id="16eea-132">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="16eea-133">Služba umožňuje škálovat aplikace serveru Blazor na velký počet souběžných připojení k signalizaci.</span><span class="sxs-lookup"><span data-stu-id="16eea-133">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="16eea-134">Kromě toho globální dosah a vysoce výkonná datová centra služby signalizace významně pomáhají při snižování latence kvůli geografickým zeměpisům.</span><span class="sxs-lookup"><span data-stu-id="16eea-134">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="16eea-135">Měření latence sítě</span><span class="sxs-lookup"><span data-stu-id="16eea-135">Measure network latency</span></span>

<span data-ttu-id="16eea-136">[Zprostředkovatel komunikace js](xref:blazor/javascript-interop) lze použít k měření latence sítě, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="16eea-136">[JS interop](xref:blazor/javascript-interop) can be used to measure network latency, as the following example demonstrates:</span></span>

```cshtml
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="16eea-137">Pro přiměřené prostředí uživatelského rozhraní doporučujeme, abyste trvalou latenci uživatelského rozhraní 250ms nebo méně.</span><span class="sxs-lookup"><span data-stu-id="16eea-137">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
