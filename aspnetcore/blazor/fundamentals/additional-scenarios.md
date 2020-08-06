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
ms.openlocfilehash: b32710e515d111b7dd6556f1db55082cd56a82b5
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818999"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="adc2c-103">ASP.NET Core Blazor Konfigurace modelu hostování</span><span class="sxs-lookup"><span data-stu-id="adc2c-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="adc2c-104">Od [Daniel Skořepa](https://github.com/danroth27), [MacKinnon Buck](https://github.com/MackinnonBuck)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="adc2c-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="adc2c-105">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="adc2c-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="adc2c-106">SignalRvyjednávání mezi zdroji pro ověřování</span><span class="sxs-lookup"><span data-stu-id="adc2c-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="adc2c-107">*Tato část se týká Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="adc2c-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="adc2c-108">Konfigurace SignalR základního klienta pro odesílání přihlašovacích údajů, jako jsou soubory cookie nebo hlavičky ověřování http:</span><span class="sxs-lookup"><span data-stu-id="adc2c-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="adc2c-109">Použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> k nastavení <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> u požadavků mezi zdroji [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="adc2c-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="adc2c-110">Přiřaďte <xref:System.Net.Http.HttpMessageHandler> k <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Možnosti:</span><span class="sxs-lookup"><span data-stu-id="adc2c-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="adc2c-111">Další informace naleznete v tématu <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="adc2c-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="adc2c-112">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="adc2c-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="adc2c-113">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="adc2c-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="adc2c-114">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="adc2c-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="adc2c-115">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="adc2c-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="adc2c-116">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s prvkem `id` z `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="adc2c-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="adc2c-117">Do šablony stylů aplikace přidejte následující `wwwroot/css/app.css` `wwwroot/css/site.css` :</span><span class="sxs-lookup"><span data-stu-id="adc2c-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="adc2c-118">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="adc2c-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="adc2c-119">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="adc2c-119">CSS class</span></span>                       | <span data-ttu-id="adc2c-120">Označující&hellip;</span><span class="sxs-lookup"><span data-stu-id="adc2c-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="adc2c-121">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="adc2c-121">A lost connection.</span></span> <span data-ttu-id="adc2c-122">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="adc2c-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="adc2c-123">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="adc2c-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="adc2c-124">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="adc2c-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="adc2c-125">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="adc2c-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="adc2c-126">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="adc2c-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="adc2c-127">Chcete-li se pokusit znovu připojit, zavolejte `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="adc2c-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="adc2c-128">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="adc2c-128">Reconnection rejected.</span></span> <span data-ttu-id="adc2c-129">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="adc2c-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="adc2c-130">K opětovnému načtení aplikace zavolejte `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="adc2c-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="adc2c-131">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="adc2c-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="adc2c-132">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="adc2c-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="adc2c-133">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="adc2c-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="adc2c-134">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="adc2c-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="adc2c-135">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="adc2c-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="adc2c-136">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="adc2c-136">Render mode</span></span>

<span data-ttu-id="adc2c-137">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="adc2c-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="adc2c-138">Blazor Serveraplikace se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="adc2c-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="adc2c-139">Tato stránka je nastavená na `_Host.cshtml` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="adc2c-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="adc2c-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="adc2c-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="adc2c-141">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="adc2c-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="adc2c-142">Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="adc2c-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="adc2c-143">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="adc2c-143">Render mode</span></span> | <span data-ttu-id="adc2c-144">Popis</span><span class="sxs-lookup"><span data-stu-id="adc2c-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="adc2c-145">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="adc2c-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="adc2c-146">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="adc2c-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="adc2c-147">Vykreslí značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="adc2c-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="adc2c-148">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="adc2c-148">Output from the component isn't included.</span></span> <span data-ttu-id="adc2c-149">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="adc2c-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="adc2c-150">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="adc2c-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="adc2c-151">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="adc2c-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a><span data-ttu-id="adc2c-152">Konfigurace SignalR klienta pro Blazor Server aplikace</span><span class="sxs-lookup"><span data-stu-id="adc2c-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="adc2c-153">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="adc2c-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="adc2c-154">Nakonfigurujte SignalR klienta používaného Blazor Server aplikacemi v `Pages/_Host.cshtml` souboru.</span><span class="sxs-lookup"><span data-stu-id="adc2c-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="adc2c-155">Umístěte skript, který volá `Blazor.start` za `_framework/blazor.server.js` skript a dovnitř `</body>` značky.</span><span class="sxs-lookup"><span data-stu-id="adc2c-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="adc2c-156">protokolování</span><span class="sxs-lookup"><span data-stu-id="adc2c-156">Logging</span></span>

<span data-ttu-id="adc2c-157">Konfigurace SignalR protokolování klienta:</span><span class="sxs-lookup"><span data-stu-id="adc2c-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="adc2c-158">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="adc2c-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="adc2c-159">Předejte objekt konfigurace ( `configureSignalR` ), který se zavolá na `configureLogging` úrovni protokolu v Tvůrci klienta.</span><span class="sxs-lookup"><span data-stu-id="adc2c-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="adc2c-160">V předchozím příkladu `information` je ekvivalentem úrovně protokolu <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="adc2c-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="adc2c-161">Úprava obslužné rutiny opětovného připojení</span><span class="sxs-lookup"><span data-stu-id="adc2c-161">Modify the reconnection handler</span></span>

<span data-ttu-id="adc2c-162">Události připojení okruhu obslužné rutiny opětovného připojení lze upravit pro vlastní chování, například:</span><span class="sxs-lookup"><span data-stu-id="adc2c-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="adc2c-163">Upozorní uživatele, pokud bylo připojení vyřazeno.</span><span class="sxs-lookup"><span data-stu-id="adc2c-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="adc2c-164">K provedení protokolování (z klienta) při připojení okruhu.</span><span class="sxs-lookup"><span data-stu-id="adc2c-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="adc2c-165">Postup úpravy událostí připojení:</span><span class="sxs-lookup"><span data-stu-id="adc2c-165">To modify the connection events:</span></span>

* <span data-ttu-id="adc2c-166">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="adc2c-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="adc2c-167">Zaregistrujte zpětná volání pro změny připojení pro Vyřazená připojení ( `onConnectionDown` ) a zavedená nebo znovu vytvořená připojení ( `onConnectionUp` ).</span><span class="sxs-lookup"><span data-stu-id="adc2c-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="adc2c-168">**Oba směry** `onConnectionDown` `onConnectionUp`musí být zadáno.</span><span class="sxs-lookup"><span data-stu-id="adc2c-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="adc2c-169">Upravit počet a interval opakování připojení</span><span class="sxs-lookup"><span data-stu-id="adc2c-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="adc2c-170">Postup úpravy počtu opakování připojení a intervalu:</span><span class="sxs-lookup"><span data-stu-id="adc2c-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="adc2c-171">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="adc2c-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="adc2c-172">Nastavte počet opakování ( `maxRetries` ) a dobu v milisekundách povolenou pro každý pokus o opakování ( `retryIntervalMilliseconds` ).</span><span class="sxs-lookup"><span data-stu-id="adc2c-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="adc2c-173">Skrýt nebo nahradit zobrazení opětovného připojení</span><span class="sxs-lookup"><span data-stu-id="adc2c-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="adc2c-174">Skrytí zobrazení opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="adc2c-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="adc2c-175">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="adc2c-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="adc2c-176">Nastavte popisovač opětovného připojení `_reconnectionDisplay` na prázdný objekt ( `{}` nebo `new Object()` ).</span><span class="sxs-lookup"><span data-stu-id="adc2c-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

<span data-ttu-id="adc2c-177">Chcete-li nahradit zobrazení opětovného připojení, nastavte `_reconnectionDisplay` v předchozím příkladu na prvek pro zobrazení:</span><span class="sxs-lookup"><span data-stu-id="adc2c-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="adc2c-178">Zástupný symbol `{ELEMENT ID}` je ID prvku HTML, který se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="adc2c-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="adc2c-179">Ovlivnění `<head>` elementů značek HTML</span><span class="sxs-lookup"><span data-stu-id="adc2c-179">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="adc2c-180">*Tato část se týká Blazor WebAssembly a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="adc2c-180">*This section applies to Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="adc2c-181">Při vykreslení, `Title` komponenty, `Link` a `Meta` přidají nebo aktualizují data v `<head>` prvcích značek HTML:</span><span class="sxs-lookup"><span data-stu-id="adc2c-181">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="adc2c-182">V předchozím příkladu zástupné symboly pro `{TITLE}` , `{URL}` a `{DESCRIPTION}` jsou řetězcové hodnoty, Razor proměnné nebo Razor výrazy.</span><span class="sxs-lookup"><span data-stu-id="adc2c-182">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="adc2c-183">Platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="adc2c-183">The following characteristics apply:</span></span>

* <span data-ttu-id="adc2c-184">Je podporováno předběžné vykreslování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="adc2c-184">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="adc2c-185">`Value`Parametr je jediným platným parametrem pro `Title` komponentu.</span><span class="sxs-lookup"><span data-stu-id="adc2c-185">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="adc2c-186">Atributy HTML, které jsou zadány `Meta` `Link` komponentám a jsou zachyceny v [dalších atributech](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) a předány do vykreslené značky HTML.</span><span class="sxs-lookup"><span data-stu-id="adc2c-186">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="adc2c-187">U více `Title` komponent se v názvu stránky zobrazuje `Value` Poslední `Title` vykreslená komponenta.</span><span class="sxs-lookup"><span data-stu-id="adc2c-187">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="adc2c-188">Je `Meta` -li `Link` pro stejné atributy zahrnuto více komponent nebo, je k dispozici pouze jedna značka HTML vykreslená na jednu `Meta` nebo `Link` komponentu.</span><span class="sxs-lookup"><span data-stu-id="adc2c-188">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="adc2c-189">Dvě `Meta` nebo `Link` komponenty nemůžou odkazovat na stejnou vykreslenou značku HTML.</span><span class="sxs-lookup"><span data-stu-id="adc2c-189">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="adc2c-190">Změny parametrů existujících `Meta` nebo `Link` komponent se projeví ve vykreslených značkách HTML.</span><span class="sxs-lookup"><span data-stu-id="adc2c-190">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="adc2c-191">Když `Link` komponenty nebo `Meta` již nejsou vykreslovány a jsou následně odstraněny rozhraním, jsou odebrány jejich vykreslené značky HTML.</span><span class="sxs-lookup"><span data-stu-id="adc2c-191">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="adc2c-192">Pokud se jedna z komponent rozhraní používá v podřízené komponentě, vykreslená značka HTML ovlivňuje všechny ostatní podřízené komponenty nadřazené komponenty, pokud je vykreslena podřízená komponenta obsahující komponentu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="adc2c-192">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="adc2c-193">Rozdíl mezi použitím jedné z těchto komponent rozhraní v podřízené komponentě a umístěním značky HTML do `wwwroot/index.html` nebo `Pages/_Host.cshtml` je vykreslená značka HTML komponenty rozhraní:</span><span class="sxs-lookup"><span data-stu-id="adc2c-193">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="adc2c-194">Může být upraveno stavem aplikace.</span><span class="sxs-lookup"><span data-stu-id="adc2c-194">Can be modified by application state.</span></span> <span data-ttu-id="adc2c-195">Pevně kódovaná značka HTML nemůže být upravena stavem aplikace.</span><span class="sxs-lookup"><span data-stu-id="adc2c-195">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="adc2c-196">Je odebrán z kódu HTML, `<head>` Pokud již není vykreslena nadřazená komponenta.</span><span class="sxs-lookup"><span data-stu-id="adc2c-196">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="adc2c-197">Další materiály</span><span class="sxs-lookup"><span data-stu-id="adc2c-197">Additional resources</span></span>

* <xref:fundamentals/logging/index>
