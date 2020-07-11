---
title: ASP.NET Core Blazor Konfigurace modelu hostování
author: guardrex
description: Přečtěte si o dalších scénářích ASP.NET Core Blazor konfiguraci modelu hostování.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b28e4e43b88fcf8eab9e8959142cca21223c57ff
ms.sourcegitcommit: e216e8f4afa21215dc38124c28d5ee19f5ed7b1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86239631"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor Konfigurace modelu hostování

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Tento článek popisuje konfiguraci modelu hostování.

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalRvyjednávání mezi zdroji pro ověřování

*Tato část se týká Blazor WebAssembly .*

Konfigurace SignalR základního klienta pro odesílání přihlašovacích údajů, jako jsou soubory cookie nebo hlavičky ověřování http:

* Použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> k nastavení <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> u požadavků mezi zdroji [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
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
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Další informace naleznete v tématu <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Odrážet stav připojení v uživatelském rozhraní

*Tato část se týká Blazor Server .*

Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit. Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.

Chcete-li přizpůsobit uživatelské rozhraní, definujte element s prvkem `id` z `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stránce:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Do šablony stylů aplikace přidejte následující `wwwroot/css/app.css` `wwwroot/css/site.css` :

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.

| CSS – třída                       | Označující&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Ztracené připojení. Klient se pokouší znovu připojit. Zobrazit modální okno. |
| `components-reconnect-hide`     | K serveru se znovu naváže aktivní připojení. Skryje modální okno. |
| `components-reconnect-failed`   | Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě. Chcete-li se pokusit znovu připojit, zavolejte `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Opětovné připojení bylo zamítnuto. Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen. K opětovnému načtení aplikace zavolejte `location.reload()` . Tento stav připojení může mít za následek:<ul><li>Dojde k chybě okruhu na straně serveru.</li><li>Klient je dostatečně odpojený, aby server vynechal stav uživatele. Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</li><li>Server se restartuje nebo se pracovní proces aplikace recykluje.</li></ul> |

## <a name="render-mode"></a>Režim vykreslování

*Tato část se týká Blazor Server .*

Blazor Serveraplikace se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru. Tato stránka je nastavená na `_Host.cshtml` Razor stránce:

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

| Režim vykreslování | Description |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor Server aplikaci. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Vykreslí značku pro Blazor Server aplikaci. Výstup komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Vykreslí komponentu do statického HTML. |

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Konfigurace SignalR klienta pro Blazor Server aplikace

*Tato část se týká Blazor Server .*

Nakonfigurujte SignalR klienta používaného Blazor Server aplikacemi v `Pages/_Host.cshtml` souboru. Umístěte skript, který volá `Blazor.start` za `_framework/blazor.server.js` skript a dovnitř `</body>` značky.

### <a name="logging"></a>Protokolování

Konfigurace SignalR protokolování klienta:

* Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.
* Předejte objekt konfigurace ( `configureSignalR` ), který se zavolá na `configureLogging` úrovni protokolu v Tvůrci klienta.

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

V předchozím příkladu `information` je ekvivalentem úrovně protokolu <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .

### <a name="modify-the-reconnection-handler"></a>Úprava obslužné rutiny opětovného připojení

Události připojení okruhu obslužné rutiny opětovného připojení lze upravit pro vlastní chování, například:

* Upozorní uživatele, pokud bylo připojení vyřazeno.
* K provedení protokolování (z klienta) při připojení okruhu.

Postup úpravy událostí připojení:

* Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.
* Zaregistrujte zpětná volání pro změny připojení pro Vyřazená připojení ( `onConnectionDown` ) a zavedená nebo znovu vytvořená připojení ( `onConnectionUp` ). **Oba směry** `onConnectionDown` `onConnectionUp`musí být zadáno.

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Upravit počet a interval opakování připojení

Postup úpravy počtu opakování připojení a intervalu:

* Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.
* Nastavte počet opakování ( `maxRetries` ) a dobu v milisekundách povolenou pro každý pokus o opakování ( `retryIntervalMilliseconds` ).

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

### <a name="hide-or-replace-the-reconnection-display"></a>Skrýt nebo nahradit zobrazení opětovného připojení

Skrytí zobrazení opětovného připojení:

* Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.
* Nastavte popisovač opětovného připojení `_reconnectionDisplay` na prázdný objekt ( `{}` nebo `new Object()` ).

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

Chcete-li nahradit zobrazení opětovného připojení, nastavte `_reconnectionDisplay` v předchozím příkladu na prvek pro zobrazení:

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

Zástupný symbol `{ELEMENT ID}` je ID prvku HTML, který se má zobrazit.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/logging/index>
