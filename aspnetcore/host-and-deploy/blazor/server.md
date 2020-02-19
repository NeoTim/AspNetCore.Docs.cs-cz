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
# <a name="host-and-deploy-opno-locblazor-server"></a>Hostování a nasazení serveru Blazor

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

[aplikaceBlazor serveru](xref:blazor/hosting-models#blazor-server) můžou přijímat [hodnoty konfigurace obecného hostitele](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Nasazení

Pomocí [modelu hostováníBlazor serveru](xref:blazor/hosting-models#blazor-server)se Blazor spouští na serveru z aplikace v ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu jsou zpracovávána prostřednictvím [SignalRho](xref:signalr/introduction) připojení.

Vyžaduje se webový server, který je hostitelem aplikace ASP.NET Core. Visual Studio obsahuje šablonu projektu **aplikaceBlazor Server** (šablona`blazorserverside` při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ).

## <a name="scalability"></a>Škálovatelnost

Naplánujte nasazení, aby se zajistilo co nejlepší využití dostupné infrastruktury pro aplikaci Blazor serveru. Pro řešení Blazor škálovatelnosti aplikací serveru se podívejte na následující zdroje informací:

* [Základy Blazor serverových aplikací](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Server nasazení

Při zvažování škálovatelnosti jednoho serveru (horizontální navýšení kapacity) je pravděpodobným zdrojem dostupným pro aplikaci první prostředek, který bude aplikace vyčerpat při zvýšení požadavků uživatelů. Dostupná paměť na serveru má vliv na:

* Počet aktivních okruhů, které může server podporovat.
* Latence uživatelského rozhraní u klienta.

Pokyny k vytváření zabezpečených a škálovatelných aplikací Blazor serveru najdete v tématu <xref:security/blazor/server>.

Každý okruh používá pro minimální aplikaci *Hello World*ve stylu přibližně 250 kB paměti. Velikost okruhu závisí na kódu aplikace a požadavcích na údržbu stavu přidružených k jednotlivým součástem. Doporučujeme změřit požadavky na prostředky během vývoje vaší aplikace a infrastruktury, ale následující standardní hodnoty můžou být výchozím bodem plánování nasazení. Pokud očekáváte, že aplikace bude podporovat 5 000 souběžných uživatelů, zvažte rozpočtování na do aplikace je minimálně 1,3 GB paměti serveru (nebo ~ 273 KB na uživatele).

### <a name="opno-locsignalr-configuration"></a>Konfigurace SignalR

aplikace Blazor serveru používají ke komunikaci s prohlížečem ASP.NET Core SignalR. [podmínky hostování a škálováníSignalR](xref:signalr/publish-to-azure-web-app) se vztahují na Blazor serverových aplikací.

Blazor funguje nejlépe při použití WebSockets jako SignalR přenosů z důvodu nižší latence, spolehlivosti a [zabezpečení](xref:signalr/security). Dlouhé cyklické dotazování používá SignalR, když nejsou objekty WebSocket dostupné, nebo když je aplikace explicitně nakonfigurovaná tak, aby používala dlouhé cyklické dotazování. Při nasazování do Azure App Service nakonfigurujte aplikaci tak, aby používala objekty WebSocket v nastaveních Azure Portal služby. Podrobnosti o konfiguraci aplikace pro Azure App Service najdete v tématu [SignalR pokyny pro publikování](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-opno-locsignalr-service"></a>Služba Azure SignalR

Pro Blazor serverových aplikací doporučujeme používat [službu Azure SignalR](/azure/azure-signalr) . Služba umožňuje horizontální navýšení kapacity aplikace Blazor serveru na velký počet souběžných připojení SignalR. Kromě toho je globální dosahování a vysoce výkonná datová centra služby SignalR významně pomáhají při snižování latence z důvodu geografické oblasti. Konfigurace aplikace (a volitelně zřízení) služby Azure SignalR:

1. Povolte službě podporovat *rychlé relace*, kde se klienti [při předvykreslování přesměrují zpátky na stejný server](xref:blazor/hosting-models#connection-to-the-server). Nastavte možnost `ServerStickyMode` nebo hodnotu konfigurace na `Required`. Aplikace obvykle vytváří konfiguraci pomocí **jednoho** z následujících přístupů:

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

     * **Konfigurace** app Service > **nastavení aplikace** v Azure Portal (**název**: `Azure:SignalR:ServerStickyMode`, **hodnota**: `Required`).

1. Vytvořte profil publikování aplikací Azure v aplikaci Visual Studio pro aplikaci Blazor Server.
1. Přidejte do profilu závislost **služby Azure SignalR** . Pokud předplatné Azure nemá stávající instanci služby Azure SignalR, která se má přiřadit k aplikaci, vyberte **vytvořit novou instanci služby azure SignalR** a zřídit novou instanci služby.
1. Publikovat aplikaci do Azure

#### <a name="iis"></a>IIS

Při použití služby IIS jsou v rámci směrování žádostí na aplikace povoleny rychlé relace. Další informace najdete v tématu [Vyrovnávání zatížení HTTP pomocí směrování žádostí na aplikace](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

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

Aby bylo možné správně fungovat SignalRch WebSockets, nastavte hlavičku proxy serveru `Upgrade` a `Connection` na následující:

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

Další informace najdete v tématu [Nginx jako proxy server WebSocket](https://www.nginx.com/blog/websocket-nginx/).

### <a name="measure-network-latency"></a>Měření latence sítě

[Zprostředkovatel komunikace js](xref:blazor/javascript-interop) lze použít k měření latence sítě, jak ukazuje následující příklad:

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
