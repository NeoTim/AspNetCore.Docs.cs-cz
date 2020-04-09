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
# <a name="host-and-deploy-opno-locblazor-server"></a>Hostování a Blazor nasazení serveru

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

Serverové aplikace mohou přijímat [obecné hodnoty konfigurace hostitele](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)

## <a name="deployment"></a>Nasazení

Pomocí [ Blazor modelu hostování](xref:blazor/hosting-models#blazor-server) Blazor serveru se na serveru spouští z aplikace ASP.NET Core. Aktualizace ui, zpracování událostí a volání JavaScriptu jsou zpracovávány prostředpou [SignalR](xref:signalr/introduction) připojení.

Je vyžadován webový server schopný hostit aplikaci ASP.NET Core. Visual Studio ** Blazor ** obsahuje šablonu`blazorserverside` projektu Server App (šablona při použití [dotnet new](/dotnet/core/tools/dotnet-new) příkazu).

## <a name="scalability"></a>Škálovatelnost

Naplánujte nasazení, abyste co nejlépe využili Blazor dostupnou infrastrukturu pro aplikaci Server. Podívejte se na Blazor následující prostředky, které řeší škálovatelnost aplikací server:

* [Základy Blazor serverových aplikací](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Server pro nasazení

Při zvažování škálovatelnosti jednoho serveru (vertikálně navýšit kapacitu) je paměť dostupná pro aplikaci pravděpodobně prvním prostředkem, který aplikace vyčerpá, protože se zvýší požadavky uživatelů. Dostupná paměť na serveru ovlivňuje:

* Počet aktivních obvodů, které může server podporovat.
* Latence ui na straně klienta.

Pokyny k vytváření zabezpečených a Blazor <xref:security/blazor/server>škálovatelných serverových aplikací naleznete v tématu .

Každý okruh používá přibližně 250 kB paměti pro minimální *Hello World*-style aplikace. Velikost okruhu závisí na kódu aplikace a požadavcích na údržbu stavu přidružených ke každé součásti. Doporučujeme měřit požadavky na prostředky během vývoje pro vaši aplikaci a infrastrukturu, ale následující směrný plán může být výchozím bodem při plánování cíle nasazení: Pokud očekáváte, že vaše aplikace bude podporovat 5 000 souběžných uživatelů, zvažte rozpočtování alespoň 1,3 GB paměti serveru do aplikace (nebo ~273 kB na uživatele).

### <a name="opno-locsignalr-configuration"></a>SignalRKonfigurace

BlazorServerové aplikace SignalR používají ke komunikaci s prohlížečem ASP.NET Core. Podmínky hostování a škálování Blazor programu se vztahují na serverové aplikace. [ SignalR](xref:signalr/publish-to-azure-web-app)

BlazorFunguje nejlépe při použití WebSockets jako přenos z SignalR důvodu nižší latence, spolehlivosti a [zabezpečení](xref:signalr/security). Dlouhé dotazování SignalR se používá, když WebSockets není k dispozici, nebo když je aplikace explicitně nakonfigurován a používat dlouhé dotazování. Při nasazování do služby Azure App Service nakonfigurujte aplikaci tak, aby používala WebSockets v nastavení portálu Azure pro službu. Podrobnosti o konfiguraci aplikace pro službu Azure App Service najdete v [ SignalR pokynech pro publikování](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-opno-locsignalr-service"></a>Služba SignalR Azure

Doporučujeme používat aplikace Blazor [Azure SignalR Service](/azure/azure-signalr) for Server. Služba umožňuje škálování aplikace Blazor server na velký počet SignalR souběžných připojení. Globální dosah SignalR služby a vysoce výkonná datová centra navíc výrazně pomáhají snižovat latenci kvůli zeměpisné poloze. Konfigurace aplikace (a volitelně zřízení) SignalR služby Azure:

1. Povolte službu pro podporu *náročných relací*, kde jsou klienti [při předběžném vykreslování přesměrováni zpět na stejný server](xref:blazor/hosting-models#connection-to-the-server). Nastavte `ServerStickyMode` možnost nebo hodnotu konfigurace na . `Required` Aplikace obvykle vytvoří konfiguraci pomocí **jednoho** z následujících přístupů:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Konfigurace (použijte **jeden** z následujících přístupů):
  
     * *appsettings.json*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * Nastavení **konfigurační** > **aplikace** služby aplikace na webu Azure Portal (**Název** `Azure:SignalR:ServerStickyMode`: , **Hodnota**: `Required`).

1. Vytvořte profil publikování Aplikací Azure Blazor ve Visual Studiu pro aplikaci Server.
1. Přidejte závislost **služby Azure SignalR ** do profilu. Pokud předplatné Azure nemá již existující instanci služby Azure, SignalR kterou chcete přiřadit k aplikaci, vyberte Vytvořit novou **instanci služby Azure SignalR ** pro zřízení nové instance služby.
1. Publikovat aplikaci do Azure

#### <a name="iis"></a>IIS

Při používání služby IIS povolte:

* [WebSockets ve službě IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Důležité relace s směrováním žádostí o aplikaci](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Vytvořte definici příchozího přenosu dat s [následujícími poznámkami Kubernetes pro rychlé relace](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

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

Aby SignalR websockety fungovaly správně, zkontrolujte, `Connection` zda jsou proxy servery `$connection_upgrade` a záhlaví nastaveny na následující hodnoty a které jsou mapovány na:For WebSockets to function properly, confirm that the proxy's `Upgrade` and headers are set to the following values and that is mapapped to either:

* Hodnota hlavičky Upgradu je ve výchozím nastavení.
* `close`pokud záhlaví upgradu chybí nebo je prázdné.

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
* [Proxy websocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a>Měření latence sítě

[JS interop](xref:blazor/call-javascript-from-dotnet) lze použít k měření latence sítě, jak ukazuje následující příklad:

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

Pro rozumné prostředí uživatelského prostředí doporučujeme trvalou latenci uživatelského prostředí 250 ms nebo méně.
