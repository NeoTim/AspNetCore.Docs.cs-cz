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
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 380bbab8898b4fbeab4efa514b17b807accbb1ac
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205862"
---
# <a name="host-and-deploy-blazor-server"></a>Server hostitele a Blazor nasazení

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

Serverové aplikace mohou přijímat [hodnoty konfigurace obecného hostitele](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)

## <a name="deployment"></a>Nasazení

Použití [ Blazor modelu hostování serveru](xref:blazor/hosting-models#blazor-server) Blazor je spuštěno na serveru aplikace z ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes [SignalR](xref:signalr/introduction) připojení.

Vyžaduje se webový server, který je hostitelem aplikace ASP.NET Core. Visual Studio obsahuje šablonu projektu ** Blazor serverová aplikace** (`blazorserverside` šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ).

## <a name="scalability"></a>Škálovatelnost

Naplánujte nasazení, aby se zajistilo co nejlepší využití dostupné infrastruktury Blazor pro serverovou aplikaci. Projděte si následující zdroje informací Blazor , které řeší škálovatelnost aplikace serveru:

* [Základní informace o Blazor serverových aplikacích](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a>Server nasazení

Při zvažování škálovatelnosti jednoho serveru (horizontální navýšení kapacity) je pravděpodobným zdrojem dostupným pro aplikaci první prostředek, který bude aplikace vyčerpat při zvýšení požadavků uživatelů. Dostupná paměť na serveru má vliv na:

* Počet aktivních okruhů, které může server podporovat.
* Latence uživatelského rozhraní u klienta.

Pokyny k vytváření zabezpečených a škálovatelných Blazor serverových aplikací <xref:security/blazor/server/threat-mitigation>najdete v tématu.

Každý okruh používá pro minimální aplikaci *Hello World*ve stylu přibližně 250 kB paměti. Velikost okruhu závisí na kódu aplikace a požadavcích na údržbu stavu přidružených k jednotlivým součástem. Doporučujeme změřit požadavky na prostředky během vývoje vaší aplikace a infrastruktury, ale následující směrný plán může být výchozím bodem plánování cíle nasazení: Pokud očekáváte, že vaše aplikace bude podporovat 5 000 souběžných uživatelů, zvažte rozpočtování aspoň 1,3 GB paměti serveru do aplikace (nebo ~ 273 KB na uživatele).

### <a name="signalr-configuration"></a>SignalRrozšířeného

BlazorServerové aplikace používají ASP.NET Core SignalR ke komunikaci s prohlížečem. podmínky hostování a škálování se vztahují na Blazor serverové aplikace. [ SignalR](xref:signalr/publish-to-azure-web-app)

Blazorfunguje nejlépe při použití WebSockets jako SignalR přenosu z důvodu nižší latence, spolehlivosti a [zabezpečení](xref:signalr/security). Dlouhé cyklické dotazování se používá SignalR , když nejsou objekty WebSocket k dispozici nebo když je aplikace explicitně nakonfigurovaná tak, aby používala dlouhé cyklické dotazování. Při nasazování do Azure App Service nakonfigurujte aplikaci tak, aby používala objekty WebSocket v nastaveních Azure Portal služby. Podrobnosti o konfiguraci aplikace pro Azure App Service najdete v [ SignalR pokynech k publikování](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-signalr-service"></a>Služba SignalR Azure

Pro Blazor serverové aplikace doporučujeme [používat SignalR službu Azure](/azure/azure-signalr) . Služba umožňuje škálovat Blazor serverovou aplikaci na velký počet souběžných SignalR připojení. Kromě toho globální dostupnost SignalR a vysoce výkonná datová centra služby významně pomáhají při snižování latence z důvodu geografické oblasti. Konfigurace aplikace (a volitelně zřízení) pro službu Azure SignalR :

1. Povolte službě podporovat *rychlé relace*, kde se klienti [při předvykreslování přesměrují zpátky na stejný server](xref:blazor/hosting-models#connection-to-the-server). Nastavte `ServerStickyMode` možnost nebo hodnotu konfigurace na `Required`. Aplikace obvykle vytváří konfiguraci pomocí **jednoho** z následujících přístupů:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Konfigurace (použijte **jeden** z následujících přístupů):
  
     * *appSettings. JSON*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * Nastavení **konfigurační** > **aplikace** služby App Service v Azure Portal (**název**: `Azure:SignalR:ServerStickyMode`, **hodnota**: `Required`).

1. Vytvořte profil publikování aplikací Azure v aplikaci Visual Studio pro Blazor serverovou aplikaci.
1. Přidejte do profilu závislost **služby Azure SignalR ** . Pokud předplatné Azure nemá stávající instanci služby Azure SignalR , která se má přiřadit k aplikaci, vyberte **vytvořit novou instanci služby Azure SignalR ** a zřídit novou instanci služby.
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

SignalR Aby objekty WebSocket fungovaly správně, zkontrolujte, že je proxy `Upgrade` a `Connection` hlavičkový Server nastavené na následující hodnoty a `$connection_upgrade` zda jsou namapovány na jednu z těchto možností:

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

Další informace najdete v těchto článcích:

* [NGINX jako proxy server WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Proxy server WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

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
