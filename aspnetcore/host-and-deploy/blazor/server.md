---
title: Hostování a nasazení ASP.NET Core Blazor serveru
author: guardrex
description: Naučte se hostovat a nasazovat aplikace Blazor serveru pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: a051d51e734fec4315da73d3c4df57706df7f363
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77465820"
---
# <a name="host-and-deploy-opno-locblazor-server"></a><span data-ttu-id="c5f85-103">Hostování a nasazení serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="c5f85-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="c5f85-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c5f85-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="c5f85-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="c5f85-105">Host configuration values</span></span>

<span data-ttu-id="c5f85-106">[aplikaceBlazor serveru](xref:blazor/hosting-models#blazor-server) můžou přijímat [hodnoty konfigurace obecného hostitele](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="c5f85-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="c5f85-107">Nasazení</span><span class="sxs-lookup"><span data-stu-id="c5f85-107">Deployment</span></span>

<span data-ttu-id="c5f85-108">Pomocí [modelu hostováníBlazor serveru](xref:blazor/hosting-models#blazor-server)se Blazor spouští na serveru z aplikace v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5f85-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="c5f85-109">Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu jsou zpracovávána prostřednictvím [SignalRho](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="c5f85-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="c5f85-110">Vyžaduje se webový server, který je hostitelem aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5f85-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="c5f85-111">Visual Studio obsahuje šablonu projektu **aplikaceBlazor Server** (šablona`blazorserverside` při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ).</span><span class="sxs-lookup"><span data-stu-id="c5f85-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="c5f85-112">Škálovatelnost</span><span class="sxs-lookup"><span data-stu-id="c5f85-112">Scalability</span></span>

<span data-ttu-id="c5f85-113">Naplánujte nasazení, aby se zajistilo co nejlepší využití dostupné infrastruktury pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f85-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="c5f85-114">Pro řešení Blazor škálovatelnosti aplikací serveru se podívejte na následující zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="c5f85-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="c5f85-115">[Základy Blazor serverových aplikací](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="c5f85-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="c5f85-116">Server nasazení</span><span class="sxs-lookup"><span data-stu-id="c5f85-116">Deployment server</span></span>

<span data-ttu-id="c5f85-117">Při zvažování škálovatelnosti jednoho serveru (horizontální navýšení kapacity) je pravděpodobným zdrojem dostupným pro aplikaci první prostředek, který bude aplikace vyčerpat při zvýšení požadavků uživatelů.</span><span class="sxs-lookup"><span data-stu-id="c5f85-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="c5f85-118">Dostupná paměť na serveru má vliv na:</span><span class="sxs-lookup"><span data-stu-id="c5f85-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="c5f85-119">Počet aktivních okruhů, které může server podporovat.</span><span class="sxs-lookup"><span data-stu-id="c5f85-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="c5f85-120">Latence uživatelského rozhraní u klienta.</span><span class="sxs-lookup"><span data-stu-id="c5f85-120">UI latency on the client.</span></span>

<span data-ttu-id="c5f85-121">Pokyny k vytváření zabezpečených a škálovatelných aplikací Blazor serveru najdete v tématu <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="c5f85-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="c5f85-122">Každý okruh používá pro minimální aplikaci *Hello World*ve stylu přibližně 250 kB paměti.</span><span class="sxs-lookup"><span data-stu-id="c5f85-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="c5f85-123">Velikost okruhu závisí na kódu aplikace a požadavcích na údržbu stavu přidružených k jednotlivým součástem.</span><span class="sxs-lookup"><span data-stu-id="c5f85-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="c5f85-124">Doporučujeme změřit požadavky na prostředky během vývoje vaší aplikace a infrastruktury, ale následující standardní hodnoty můžou být výchozím bodem plánování nasazení. Pokud očekáváte, že aplikace bude podporovat 5 000 souběžných uživatelů, zvažte rozpočtování na do aplikace je minimálně 1,3 GB paměti serveru (nebo ~ 273 KB na uživatele).</span><span class="sxs-lookup"><span data-stu-id="c5f85-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="opno-locsignalr-configuration"></a><span data-ttu-id="c5f85-125">Konfigurace SignalR</span><span class="sxs-lookup"><span data-stu-id="c5f85-125">SignalR configuration</span></span>

<span data-ttu-id="c5f85-126">aplikace Blazor serveru používají ke komunikaci s prohlížečem ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5f85-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="c5f85-127">[podmínky hostování a škálováníSignalR](xref:signalr/publish-to-azure-web-app) se vztahují na Blazor serverových aplikací.</span><span class="sxs-lookup"><span data-stu-id="c5f85-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="c5f85-128"> funguje nejlépe při použití WebSockets jako SignalR přenosů z důvodu nižší latence, spolehlivosti a [zabezpečení](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="c5f85-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="c5f85-129">Dlouhé cyklické dotazování používá SignalR, když nejsou objekty WebSocket dostupné, nebo když je aplikace explicitně nakonfigurovaná tak, aby používala dlouhé cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="c5f85-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="c5f85-130">Při nasazování do Azure App Service nakonfigurujte aplikaci tak, aby používala objekty WebSocket v nastaveních Azure Portal služby.</span><span class="sxs-lookup"><span data-stu-id="c5f85-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="c5f85-131">Podrobnosti o konfiguraci aplikace pro Azure App Service najdete v tématu [SignalR pokyny pro publikování](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="c5f85-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-opno-locsignalr-service"></a><span data-ttu-id="c5f85-132">Služba Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="c5f85-132">Azure SignalR Service</span></span>

<span data-ttu-id="c5f85-133">Pro Blazor serverových aplikací doporučujeme používat [službu Azure SignalR](/azure/azure-signalr) .</span><span class="sxs-lookup"><span data-stu-id="c5f85-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="c5f85-134">Služba umožňuje horizontální navýšení kapacity aplikace Blazor serveru na velký počet souběžných připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5f85-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="c5f85-135">Kromě toho je globální dosahování a vysoce výkonná datová centra služby SignalR významně pomáhají při snižování latence z důvodu geografické oblasti.</span><span class="sxs-lookup"><span data-stu-id="c5f85-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="c5f85-136">Konfigurace aplikace (a volitelně zřízení) služby Azure SignalR:</span><span class="sxs-lookup"><span data-stu-id="c5f85-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="c5f85-137">Povolte službě podporovat *rychlé relace*, kde se klienti [při předvykreslování přesměrují zpátky na stejný server](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="c5f85-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="c5f85-138">Nastavte možnost `ServerStickyMode` nebo hodnotu konfigurace na `Required`.</span><span class="sxs-lookup"><span data-stu-id="c5f85-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="c5f85-139">Aplikace obvykle vytváří konfiguraci pomocí **jednoho** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="c5f85-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="c5f85-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c5f85-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="c5f85-141">Konfigurace (použijte **jeden** z následujících přístupů):</span><span class="sxs-lookup"><span data-stu-id="c5f85-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="c5f85-142">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="c5f85-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="c5f85-143">**Konfigurace** app Service > **nastavení aplikace** v Azure Portal (**název**: `Azure:SignalR:ServerStickyMode`, **hodnota**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="c5f85-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="c5f85-144">Vytvořte profil publikování aplikací Azure v aplikaci Visual Studio pro aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="c5f85-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="c5f85-145">Přidejte do profilu závislost **služby Azure SignalR** .</span><span class="sxs-lookup"><span data-stu-id="c5f85-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="c5f85-146">Pokud předplatné Azure nemá stávající instanci služby Azure SignalR, která se má přiřadit k aplikaci, vyberte **vytvořit novou instanci služby azure SignalR** a zřídit novou instanci služby.</span><span class="sxs-lookup"><span data-stu-id="c5f85-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="c5f85-147">Publikovat aplikaci do Azure</span><span class="sxs-lookup"><span data-stu-id="c5f85-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="c5f85-148">IIS</span><span class="sxs-lookup"><span data-stu-id="c5f85-148">IIS</span></span>

<span data-ttu-id="c5f85-149">Při použití služby IIS jsou v rámci směrování žádostí na aplikace povoleny rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="c5f85-149">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="c5f85-150">Další informace najdete v tématu [Vyrovnávání zatížení HTTP pomocí směrování žádostí na aplikace](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="c5f85-150">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="c5f85-151">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="c5f85-151">Kubernetes</span></span>

<span data-ttu-id="c5f85-152">Vytvořte definici příchozího přenosu dat s následujícími [Kubernetes poznámkami pro rychlé relace](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="c5f85-152">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="c5f85-153">Linux na serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="c5f85-153">Linux with Nginx</span></span>

<span data-ttu-id="c5f85-154">Aby bylo možné správně fungovat SignalRch WebSockets, nastavte hlavičku proxy serveru `Upgrade` a `Connection` na následující:</span><span class="sxs-lookup"><span data-stu-id="c5f85-154">For SignalR WebSockets to function properly, set the proxy's `Upgrade` and `Connection` headers to the following:</span></span>

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

<span data-ttu-id="c5f85-155">Další informace najdete v tématu [Nginx jako proxy server WebSocket](https://www.nginx.com/blog/websocket-nginx/).</span><span class="sxs-lookup"><span data-stu-id="c5f85-155">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="c5f85-156">Měření latence sítě</span><span class="sxs-lookup"><span data-stu-id="c5f85-156">Measure network latency</span></span>

<span data-ttu-id="c5f85-157">[Zprostředkovatel komunikace js](xref:blazor/javascript-interop) lze použít k měření latence sítě, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="c5f85-157">[JS interop](xref:blazor/javascript-interop) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
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

<span data-ttu-id="c5f85-158">Pro přiměřené prostředí uživatelského rozhraní doporučujeme, abyste trvalou latenci uživatelského rozhraní 250ms nebo méně.</span><span class="sxs-lookup"><span data-stu-id="c5f85-158">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
