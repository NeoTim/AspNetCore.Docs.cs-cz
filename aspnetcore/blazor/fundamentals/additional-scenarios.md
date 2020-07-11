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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="e0211-103">ASP.NET Core Blazor Konfigurace modelu hostování</span><span class="sxs-lookup"><span data-stu-id="e0211-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="e0211-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e0211-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e0211-105">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="e0211-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="e0211-106">vyjednávání mezi zdroji pro ověřování</span><span class="sxs-lookup"><span data-stu-id="e0211-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="e0211-107">*Tato část se týká Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="e0211-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="e0211-108">Konfigurace SignalR základního klienta pro odesílání přihlašovacích údajů, jako jsou soubory cookie nebo hlavičky ověřování http:</span><span class="sxs-lookup"><span data-stu-id="e0211-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="e0211-109">Použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> k nastavení <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> u požadavků mezi zdroji [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="e0211-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="e0211-110">Přiřaďte <xref:System.Net.Http.HttpMessageHandler> k <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Možnosti:</span><span class="sxs-lookup"><span data-stu-id="e0211-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="e0211-111">Další informace naleznete v tématu <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="e0211-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="e0211-112">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="e0211-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="e0211-113">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="e0211-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="e0211-114">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="e0211-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="e0211-115">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="e0211-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="e0211-116">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s prvkem `id` z `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="e0211-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="e0211-117">Do šablony stylů aplikace přidejte následující `wwwroot/css/app.css` `wwwroot/css/site.css` :</span><span class="sxs-lookup"><span data-stu-id="e0211-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="e0211-118">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="e0211-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="e0211-119">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="e0211-119">CSS class</span></span>                       | <span data-ttu-id="e0211-120">Označující&hellip;</span><span class="sxs-lookup"><span data-stu-id="e0211-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="e0211-121">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="e0211-121">A lost connection.</span></span> <span data-ttu-id="e0211-122">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="e0211-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="e0211-123">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="e0211-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="e0211-124">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="e0211-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="e0211-125">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="e0211-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="e0211-126">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="e0211-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="e0211-127">Chcete-li se pokusit znovu připojit, zavolejte `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="e0211-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="e0211-128">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="e0211-128">Reconnection rejected.</span></span> <span data-ttu-id="e0211-129">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="e0211-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="e0211-130">K opětovnému načtení aplikace zavolejte `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="e0211-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="e0211-131">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="e0211-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="e0211-132">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="e0211-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="e0211-133">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="e0211-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="e0211-134">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="e0211-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="e0211-135">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="e0211-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="e0211-136">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="e0211-136">Render mode</span></span>

<span data-ttu-id="e0211-137">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="e0211-137">*This section applies to Blazor Server.*</span></span>

Blazor Server<span data-ttu-id="e0211-138">aplikace se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="e0211-138"> apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="e0211-139">Tato stránka je nastavená na `_Host.cshtml` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="e0211-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="e0211-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="e0211-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="e0211-141">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="e0211-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="e0211-142">Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="e0211-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="e0211-143">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="e0211-143">Render mode</span></span> | <span data-ttu-id="e0211-144">Description</span><span class="sxs-lookup"><span data-stu-id="e0211-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="e0211-145">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e0211-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e0211-146">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0211-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="e0211-147">Vykreslí značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e0211-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e0211-148">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="e0211-148">Output from the component isn't included.</span></span> <span data-ttu-id="e0211-149">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0211-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="e0211-150">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="e0211-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="e0211-151">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="e0211-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="e0211-152">Konfigurace SignalR klienta pro Blazor Server aplikace</span><span class="sxs-lookup"><span data-stu-id="e0211-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="e0211-153">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="e0211-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="e0211-154">Nakonfigurujte SignalR klienta používaného Blazor Server aplikacemi v `Pages/_Host.cshtml` souboru.</span><span class="sxs-lookup"><span data-stu-id="e0211-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="e0211-155">Umístěte skript, který volá `Blazor.start` za `_framework/blazor.server.js` skript a dovnitř `</body>` značky.</span><span class="sxs-lookup"><span data-stu-id="e0211-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="e0211-156">Protokolování</span><span class="sxs-lookup"><span data-stu-id="e0211-156">Logging</span></span>

<span data-ttu-id="e0211-157">Konfigurace SignalR protokolování klienta:</span><span class="sxs-lookup"><span data-stu-id="e0211-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="e0211-158">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="e0211-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="e0211-159">Předejte objekt konfigurace ( `configureSignalR` ), který se zavolá na `configureLogging` úrovni protokolu v Tvůrci klienta.</span><span class="sxs-lookup"><span data-stu-id="e0211-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

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

<span data-ttu-id="e0211-160">V předchozím příkladu `information` je ekvivalentem úrovně protokolu <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="e0211-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="e0211-161">Úprava obslužné rutiny opětovného připojení</span><span class="sxs-lookup"><span data-stu-id="e0211-161">Modify the reconnection handler</span></span>

<span data-ttu-id="e0211-162">Události připojení okruhu obslužné rutiny opětovného připojení lze upravit pro vlastní chování, například:</span><span class="sxs-lookup"><span data-stu-id="e0211-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="e0211-163">Upozorní uživatele, pokud bylo připojení vyřazeno.</span><span class="sxs-lookup"><span data-stu-id="e0211-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="e0211-164">K provedení protokolování (z klienta) při připojení okruhu.</span><span class="sxs-lookup"><span data-stu-id="e0211-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="e0211-165">Postup úpravy událostí připojení:</span><span class="sxs-lookup"><span data-stu-id="e0211-165">To modify the connection events:</span></span>

* <span data-ttu-id="e0211-166">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="e0211-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="e0211-167">Zaregistrujte zpětná volání pro změny připojení pro Vyřazená připojení ( `onConnectionDown` ) a zavedená nebo znovu vytvořená připojení ( `onConnectionUp` ).</span><span class="sxs-lookup"><span data-stu-id="e0211-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="e0211-168">**Oba směry** `onConnectionDown` `onConnectionUp`musí být zadáno.</span><span class="sxs-lookup"><span data-stu-id="e0211-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="e0211-169">Upravit počet a interval opakování připojení</span><span class="sxs-lookup"><span data-stu-id="e0211-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="e0211-170">Postup úpravy počtu opakování připojení a intervalu:</span><span class="sxs-lookup"><span data-stu-id="e0211-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="e0211-171">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="e0211-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="e0211-172">Nastavte počet opakování ( `maxRetries` ) a dobu v milisekundách povolenou pro každý pokus o opakování ( `retryIntervalMilliseconds` ).</span><span class="sxs-lookup"><span data-stu-id="e0211-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="e0211-173">Skrýt nebo nahradit zobrazení opětovného připojení</span><span class="sxs-lookup"><span data-stu-id="e0211-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="e0211-174">Skrytí zobrazení opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="e0211-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="e0211-175">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="e0211-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="e0211-176">Nastavte popisovač opětovného připojení `_reconnectionDisplay` na prázdný objekt ( `{}` nebo `new Object()` ).</span><span class="sxs-lookup"><span data-stu-id="e0211-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

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

<span data-ttu-id="e0211-177">Chcete-li nahradit zobrazení opětovného připojení, nastavte `_reconnectionDisplay` v předchozím příkladu na prvek pro zobrazení:</span><span class="sxs-lookup"><span data-stu-id="e0211-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="e0211-178">Zástupný symbol `{ELEMENT ID}` je ID prvku HTML, který se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="e0211-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0211-179">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e0211-179">Additional resources</span></span>

* <xref:fundamentals/logging/index>
