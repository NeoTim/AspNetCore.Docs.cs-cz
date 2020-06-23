---
title: ASP.NET Core Blazor Konfigurace modelu hostování
author: guardrex
description: Přečtěte si o dalších scénářích ASP.NET Core Blazor konfiguraci modelu hostování.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 2efc13d5d4ab91ffdf6c4c7021072a2b3f83153f
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242651"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor Konfigurace modelu hostování

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Tento článek popisuje konfiguraci modelu hostování.

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalRvyjednávání mezi zdroji pro ověřování

*Tato část se vztahuje na Blazor WebAssembly.*

Konfigurace SignalR základního klienta pro odesílání přihlašovacích údajů, jako jsou soubory cookie nebo hlavičky ověřování http:

* Použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> k nastavení <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> u požadavků mezi zdroji [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* Přiřaďte <xref:System.Net.Http.HttpMessageHandler> k <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Možnosti:

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Další informace naleznete v tématu <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Odrážet stav připojení v uživatelském rozhraní

*Tato část se týká Blazor serveru.*

Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit. Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.

Chcete-li přizpůsobit uživatelské rozhraní, definujte element s prvkem `id` z `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stránce:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.

| CSS – třída                       | Označující&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Ztracené připojení. Klient se pokouší znovu připojit. Zobrazit modální okno. |
| `components-reconnect-hide`     | K serveru se znovu naváže aktivní připojení. Skryje modální okno. |
| `components-reconnect-failed`   | Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě. Chcete-li se pokusit znovu připojit, zavolejte `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Opětovné připojení bylo zamítnuto. Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen. K opětovnému načtení aplikace zavolejte `location.reload()` . Tento stav připojení může mít za následek:<ul><li>Dojde k chybě okruhu na straně serveru.</li><li>Klient je dostatečně odpojený, aby server vynechal stav uživatele. Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</li><li>Server se restartuje nebo se pracovní proces aplikace recykluje.</li></ul> |

## <a name="render-mode"></a>Režim vykreslování

*Tato část se týká Blazor serveru.*

BlazorServerové aplikace se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru. Tato stránka je nastavená na `_Host.cshtml` Razor stránce:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>nakonfiguruje, jestli součást:

* Je předem vykreslen na stránku.
* Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | Description |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Vykreslí značku pro Blazor serverovou aplikaci. Výstup komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Vykreslí komponentu do statického HTML. |

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Konfigurace SignalR klienta pro Blazor serverové aplikace

*Tato část se týká Blazor serveru.*

V některých případech je potřeba nakonfigurovat SignalR klienta používaného Blazor serverovými aplikacemi. Například můžete chtít nakonfigurovat protokolování na SignalR straně klienta, aby bylo možné diagnostikovat problém s připojením.

Konfigurace SignalR klienta v `Pages/_Host.cshtml` souboru:

* Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.
* Zavolejte `Blazor.start` a předejte do konfiguračního objektu, který určuje SignalR Tvůrce.

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/logging/index>
