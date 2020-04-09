---
title: Hostování a nasazení Blazor ASP.NET Core Server
author: guardrex
description: Přečtěte si, jak Blazor hostovat a nasazovat serverovou aplikaci pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 866bb348180c872d8ab20787283cfb7217183a8d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79025429"
---
# <a name="host-and-deploy-opno-locblazor-server"></a><span data-ttu-id="9959b-103">Hostování a Blazor nasazení serveru</span><span class="sxs-lookup"><span data-stu-id="9959b-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="9959b-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9959b-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="9959b-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="9959b-105">Host configuration values</span></span>

<span data-ttu-id="9959b-106">Serverové aplikace mohou přijímat [obecné hodnoty konfigurace hostitele](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="9959b-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="9959b-107">Nasazení</span><span class="sxs-lookup"><span data-stu-id="9959b-107">Deployment</span></span>

<span data-ttu-id="9959b-108">Pomocí [ Blazor modelu hostování](xref:blazor/hosting-models#blazor-server) Blazor serveru se na serveru spouští z aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9959b-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="9959b-109">Aktualizace ui, zpracování událostí a volání JavaScriptu jsou zpracovávány prostředpou [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="9959b-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="9959b-110">Je vyžadován webový server schopný hostit aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9959b-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="9959b-111">Visual Studio \*\* Blazor \*\* obsahuje šablonu`blazorserverside` projektu Server App (šablona při použití [dotnet new](/dotnet/core/tools/dotnet-new) příkazu).</span><span class="sxs-lookup"><span data-stu-id="9959b-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="9959b-112">Škálovatelnost</span><span class="sxs-lookup"><span data-stu-id="9959b-112">Scalability</span></span>

<span data-ttu-id="9959b-113">Naplánujte nasazení, abyste co nejlépe využili Blazor dostupnou infrastrukturu pro aplikaci Server.</span><span class="sxs-lookup"><span data-stu-id="9959b-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="9959b-114">Podívejte se na Blazor následující prostředky, které řeší škálovatelnost aplikací server:</span><span class="sxs-lookup"><span data-stu-id="9959b-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="9959b-115">[Základy Blazor serverových aplikací](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="9959b-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="9959b-116">Server pro nasazení</span><span class="sxs-lookup"><span data-stu-id="9959b-116">Deployment server</span></span>

<span data-ttu-id="9959b-117">Při zvažování škálovatelnosti jednoho serveru (vertikálně navýšit kapacitu) je paměť dostupná pro aplikaci pravděpodobně prvním prostředkem, který aplikace vyčerpá, protože se zvýší požadavky uživatelů.</span><span class="sxs-lookup"><span data-stu-id="9959b-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="9959b-118">Dostupná paměť na serveru ovlivňuje:</span><span class="sxs-lookup"><span data-stu-id="9959b-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="9959b-119">Počet aktivních obvodů, které může server podporovat.</span><span class="sxs-lookup"><span data-stu-id="9959b-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="9959b-120">Latence ui na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9959b-120">UI latency on the client.</span></span>

<span data-ttu-id="9959b-121">Pokyny k vytváření zabezpečených a Blazor <xref:security/blazor/server>škálovatelných serverových aplikací naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="9959b-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="9959b-122">Každý okruh používá přibližně 250 kB paměti pro minimální *Hello World*-style aplikace.</span><span class="sxs-lookup"><span data-stu-id="9959b-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="9959b-123">Velikost okruhu závisí na kódu aplikace a požadavcích na údržbu stavu přidružených ke každé součásti.</span><span class="sxs-lookup"><span data-stu-id="9959b-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="9959b-124">Doporučujeme měřit požadavky na prostředky během vývoje pro vaši aplikaci a infrastrukturu, ale následující směrný plán může být výchozím bodem při plánování cíle nasazení: Pokud očekáváte, že vaše aplikace bude podporovat 5 000 souběžných uživatelů, zvažte rozpočtování alespoň 1,3 GB paměti serveru do aplikace (nebo ~273 kB na uživatele).</span><span class="sxs-lookup"><span data-stu-id="9959b-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="opno-locsignalr-configuration"></a>SignalR<span data-ttu-id="9959b-125">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="9959b-125"> configuration</span></span>

Blazor<span data-ttu-id="9959b-126">Serverové aplikace SignalR používají ke komunikaci s prohlížečem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9959b-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="9959b-127">Podmínky hostování a škálování Blazor programu se vztahují na serverové aplikace. [ SignalR](xref:signalr/publish-to-azure-web-app)</span><span class="sxs-lookup"><span data-stu-id="9959b-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="9959b-128">Funguje nejlépe při použití WebSockets jako přenos z SignalR důvodu nižší latence, spolehlivosti a [zabezpečení](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="9959b-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="9959b-129">Dlouhé dotazování SignalR se používá, když WebSockets není k dispozici, nebo když je aplikace explicitně nakonfigurován a používat dlouhé dotazování.</span><span class="sxs-lookup"><span data-stu-id="9959b-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="9959b-130">Při nasazování do služby Azure App Service nakonfigurujte aplikaci tak, aby používala WebSockets v nastavení portálu Azure pro službu.</span><span class="sxs-lookup"><span data-stu-id="9959b-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="9959b-131">Podrobnosti o konfiguraci aplikace pro službu Azure App Service najdete v [ SignalR pokynech pro publikování](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="9959b-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-opno-locsignalr-service"></a><span data-ttu-id="9959b-132">Služba SignalR Azure</span><span class="sxs-lookup"><span data-stu-id="9959b-132">Azure SignalR Service</span></span>

<span data-ttu-id="9959b-133">Doporučujeme používat aplikace Blazor [Azure SignalR Service](/azure/azure-signalr) for Server.</span><span class="sxs-lookup"><span data-stu-id="9959b-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="9959b-134">Služba umožňuje škálování aplikace Blazor server na velký počet SignalR souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="9959b-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="9959b-135">Globální dosah SignalR služby a vysoce výkonná datová centra navíc výrazně pomáhají snižovat latenci kvůli zeměpisné poloze.</span><span class="sxs-lookup"><span data-stu-id="9959b-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="9959b-136">Konfigurace aplikace (a volitelně zřízení) SignalR služby Azure:</span><span class="sxs-lookup"><span data-stu-id="9959b-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="9959b-137">Povolte službu pro podporu *náročných relací*, kde jsou klienti [při předběžném vykreslování přesměrováni zpět na stejný server](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="9959b-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="9959b-138">Nastavte `ServerStickyMode` možnost nebo hodnotu konfigurace na . `Required`</span><span class="sxs-lookup"><span data-stu-id="9959b-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="9959b-139">Aplikace obvykle vytvoří konfiguraci pomocí **jednoho** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9959b-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="9959b-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9959b-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="9959b-141">Konfigurace (použijte **jeden** z následujících přístupů):</span><span class="sxs-lookup"><span data-stu-id="9959b-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="9959b-142">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="9959b-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="9959b-143">Nastavení **konfigurační** > **aplikace** služby aplikace na webu Azure Portal (**Název** `Azure:SignalR:ServerStickyMode`: , **Hodnota**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="9959b-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="9959b-144">Vytvořte profil publikování Aplikací Azure Blazor ve Visual Studiu pro aplikaci Server.</span><span class="sxs-lookup"><span data-stu-id="9959b-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="9959b-145">Přidejte závislost \*\*služby Azure SignalR \*\* do profilu.</span><span class="sxs-lookup"><span data-stu-id="9959b-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="9959b-146">Pokud předplatné Azure nemá již existující instanci služby Azure, SignalR kterou chcete přiřadit k aplikaci, vyberte Vytvořit novou \*\*instanci služby Azure SignalR \*\* pro zřízení nové instance služby.</span><span class="sxs-lookup"><span data-stu-id="9959b-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="9959b-147">Publikovat aplikaci do Azure</span><span class="sxs-lookup"><span data-stu-id="9959b-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="9959b-148">IIS</span><span class="sxs-lookup"><span data-stu-id="9959b-148">IIS</span></span>

<span data-ttu-id="9959b-149">Při používání služby IIS povolte:</span><span class="sxs-lookup"><span data-stu-id="9959b-149">When using IIS, enable:</span></span>

* <span data-ttu-id="9959b-150">[WebSockets ve službě IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="9959b-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="9959b-151">[Důležité relace s směrováním žádostí o aplikaci](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="9959b-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="9959b-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="9959b-152">Kubernetes</span></span>

<span data-ttu-id="9959b-153">Vytvořte definici příchozího přenosu dat s [následujícími poznámkami Kubernetes pro rychlé relace](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="9959b-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="9959b-154">Linux na serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="9959b-154">Linux with Nginx</span></span>

<span data-ttu-id="9959b-155">Aby SignalR websockety fungovaly správně, zkontrolujte, `Connection` zda jsou proxy servery `$connection_upgrade` a záhlaví nastaveny na následující hodnoty a které jsou mapovány na:For WebSockets to function properly, confirm that the proxy's `Upgrade` and headers are set to the following values and that is mapapped to either:</span><span class="sxs-lookup"><span data-stu-id="9959b-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="9959b-156">Hodnota hlavičky Upgradu je ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="9959b-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="9959b-157">`close`pokud záhlaví upgradu chybí nebo je prázdné.</span><span class="sxs-lookup"><span data-stu-id="9959b-157">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="9959b-158">Další informace najdete v těchto článcích:</span><span class="sxs-lookup"><span data-stu-id="9959b-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="9959b-159">NGINX jako proxy server WebSocket</span><span class="sxs-lookup"><span data-stu-id="9959b-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="9959b-160">Proxy websocket</span><span class="sxs-lookup"><span data-stu-id="9959b-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a><span data-ttu-id="9959b-161">Měření latence sítě</span><span class="sxs-lookup"><span data-stu-id="9959b-161">Measure network latency</span></span>

<span data-ttu-id="9959b-162">[JS interop](xref:blazor/call-javascript-from-dotnet) lze použít k měření latence sítě, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="9959b-162">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="9959b-163">Pro rozumné prostředí uživatelského prostředí doporučujeme trvalou latenci uživatelského prostředí 250 ms nebo méně.</span><span class="sxs-lookup"><span data-stu-id="9959b-163">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
