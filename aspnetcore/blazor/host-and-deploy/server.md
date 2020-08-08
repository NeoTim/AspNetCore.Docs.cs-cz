---
title: ASP.NET Core hostitele a nasazeníBlazor Server
author: guardrex
description: Naučte se hostovat a nasazovat Blazor Server aplikaci pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/server
ms.openlocfilehash: e7c8627cd27fd30288b4bcfa1ac2ffe3e9b46e29
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014214"
---
# <a name="host-and-deploy-no-locblazor-server"></a><span data-ttu-id="66405-103">Hostitel a nasazeníBlazor Server</span><span class="sxs-lookup"><span data-stu-id="66405-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="66405-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="66405-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="66405-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="66405-105">Host configuration values</span></span>

<span data-ttu-id="66405-106">[ Blazor Server aplikace](xref:blazor/hosting-models#blazor-server) můžou přijímat [hodnoty konfigurace obecného hostitele](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="66405-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="66405-107">Nasazení</span><span class="sxs-lookup"><span data-stu-id="66405-107">Deployment</span></span>

<span data-ttu-id="66405-108">Použití [ Blazor Server modelu hostování](xref:blazor/hosting-models#blazor-server) Blazor je spuštěno na serveru aplikace z ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="66405-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="66405-109">Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="66405-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="66405-110">Vyžaduje se webový server, který je hostitelem aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="66405-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="66405-111">Visual Studio zahrnuje šablonu projektu \*\* Blazor Server aplikace\*\* ( `blazorserverside` Šablona při použití [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazu).</span><span class="sxs-lookup"><span data-stu-id="66405-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="66405-112">Škálovatelnost</span><span class="sxs-lookup"><span data-stu-id="66405-112">Scalability</span></span>

<span data-ttu-id="66405-113">Naplánujte nasazení, aby se zajistilo co nejlepší využití dostupné infrastruktury pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="66405-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="66405-114">Podívejte se na následující zdroje informací pro řešení Blazor Server škálovatelnosti aplikace:</span><span class="sxs-lookup"><span data-stu-id="66405-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="66405-115">Základy Blazor Server aplikací</span><span class="sxs-lookup"><span data-stu-id="66405-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="66405-116">Server nasazení</span><span class="sxs-lookup"><span data-stu-id="66405-116">Deployment server</span></span>

<span data-ttu-id="66405-117">Při zvažování škálovatelnosti jednoho serveru (horizontální navýšení kapacity) je pravděpodobným zdrojem dostupným pro aplikaci první prostředek, který bude aplikace vyčerpat při zvýšení požadavků uživatelů.</span><span class="sxs-lookup"><span data-stu-id="66405-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="66405-118">Dostupná paměť na serveru má vliv na:</span><span class="sxs-lookup"><span data-stu-id="66405-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="66405-119">Počet aktivních okruhů, které může server podporovat.</span><span class="sxs-lookup"><span data-stu-id="66405-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="66405-120">Latence uživatelského rozhraní u klienta.</span><span class="sxs-lookup"><span data-stu-id="66405-120">UI latency on the client.</span></span>

<span data-ttu-id="66405-121">Pokyny k vytváření zabezpečených a škálovatelných Blazor serverových aplikací najdete v tématu <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="66405-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="66405-122">Každý okruh používá pro minimální aplikaci *Hello World*ve stylu přibližně 250 kB paměti.</span><span class="sxs-lookup"><span data-stu-id="66405-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="66405-123">Velikost okruhu závisí na kódu aplikace a požadavcích na údržbu stavu přidružených k jednotlivým součástem.</span><span class="sxs-lookup"><span data-stu-id="66405-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="66405-124">Doporučujeme změřit požadavky na prostředky během vývoje vaší aplikace a infrastruktury, ale následující směrný plán může být výchozím bodem plánování cíle nasazení: Pokud očekáváte, že vaše aplikace bude podporovat 5 000 souběžných uživatelů, zvažte rozpočtování aspoň 1,3 GB paměti serveru do aplikace (nebo ~ 273 KB na uživatele).</span><span class="sxs-lookup"><span data-stu-id="66405-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="no-locsignalr-configuration"></a><span data-ttu-id="66405-125">SignalRrozšířeného</span><span class="sxs-lookup"><span data-stu-id="66405-125">SignalR configuration</span></span>

<span data-ttu-id="66405-126">Blazor Serveraplikace používají ASP.NET Core SignalR ke komunikaci s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="66405-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="66405-127">[ SignalR podmínky hostování a škálování](xref:signalr/publish-to-azure-web-app) se vztahují na Blazor Server aplikace.</span><span class="sxs-lookup"><span data-stu-id="66405-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="66405-128">Blazorfunguje nejlépe při použití WebSockets jako SignalR přenosu z důvodu nižší latence, spolehlivosti a [zabezpečení](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="66405-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="66405-129">Dlouhé cyklické dotazování se používá, SignalR když nejsou objekty WebSocket k dispozici nebo když je aplikace explicitně nakonfigurovaná tak, aby používala dlouhé cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="66405-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="66405-130">Při nasazování do Azure App Service nakonfigurujte aplikaci tak, aby používala objekty WebSocket v nastaveních Azure Portal služby.</span><span class="sxs-lookup"><span data-stu-id="66405-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="66405-131">Podrobnosti o konfiguraci aplikace pro Azure App Service najdete v [ SignalR pokynech k publikování](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="66405-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-no-locsignalr-service"></a><span data-ttu-id="66405-132">SignalRSlužba Azure</span><span class="sxs-lookup"><span data-stu-id="66405-132">Azure SignalR Service</span></span>

<span data-ttu-id="66405-133">Pro aplikace doporučujeme používat [ SignalR službu Azure](/azure/azure-signalr) Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="66405-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="66405-134">Služba umožňuje horizontální navýšení kapacity Blazor Server aplikace na velký počet souběžných SignalR připojení.</span><span class="sxs-lookup"><span data-stu-id="66405-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="66405-135">Kromě toho SignalR globální dostupnost a vysoce výkonná datová centra služby významně pomáhají při snižování latence z důvodu geografické oblasti.</span><span class="sxs-lookup"><span data-stu-id="66405-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="66405-136">Konfigurace aplikace (a volitelně zřízení) pro SignalR službu Azure:</span><span class="sxs-lookup"><span data-stu-id="66405-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="66405-137">Povolte službě podporovat *rychlé relace*, kde se klienti [při předvykreslování přesměrují zpátky na stejný server](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="66405-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="66405-138">Nastavte `ServerStickyMode` možnost nebo hodnotu konfigurace na `Required` .</span><span class="sxs-lookup"><span data-stu-id="66405-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="66405-139">Aplikace obvykle vytváří konfiguraci pomocí **jednoho** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="66405-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="66405-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="66405-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="66405-141">Konfigurace (použijte **jeden** z následujících přístupů):</span><span class="sxs-lookup"><span data-stu-id="66405-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="66405-142">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="66405-142">`appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="66405-143">Nastavení **konfigurační**  >  **aplikace** služby App Service v Azure Portal (**název**: `Azure:SignalR:ServerStickyMode` , **hodnota**: `Required` ).</span><span class="sxs-lookup"><span data-stu-id="66405-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="66405-144">Vytvořte profil publikování aplikací Azure v aplikaci Visual Studio pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="66405-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="66405-145">Přidejte do profilu závislost \*\* SignalR služby Azure\*\* .</span><span class="sxs-lookup"><span data-stu-id="66405-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="66405-146">Pokud předplatné Azure nemá stávající SignalR instanci služby Azure, která se má přiřadit k aplikaci, vyberte **vytvořit novou SignalR instanci služby Azure** a zřídit novou instanci služby.</span><span class="sxs-lookup"><span data-stu-id="66405-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="66405-147">Publikovat aplikaci do Azure</span><span class="sxs-lookup"><span data-stu-id="66405-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="66405-148">IIS</span><span class="sxs-lookup"><span data-stu-id="66405-148">IIS</span></span>

<span data-ttu-id="66405-149">Při použití služby IIS povolte:</span><span class="sxs-lookup"><span data-stu-id="66405-149">When using IIS, enable:</span></span>

* <span data-ttu-id="66405-150">[WebSockets ve službě IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="66405-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="66405-151">[Rychlé relace s směrováním žádostí o aplikace](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)</span><span class="sxs-lookup"><span data-stu-id="66405-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="66405-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="66405-152">Kubernetes</span></span>

<span data-ttu-id="66405-153">Vytvořte definici příchozího přenosu dat s následujícími [Kubernetes poznámkami pro rychlé relace](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="66405-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="66405-154">Linux na serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="66405-154">Linux with Nginx</span></span>

<span data-ttu-id="66405-155">SignalRAby objekty WebSocket fungovaly správně, zkontrolujte, že je proxy `Upgrade` a `Connection` hlavičkový Server nastavené na následující hodnoty a zda jsou `$connection_upgrade` namapovány na jednu z těchto možností:</span><span class="sxs-lookup"><span data-stu-id="66405-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="66405-156">Hodnota hlavičky upgradu je standardně nastavená.</span><span class="sxs-lookup"><span data-stu-id="66405-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="66405-157">`close`Pokud záhlaví upgradu chybí nebo je prázdné.</span><span class="sxs-lookup"><span data-stu-id="66405-157">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="66405-158">Další informace najdete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="66405-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="66405-159">NGINX jako proxy server WebSocket</span><span class="sxs-lookup"><span data-stu-id="66405-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="66405-160">Proxy server WebSocket</span><span class="sxs-lookup"><span data-stu-id="66405-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="66405-161">Linux na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="66405-161">Linux with Apache</span></span>

<span data-ttu-id="66405-162">Pro hostování Blazor aplikace za Apache v systému Linux nakonfigurujte `ProxyPass` provoz http a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="66405-162">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="66405-163">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="66405-163">In the following example:</span></span>

* <span data-ttu-id="66405-164">Na hostitelském počítači je spuštěný Server Kestrel.</span><span class="sxs-lookup"><span data-stu-id="66405-164">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="66405-165">Aplikace naslouchá provozu na portu 5000.</span><span class="sxs-lookup"><span data-stu-id="66405-165">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="66405-166">Povolte následující moduly:</span><span class="sxs-lookup"><span data-stu-id="66405-166">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="66405-167">Podívejte se na chyby protokolu WebSocket v konzole prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="66405-167">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="66405-168">Příklady chyb:</span><span class="sxs-lookup"><span data-stu-id="66405-168">Example errors:</span></span>

* <span data-ttu-id="66405-169">Firefox nemůže navázat připojení k serveru na adrese ws://the-domain-name.tld/_blazor?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="66405-169">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="66405-170">Chyba: Nepodařilo se spustit objekty WebSockets transportu: Chyba: došlo k chybě přenosu.</span><span class="sxs-lookup"><span data-stu-id="66405-170">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="66405-171">Chyba: nepovedlo se spustit přenos ' LongPolling ': TypeError: this. Transport není definovaný.</span><span class="sxs-lookup"><span data-stu-id="66405-171">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="66405-172">Chyba: Nelze se připojit k serveru pomocí žádné z dostupných přenosů.</span><span class="sxs-lookup"><span data-stu-id="66405-172">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="66405-173">Selhání objektu WebSockets</span><span class="sxs-lookup"><span data-stu-id="66405-173">WebSockets failed</span></span>
* <span data-ttu-id="66405-174">Chyba: nelze odeslat data, pokud připojení není ve stavu připojeno.</span><span class="sxs-lookup"><span data-stu-id="66405-174">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="66405-175">Další informace najdete v [dokumentaci k Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="66405-175">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="66405-176">Měření latence sítě</span><span class="sxs-lookup"><span data-stu-id="66405-176">Measure network latency</span></span>

<span data-ttu-id="66405-177">[Zprostředkovatel komunikace js](xref:blazor/call-javascript-from-dotnet) lze použít k měření latence sítě, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="66405-177">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="66405-178">Pro přiměřené prostředí uživatelského rozhraní doporučujeme, abyste trvalou latenci uživatelského rozhraní 250ms nebo méně.</span><span class="sxs-lookup"><span data-stu-id="66405-178">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
