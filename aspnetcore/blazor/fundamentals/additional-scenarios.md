---
title: ASP.NET Core Blazor Konfigurace modelu hostování
author: guardrex
description: Přečtěte si o dalších scénářích ASP.NET Core Blazor konfiguraci modelu hostování.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 870509a3cbbcbea9b1c4804185c49a831af22630
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009632"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="4ff97-103">ASP.NET Core Blazor Konfigurace modelu hostování</span><span class="sxs-lookup"><span data-stu-id="4ff97-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="4ff97-104">Od [Daniel Skořepa](https://github.com/danroth27), [MacKinnon Buck](https://github.com/MackinnonBuck)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4ff97-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4ff97-105">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="4ff97-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="4ff97-106">SignalR vyjednávání mezi zdroji pro ověřování</span><span class="sxs-lookup"><span data-stu-id="4ff97-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="4ff97-107">*Tato část se týká Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="4ff97-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="4ff97-108">Pro konfiguraci SignalR základního klienta pro odesílání přihlašovacích údajů, jako jsou například cookie s nebo HLAVIČKY ověřování http:</span><span class="sxs-lookup"><span data-stu-id="4ff97-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="4ff97-109">Použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> k nastavení <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> u požadavků mezi zdroji [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="4ff97-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="4ff97-110">Přiřaďte <xref:System.Net.Http.HttpMessageHandler> k <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Možnosti:</span><span class="sxs-lookup"><span data-stu-id="4ff97-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="4ff97-111">Další informace naleznete v tématu <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="4ff97-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="4ff97-112">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="4ff97-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="4ff97-113">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="4ff97-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="4ff97-114">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="4ff97-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="4ff97-115">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="4ff97-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="4ff97-116">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s prvkem `id` z `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="4ff97-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="4ff97-117">Do šablony stylů aplikace přidejte následující `wwwroot/css/app.css` `wwwroot/css/site.css` :</span><span class="sxs-lookup"><span data-stu-id="4ff97-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="4ff97-118">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="4ff97-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="4ff97-119">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="4ff97-119">CSS class</span></span>                       | <span data-ttu-id="4ff97-120">Označující&hellip;</span><span class="sxs-lookup"><span data-stu-id="4ff97-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="4ff97-121">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="4ff97-121">A lost connection.</span></span> <span data-ttu-id="4ff97-122">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="4ff97-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="4ff97-123">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="4ff97-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="4ff97-124">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="4ff97-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="4ff97-125">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="4ff97-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="4ff97-126">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="4ff97-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="4ff97-127">Chcete-li se pokusit znovu připojit, zavolejte `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="4ff97-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="4ff97-128">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="4ff97-128">Reconnection rejected.</span></span> <span data-ttu-id="4ff97-129">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="4ff97-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="4ff97-130">K opětovnému načtení aplikace zavolejte `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="4ff97-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="4ff97-131">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="4ff97-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="4ff97-132">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="4ff97-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="4ff97-133">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="4ff97-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="4ff97-134">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="4ff97-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="4ff97-135">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="4ff97-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="4ff97-136">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="4ff97-136">Render mode</span></span>

<span data-ttu-id="4ff97-137">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="4ff97-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="4ff97-138">Blazor Server aplikace se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="4ff97-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="4ff97-139">Tato stránka je nastavená na `_Host.cshtml` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="4ff97-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="4ff97-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="4ff97-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="4ff97-141">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="4ff97-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="4ff97-142">Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="4ff97-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="4ff97-143">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="4ff97-143">Render mode</span></span> | <span data-ttu-id="4ff97-144">Popis</span><span class="sxs-lookup"><span data-stu-id="4ff97-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4ff97-145">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ff97-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4ff97-146">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ff97-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4ff97-147">Vykreslí značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ff97-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4ff97-148">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="4ff97-148">Output from the component isn't included.</span></span> <span data-ttu-id="4ff97-149">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ff97-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4ff97-150">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="4ff97-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="4ff97-151">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="4ff97-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="4ff97-152">Inicializovat Blazor okruh</span><span class="sxs-lookup"><span data-stu-id="4ff97-152">Initialize the Blazor circuit</span></span>

<span data-ttu-id="4ff97-153">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="4ff97-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="4ff97-154">Konfigurace ručního spuštění Blazor Server [ SignalR okruhu](xref:blazor/hosting-models#circuits) aplikace v `Pages/_Host.cshtml` souboru:</span><span class="sxs-lookup"><span data-stu-id="4ff97-154">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="4ff97-155">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="4ff97-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="4ff97-156">Umístěte skript, který volá `Blazor.start` za `blazor.server.js` značku skriptu a uvnitř uzavírací `</body>` značky.</span><span class="sxs-lookup"><span data-stu-id="4ff97-156">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="4ff97-157">Pokud `autostart` je zakázaný, všechny aspekty aplikace, které nezávisí na okruhu, fungují normálně.</span><span class="sxs-lookup"><span data-stu-id="4ff97-157">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="4ff97-158">Například směrování na straně klienta je funkční.</span><span class="sxs-lookup"><span data-stu-id="4ff97-158">For example, client-side routing is operational.</span></span> <span data-ttu-id="4ff97-159">Nicméně jakýkoliv aspekt, který závisí na okruhu, není funkční, dokud `Blazor.start` se nevolá.</span><span class="sxs-lookup"><span data-stu-id="4ff97-159">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="4ff97-160">Chování aplikace je nepředvídatelné bez vytvořeného okruhu.</span><span class="sxs-lookup"><span data-stu-id="4ff97-160">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="4ff97-161">Například metody komponenty se nedaří spustit, pokud je okruh odpojen.</span><span class="sxs-lookup"><span data-stu-id="4ff97-161">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="4ff97-162">Inicializovat Blazor při přípravě dokumentu</span><span class="sxs-lookup"><span data-stu-id="4ff97-162">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="4ff97-163">Inicializace aplikace, Blazor když je dokument připravený:</span><span class="sxs-lookup"><span data-stu-id="4ff97-163">To initialize the Blazor app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="4ff97-164">Řetězení k `Promise` , které je výsledkem ručního spuštění</span><span class="sxs-lookup"><span data-stu-id="4ff97-164">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="4ff97-165">Chcete-li provést další úkoly, jako je například Inicializace zprostředkovatele spolupráce v JS, použijte příkaz `then` k zřetězení s `Promise` výsledkem ručního Blazor spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="4ff97-165">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="4ff97-166">Konfigurace SignalR klienta</span><span class="sxs-lookup"><span data-stu-id="4ff97-166">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="4ff97-167">Protokolování</span><span class="sxs-lookup"><span data-stu-id="4ff97-167">Logging</span></span>

<span data-ttu-id="4ff97-168">Pokud chcete nakonfigurovat SignalR protokolování klienta, předejte do konfiguračního objektu ( `configureSignalR` ), který se zavolá na `configureLogging` úrovni protokolu na tvůrci klienta:</span><span class="sxs-lookup"><span data-stu-id="4ff97-168">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

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

<span data-ttu-id="4ff97-169">V předchozím příkladu `information` je ekvivalentem úrovně protokolu <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4ff97-169">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="4ff97-170">Úprava obslužné rutiny opětovného připojení</span><span class="sxs-lookup"><span data-stu-id="4ff97-170">Modify the reconnection handler</span></span>

<span data-ttu-id="4ff97-171">Události připojení okruhu obslužné rutiny opětovného připojení lze upravit pro vlastní chování, například:</span><span class="sxs-lookup"><span data-stu-id="4ff97-171">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="4ff97-172">Upozorní uživatele, pokud bylo připojení vyřazeno.</span><span class="sxs-lookup"><span data-stu-id="4ff97-172">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="4ff97-173">K provedení protokolování (z klienta) při připojení okruhu.</span><span class="sxs-lookup"><span data-stu-id="4ff97-173">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="4ff97-174">Chcete-li upravit události připojení, zaregistrujte zpětná volání pro následující změny připojení:</span><span class="sxs-lookup"><span data-stu-id="4ff97-174">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="4ff97-175">Byla přerušena použití připojení `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="4ff97-175">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="4ff97-176">Navázalo se opakované použití připojení `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="4ff97-176">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="4ff97-177">**Oba směry** `onConnectionDown` `onConnectionUp` musí být zadány:</span><span class="sxs-lookup"><span data-stu-id="4ff97-177">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="4ff97-178">Upravit počet a interval opakování připojení</span><span class="sxs-lookup"><span data-stu-id="4ff97-178">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="4ff97-179">Pokud chcete upravit počet a interval opakování připojení, nastavte počet opakování ( `maxRetries` ) a dobu v milisekundách povolenou pro každý pokus o opakování ( `retryIntervalMilliseconds` ):</span><span class="sxs-lookup"><span data-stu-id="4ff97-179">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="4ff97-180">Skrýt nebo nahradit zobrazení opětovného připojení</span><span class="sxs-lookup"><span data-stu-id="4ff97-180">Hide or replace the reconnection display</span></span>

<span data-ttu-id="4ff97-181">Chcete-li skrýt zobrazení opětovného připojení, nastavte popisovač opětovného připojení `_reconnectionDisplay` na prázdný objekt ( `{}` nebo `new Object()` ):</span><span class="sxs-lookup"><span data-stu-id="4ff97-181">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="4ff97-182">Chcete-li nahradit zobrazení opětovného připojení, nastavte `_reconnectionDisplay` v předchozím příkladu na prvek pro zobrazení:</span><span class="sxs-lookup"><span data-stu-id="4ff97-182">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="4ff97-183">Zástupný symbol `{ELEMENT ID}` je ID prvku HTML, který se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="4ff97-183">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4ff97-184">Přizpůsobte zpoždění před tím, než se zobrazí zobrazení opětovného připojení nastavením `transition-delay` vlastnosti v CSS ( `wwwroot/css/site.css` ) aplikace pro modální prvek.</span><span class="sxs-lookup"><span data-stu-id="4ff97-184">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="4ff97-185">Následující příklad nastaví zpoždění přechodu z 500 ms (výchozí) na 1 000 ms (1 sekunda):</span><span class="sxs-lookup"><span data-stu-id="4ff97-185">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

::: moniker-end

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="4ff97-186">Ovlivnění `<head>` elementů značek HTML</span><span class="sxs-lookup"><span data-stu-id="4ff97-186">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="4ff97-187">*Tato část se vztahuje na nadcházející ASP.NET Core 5,0 verze Blazor WebAssembly a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="4ff97-187">*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="4ff97-188">Při vykreslení, `Title` komponenty, `Link` a `Meta` přidají nebo aktualizují data v `<head>` prvcích značek HTML:</span><span class="sxs-lookup"><span data-stu-id="4ff97-188">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="4ff97-189">V předchozím příkladu zástupné symboly pro `{TITLE}` , `{URL}` a `{DESCRIPTION}` jsou řetězcové hodnoty, Razor proměnné nebo Razor výrazy.</span><span class="sxs-lookup"><span data-stu-id="4ff97-189">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="4ff97-190">Platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="4ff97-190">The following characteristics apply:</span></span>

* <span data-ttu-id="4ff97-191">Je podporováno předběžné vykreslování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="4ff97-191">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="4ff97-192">`Value`Parametr je jediným platným parametrem pro `Title` komponentu.</span><span class="sxs-lookup"><span data-stu-id="4ff97-192">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="4ff97-193">Atributy HTML, které jsou zadány `Meta` `Link` komponentám a jsou zachyceny v [dalších atributech](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) a předány do vykreslené značky HTML.</span><span class="sxs-lookup"><span data-stu-id="4ff97-193">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="4ff97-194">U více `Title` komponent se v názvu stránky zobrazuje `Value` Poslední `Title` vykreslená komponenta.</span><span class="sxs-lookup"><span data-stu-id="4ff97-194">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="4ff97-195">Je `Meta` -li `Link` pro stejné atributy zahrnuto více komponent nebo, je k dispozici pouze jedna značka HTML vykreslená na jednu `Meta` nebo `Link` komponentu.</span><span class="sxs-lookup"><span data-stu-id="4ff97-195">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="4ff97-196">Dvě `Meta` nebo `Link` komponenty nemůžou odkazovat na stejnou vykreslenou značku HTML.</span><span class="sxs-lookup"><span data-stu-id="4ff97-196">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="4ff97-197">Změny parametrů existujících `Meta` nebo `Link` komponent se projeví ve vykreslených značkách HTML.</span><span class="sxs-lookup"><span data-stu-id="4ff97-197">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="4ff97-198">Když `Link` komponenty nebo `Meta` již nejsou vykreslovány a jsou následně odstraněny rozhraním, jsou odebrány jejich vykreslené značky HTML.</span><span class="sxs-lookup"><span data-stu-id="4ff97-198">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="4ff97-199">Pokud se jedna z komponent rozhraní používá v podřízené komponentě, vykreslená značka HTML ovlivňuje všechny ostatní podřízené komponenty nadřazené komponenty, pokud je vykreslena podřízená komponenta obsahující komponentu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4ff97-199">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="4ff97-200">Rozdíl mezi použitím jedné z těchto komponent rozhraní v podřízené komponentě a umístěním značky HTML do `wwwroot/index.html` nebo `Pages/_Host.cshtml` je vykreslená značka HTML komponenty rozhraní:</span><span class="sxs-lookup"><span data-stu-id="4ff97-200">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="4ff97-201">Může být upraveno stavem aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ff97-201">Can be modified by application state.</span></span> <span data-ttu-id="4ff97-202">Pevně kódovaná značka HTML nemůže být upravena stavem aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ff97-202">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="4ff97-203">Je odebrán z kódu HTML, `<head>` Pokud již není vykreslena nadřazená komponenta.</span><span class="sxs-lookup"><span data-stu-id="4ff97-203">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

## <a name="static-files"></a><span data-ttu-id="4ff97-204">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="4ff97-204">Static files</span></span>

<span data-ttu-id="4ff97-205">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="4ff97-205">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="4ff97-206">Pokud chcete vytvořit další mapování souborů pomocí <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> nebo nakonfigurovat jinou <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , použijte **jeden** z následujících přístupů.</span><span class="sxs-lookup"><span data-stu-id="4ff97-206">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="4ff97-207">V následujících příkladech `{EXTENSION}` je zástupný symbol Přípona souboru a `{CONTENT TYPE}` zástupný symbol je typ obsahu.</span><span class="sxs-lookup"><span data-stu-id="4ff97-207">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="4ff97-208">Nakonfigurujte možnosti prostřednictvím [Injektáže závislosti (di)](xref:blazor/fundamentals/dependency-injection) v `Startup.ConfigureServices` ( `Startup.cs` ) pomocí <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :</span><span class="sxs-lookup"><span data-stu-id="4ff97-208">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="4ff97-209">Vzhledem k tomu, že tento přístup nakonfiguruje stejného poskytovatele souborů používaného pro obsluhu, ujistěte se `blazor.server.js` , že vaše vlastní konfigurace nekoliduje s obsluhou `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="4ff97-209">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="4ff97-210">Neodstraňujte například mapování pro soubory JavaScriptu konfigurací poskytovatele pomocí `provider.Mappings.Remove(".js")` .</span><span class="sxs-lookup"><span data-stu-id="4ff97-210">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="4ff97-211">Použijte dvě volání do <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="4ff97-211">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="4ff97-212">Nakonfigurujte vlastního zprostředkovatele souborů v prvním volání pomocí <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .</span><span class="sxs-lookup"><span data-stu-id="4ff97-212">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="4ff97-213">Druhý middleware slouží `blazor.server.js` k tomu, který používá výchozí konfiguraci statických souborů poskytovanou Blazor rozhraním.</span><span class="sxs-lookup"><span data-stu-id="4ff97-213">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

## <a name="additional-resources"></a><span data-ttu-id="4ff97-214">Další materiály</span><span class="sxs-lookup"><span data-stu-id="4ff97-214">Additional resources</span></span>

* <xref:fundamentals/logging/index>
