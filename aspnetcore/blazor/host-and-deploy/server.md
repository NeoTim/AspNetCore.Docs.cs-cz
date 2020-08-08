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
# <a name="host-and-deploy-no-locblazor-server"></a>Hostitel a nasazeníBlazor Server

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

[ Blazor Server aplikace](xref:blazor/hosting-models#blazor-server) můžou přijímat [hodnoty konfigurace obecného hostitele](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Nasazení

Použití [ Blazor Server modelu hostování](xref:blazor/hosting-models#blazor-server) Blazor je spuštěno na serveru aplikace z ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes [SignalR](xref:signalr/introduction) připojení.

Vyžaduje se webový server, který je hostitelem aplikace ASP.NET Core. Visual Studio zahrnuje šablonu projektu ** Blazor Server aplikace** ( `blazorserverside` Šablona při použití [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazu).

## <a name="scalability"></a>Škálovatelnost

Naplánujte nasazení, aby se zajistilo co nejlepší využití dostupné infrastruktury pro Blazor Server aplikaci. Podívejte se na následující zdroje informací pro řešení Blazor Server škálovatelnosti aplikace:

* [Základy Blazor Server aplikací](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a>Server nasazení

Při zvažování škálovatelnosti jednoho serveru (horizontální navýšení kapacity) je pravděpodobným zdrojem dostupným pro aplikaci první prostředek, který bude aplikace vyčerpat při zvýšení požadavků uživatelů. Dostupná paměť na serveru má vliv na:

* Počet aktivních okruhů, které může server podporovat.
* Latence uživatelského rozhraní u klienta.

Pokyny k vytváření zabezpečených a škálovatelných Blazor serverových aplikací najdete v tématu <xref:blazor/security/server/threat-mitigation> .

Každý okruh používá pro minimální aplikaci *Hello World*ve stylu přibližně 250 kB paměti. Velikost okruhu závisí na kódu aplikace a požadavcích na údržbu stavu přidružených k jednotlivým součástem. Doporučujeme změřit požadavky na prostředky během vývoje vaší aplikace a infrastruktury, ale následující směrný plán může být výchozím bodem plánování cíle nasazení: Pokud očekáváte, že vaše aplikace bude podporovat 5 000 souběžných uživatelů, zvažte rozpočtování aspoň 1,3 GB paměti serveru do aplikace (nebo ~ 273 KB na uživatele).

### <a name="no-locsignalr-configuration"></a>SignalRrozšířeného

Blazor Serveraplikace používají ASP.NET Core SignalR ke komunikaci s prohlížečem. [ SignalR podmínky hostování a škálování](xref:signalr/publish-to-azure-web-app) se vztahují na Blazor Server aplikace.

Blazorfunguje nejlépe při použití WebSockets jako SignalR přenosu z důvodu nižší latence, spolehlivosti a [zabezpečení](xref:signalr/security). Dlouhé cyklické dotazování se používá, SignalR když nejsou objekty WebSocket k dispozici nebo když je aplikace explicitně nakonfigurovaná tak, aby používala dlouhé cyklické dotazování. Při nasazování do Azure App Service nakonfigurujte aplikaci tak, aby používala objekty WebSocket v nastaveních Azure Portal služby. Podrobnosti o konfiguraci aplikace pro Azure App Service najdete v [ SignalR pokynech k publikování](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-no-locsignalr-service"></a>SignalRSlužba Azure

Pro aplikace doporučujeme používat [ SignalR službu Azure](/azure/azure-signalr) Blazor Server . Služba umožňuje horizontální navýšení kapacity Blazor Server aplikace na velký počet souběžných SignalR připojení. Kromě toho SignalR globální dostupnost a vysoce výkonná datová centra služby významně pomáhají při snižování latence z důvodu geografické oblasti. Konfigurace aplikace (a volitelně zřízení) pro SignalR službu Azure:

1. Povolte službě podporovat *rychlé relace*, kde se klienti [při předvykreslování přesměrují zpátky na stejný server](xref:blazor/hosting-models#connection-to-the-server). Nastavte `ServerStickyMode` možnost nebo hodnotu konfigurace na `Required` . Aplikace obvykle vytváří konfiguraci pomocí **jednoho** z následujících přístupů:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Konfigurace (použijte **jeden** z následujících přístupů):
  
     * `appsettings.json`:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * Nastavení **konfigurační**  >  **aplikace** služby App Service v Azure Portal (**název**: `Azure:SignalR:ServerStickyMode` , **hodnota**: `Required` ).

1. Vytvořte profil publikování aplikací Azure v aplikaci Visual Studio pro Blazor Server aplikaci.
1. Přidejte do profilu závislost ** SignalR služby Azure** . Pokud předplatné Azure nemá stávající SignalR instanci služby Azure, která se má přiřadit k aplikaci, vyberte **vytvořit novou SignalR instanci služby Azure** a zřídit novou instanci služby.
1. Publikovat aplikaci do Azure

#### <a name="iis"></a>IIS

Při použití služby IIS povolte:

* [WebSockets ve službě IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Rychlé relace s směrováním žádostí o aplikace](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)

#### <a name="kubernetes"></a>Kubernetes

Vytvořte definici příchozího přenosu dat s následujícími [Kubernetes poznámkami pro rychlé relace](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

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

#### <a name="linux-with-nginx"></a>Linux na serveru Nginx

SignalRAby objekty WebSocket fungovaly správně, zkontrolujte, že je proxy `Upgrade` a `Connection` hlavičkový Server nastavené na následující hodnoty a zda jsou `$connection_upgrade` namapovány na jednu z těchto možností:

* Hodnota hlavičky upgradu je standardně nastavená.
* `close`Pokud záhlaví upgradu chybí nebo je prázdné.

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

Další informace najdete v následujících článcích:

* [NGINX jako proxy server WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Proxy server WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a>Linux na serveru Apache

Pro hostování Blazor aplikace za Apache v systému Linux nakonfigurujte `ProxyPass` provoz http a WebSockets.

V následujícím příkladu:

* Na hostitelském počítači je spuštěný Server Kestrel.
* Aplikace naslouchá provozu na portu 5000.

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

Povolte následující moduly:

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

Podívejte se na chyby protokolu WebSocket v konzole prohlížeče. Příklady chyb:

* Firefox nemůže navázat připojení k serveru na adrese ws://the-domain-name.tld/_blazor?id=XXX.
* Chyba: Nepodařilo se spustit objekty WebSockets transportu: Chyba: došlo k chybě přenosu.
* Chyba: nepovedlo se spustit přenos ' LongPolling ': TypeError: this. Transport není definovaný.
* Chyba: Nelze se připojit k serveru pomocí žádné z dostupných přenosů. Selhání objektu WebSockets
* Chyba: nelze odeslat data, pokud připojení není ve stavu připojeno.

Další informace najdete v [dokumentaci k Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).

### <a name="measure-network-latency"></a>Měření latence sítě

[Zprostředkovatel komunikace js](xref:blazor/call-javascript-from-dotnet) lze použít k měření latence sítě, jak ukazuje následující příklad:

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

Pro přiměřené prostředí uživatelského rozhraní doporučujeme, abyste trvalou latenci uživatelského rozhraní 250ms nebo méně.
