---
title: Hostování a nasazení serveru Blazor ASP.NET Core
author: guardrex
description: Naučte se hostovat a nasazovat Blazor serverovou aplikaci pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: e69b91035c65739dde724330e83793c0b8b5481a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775151"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="ce992-103">Server hostitele a Blazor nasazení</span><span class="sxs-lookup"><span data-stu-id="ce992-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="ce992-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ce992-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="ce992-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="ce992-105">Host configuration values</span></span>

<span data-ttu-id="ce992-106">Serverové aplikace mohou přijímat [hodnoty konfigurace obecného hostitele](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="ce992-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="ce992-107">Nasazení</span><span class="sxs-lookup"><span data-stu-id="ce992-107">Deployment</span></span>

<span data-ttu-id="ce992-108">Použití [ Blazor modelu hostování serveru](xref:blazor/hosting-models#blazor-server) Blazor je spuštěno na serveru aplikace z ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ce992-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="ce992-109">Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="ce992-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="ce992-110">Vyžaduje se webový server, který je hostitelem aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ce992-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="ce992-111">Visual Studio obsahuje šablonu projektu \*\* Blazor serverová aplikace\*\* (`blazorserverside` šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ).</span><span class="sxs-lookup"><span data-stu-id="ce992-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="ce992-112">Škálovatelnost</span><span class="sxs-lookup"><span data-stu-id="ce992-112">Scalability</span></span>

<span data-ttu-id="ce992-113">Naplánujte nasazení, aby se zajistilo co nejlepší využití dostupné infrastruktury Blazor pro serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ce992-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="ce992-114">Projděte si následující zdroje informací Blazor , které řeší škálovatelnost aplikace serveru:</span><span class="sxs-lookup"><span data-stu-id="ce992-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="ce992-115">[Základní informace o Blazor serverových aplikacích](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="ce992-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="ce992-116">Server nasazení</span><span class="sxs-lookup"><span data-stu-id="ce992-116">Deployment server</span></span>

<span data-ttu-id="ce992-117">Při zvažování škálovatelnosti jednoho serveru (horizontální navýšení kapacity) je pravděpodobným zdrojem dostupným pro aplikaci první prostředek, který bude aplikace vyčerpat při zvýšení požadavků uživatelů.</span><span class="sxs-lookup"><span data-stu-id="ce992-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="ce992-118">Dostupná paměť na serveru má vliv na:</span><span class="sxs-lookup"><span data-stu-id="ce992-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="ce992-119">Počet aktivních okruhů, které může server podporovat.</span><span class="sxs-lookup"><span data-stu-id="ce992-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="ce992-120">Latence uživatelského rozhraní u klienta.</span><span class="sxs-lookup"><span data-stu-id="ce992-120">UI latency on the client.</span></span>

<span data-ttu-id="ce992-121">Pokyny k vytváření zabezpečených a škálovatelných Blazor serverových aplikací <xref:security/blazor/server/threat-mitigation>najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="ce992-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server/threat-mitigation>.</span></span>

<span data-ttu-id="ce992-122">Každý okruh používá pro minimální aplikaci *Hello World*ve stylu přibližně 250 kB paměti.</span><span class="sxs-lookup"><span data-stu-id="ce992-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="ce992-123">Velikost okruhu závisí na kódu aplikace a požadavcích na údržbu stavu přidružených k jednotlivým součástem.</span><span class="sxs-lookup"><span data-stu-id="ce992-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="ce992-124">Doporučujeme změřit požadavky na prostředky během vývoje vaší aplikace a infrastruktury, ale následující směrný plán může být výchozím bodem plánování cíle nasazení: Pokud očekáváte, že vaše aplikace bude podporovat 5 000 souběžných uživatelů, zvažte rozpočtování aspoň 1,3 GB paměti serveru do aplikace (nebo ~ 273 KB na uživatele).</span><span class="sxs-lookup"><span data-stu-id="ce992-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="ce992-125">rozšířeného</span><span class="sxs-lookup"><span data-stu-id="ce992-125"> configuration</span></span>

Blazor<span data-ttu-id="ce992-126">Serverové aplikace používají ASP.NET Core SignalR ke komunikaci s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="ce992-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="ce992-127">podmínky hostování a škálování se vztahují na Blazor serverové aplikace. [ SignalR](xref:signalr/publish-to-azure-web-app)</span><span class="sxs-lookup"><span data-stu-id="ce992-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="ce992-128">funguje nejlépe při použití WebSockets jako SignalR přenosu z důvodu nižší latence, spolehlivosti a [zabezpečení](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="ce992-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="ce992-129">Dlouhé cyklické dotazování se používá SignalR , když nejsou objekty WebSocket k dispozici nebo když je aplikace explicitně nakonfigurovaná tak, aby používala dlouhé cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="ce992-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="ce992-130">Při nasazování do Azure App Service nakonfigurujte aplikaci tak, aby používala objekty WebSocket v nastaveních Azure Portal služby.</span><span class="sxs-lookup"><span data-stu-id="ce992-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="ce992-131">Podrobnosti o konfiguraci aplikace pro Azure App Service najdete v [ SignalR pokynech k publikování](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="ce992-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="ce992-132">Služba SignalR Azure</span><span class="sxs-lookup"><span data-stu-id="ce992-132">Azure SignalR Service</span></span>

<span data-ttu-id="ce992-133">Pro Blazor serverové aplikace doporučujeme [používat SignalR službu Azure](/azure/azure-signalr) .</span><span class="sxs-lookup"><span data-stu-id="ce992-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="ce992-134">Služba umožňuje škálovat Blazor serverovou aplikaci na velký počet souběžných SignalR připojení.</span><span class="sxs-lookup"><span data-stu-id="ce992-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="ce992-135">Kromě toho globální dostupnost SignalR a vysoce výkonná datová centra služby významně pomáhají při snižování latence z důvodu geografické oblasti.</span><span class="sxs-lookup"><span data-stu-id="ce992-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="ce992-136">Konfigurace aplikace (a volitelně zřízení) pro službu Azure SignalR :</span><span class="sxs-lookup"><span data-stu-id="ce992-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="ce992-137">Povolte službě podporovat *rychlé relace*, kde se klienti [při předvykreslování přesměrují zpátky na stejný server](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="ce992-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="ce992-138">Nastavte `ServerStickyMode` možnost nebo hodnotu konfigurace na `Required`.</span><span class="sxs-lookup"><span data-stu-id="ce992-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="ce992-139">Aplikace obvykle vytváří konfiguraci pomocí **jednoho** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ce992-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="ce992-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ce992-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="ce992-141">Konfigurace (použijte **jeden** z následujících přístupů):</span><span class="sxs-lookup"><span data-stu-id="ce992-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="ce992-142">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="ce992-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="ce992-143">Nastavení **konfigurační** > **aplikace** služby App Service v Azure Portal (**název**: `Azure:SignalR:ServerStickyMode`, **hodnota**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="ce992-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="ce992-144">Vytvořte profil publikování aplikací Azure v aplikaci Visual Studio pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ce992-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="ce992-145">Přidejte do profilu závislost \*\*služby Azure SignalR \*\* .</span><span class="sxs-lookup"><span data-stu-id="ce992-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="ce992-146">Pokud předplatné Azure nemá stávající instanci služby Azure SignalR , která se má přiřadit k aplikaci, vyberte \*\*vytvořit novou instanci služby Azure SignalR \*\* a zřídit novou instanci služby.</span><span class="sxs-lookup"><span data-stu-id="ce992-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="ce992-147">Publikovat aplikaci do Azure</span><span class="sxs-lookup"><span data-stu-id="ce992-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="ce992-148">IIS</span><span class="sxs-lookup"><span data-stu-id="ce992-148">IIS</span></span>

<span data-ttu-id="ce992-149">Při použití služby IIS povolte:</span><span class="sxs-lookup"><span data-stu-id="ce992-149">When using IIS, enable:</span></span>

* <span data-ttu-id="ce992-150">[WebSockets ve službě IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="ce992-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="ce992-151">[Rychlé relace s směrováním žádostí o aplikace](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)</span><span class="sxs-lookup"><span data-stu-id="ce992-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="ce992-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="ce992-152">Kubernetes</span></span>

<span data-ttu-id="ce992-153">Vytvořte definici příchozího přenosu dat s následujícími [Kubernetes poznámkami pro rychlé relace](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="ce992-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="ce992-154">Linux na serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="ce992-154">Linux with Nginx</span></span>

<span data-ttu-id="ce992-155">SignalR Aby objekty WebSocket fungovaly správně, zkontrolujte, že je proxy `Upgrade` a `Connection` hlavičkový Server nastavené na následující hodnoty a `$connection_upgrade` zda jsou namapovány na jednu z těchto možností:</span><span class="sxs-lookup"><span data-stu-id="ce992-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="ce992-156">Hodnota hlavičky upgradu je standardně nastavená.</span><span class="sxs-lookup"><span data-stu-id="ce992-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="ce992-157">`close`Pokud záhlaví upgradu chybí nebo je prázdné.</span><span class="sxs-lookup"><span data-stu-id="ce992-157">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="ce992-158">Další informace najdete v těchto článcích:</span><span class="sxs-lookup"><span data-stu-id="ce992-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="ce992-159">NGINX jako proxy server WebSocket</span><span class="sxs-lookup"><span data-stu-id="ce992-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="ce992-160">Proxy server WebSocket</span><span class="sxs-lookup"><span data-stu-id="ce992-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a><span data-ttu-id="ce992-161">Měření latence sítě</span><span class="sxs-lookup"><span data-stu-id="ce992-161">Measure network latency</span></span>

<span data-ttu-id="ce992-162">[Zprostředkovatel komunikace js](xref:blazor/call-javascript-from-dotnet) lze použít k měření latence sítě, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="ce992-162">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="ce992-163">Pro přiměřené prostředí uživatelského rozhraní doporučujeme, abyste trvalou latenci uživatelského rozhraní 250ms nebo méně.</span><span class="sxs-lookup"><span data-stu-id="ce992-163">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
