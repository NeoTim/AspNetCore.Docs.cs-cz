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
# <a name="host-and-deploy-blazor-server"></a>Hostování a nasazení serveru Blazor

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

[Serverové aplikace Blazor](xref:blazor/hosting-models#blazor-server) můžou přijímat [hodnoty konfigurace obecného hostitele](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Nasazení

Pomocí [modelu hostování serveru Blazor](xref:blazor/hosting-models#blazor-server)se Blazor spouští na serveru z aplikace v ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes připojení [](xref:signalr/introduction) k signalizaci.

Vyžaduje se webový server, který je hostitelem aplikace ASP.NET Core. Visual Studio obsahuje šablonu projektu **aplikace Blazor serveru** (`blazorserverside` šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ).

## <a name="scalability"></a>Škálovatelnost

Naplánujte nasazení, aby se zajistilo co nejlepší využití dostupné infrastruktury pro aplikaci Blazor serveru. Podívejte se na následující zdroje informací pro řešení škálovatelnosti aplikace serveru Blazor:

* [Základy aplikací serveru Blazor](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Server nasazení

Při zvažování škálovatelnosti jednoho serveru (horizontální navýšení kapacity) je pravděpodobným zdrojem dostupným pro aplikaci první prostředek, který bude aplikace vyčerpat při zvýšení požadavků uživatelů. Dostupná paměť na serveru má vliv na:

* Počet aktivních okruhů, které může server podporovat.
* Latence uživatelského rozhraní u klienta.

Pokyny k vytváření zabezpečených a škálovatelných aplikací Blazor serveru najdete <xref:security/blazor/server>v tématu.

Každý okruh používá pro minimální aplikaci *Hello World*ve stylu přibližně 250 kB paměti. Velikost okruhu závisí na kódu aplikace a požadavcích na údržbu stavu přidružených k jednotlivým součástem. Doporučujeme změřit požadavky na prostředky během vývoje vaší aplikace a infrastruktury, ale následující směrný plán může být výchozím bodem při plánování nasazení cíle: Pokud očekáváte, že aplikace bude podporovat 5 000 souběžných uživatelů, zvažte rozpočtování do aplikace minimálně 1,3 GB paměti serveru (nebo ~ 273 KB na uživatele).

### <a name="signalr-configuration"></a>Konfigurace signálu

Aplikace serveru Blazor používají ke komunikaci s prohlížečem ASP.NET Core signál. [Podmínky hostování a škálování signalizace](xref:signalr/publish-to-azure-web-app) se vztahují na Blazor serverové aplikace.

Blazor funguje nejlépe při použití WebSockets jako přenosu signálu z důvodu nižší latence, spolehlivosti a [zabezpečení](xref:signalr/security). Pokud nejsou objekty WebSockets dostupné nebo když je aplikace explicitně nakonfigurovaná tak, aby používala dlouhé cyklické dotazování, používá se pro ně signalizace dlouhé. Při nasazování do Azure App Service nakonfigurujte aplikaci tak, aby používala objekty WebSocket v nastaveních Azure Portal služby. Podrobnosti o konfiguraci aplikace pro Azure App Service najdete v [pokynech pro publikování signálu](xref:signalr/publish-to-azure-web-app).

Pro aplikace serveru Blazor doporučujeme používat [službu Azure signaler](/azure/azure-signalr) . Služba umožňuje škálovat aplikace serveru Blazor na velký počet souběžných připojení k signalizaci. Kromě toho globální dosah a vysoce výkonná datová centra služby signalizace významně pomáhají při snižování latence kvůli geografickým zeměpisům.

### <a name="measure-network-latency"></a>Měření latence sítě

[Zprostředkovatel komunikace js](xref:blazor/javascript-interop) lze použít k měření latence sítě, jak ukazuje následující příklad:

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

Pro přiměřené prostředí uživatelského rozhraní doporučujeme, abyste trvalou latenci uživatelského rozhraní 250ms nebo méně.
