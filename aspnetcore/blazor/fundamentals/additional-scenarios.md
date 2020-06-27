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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 236dffd829bcd7c30ae1145242ce07cd8e9857e6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402946"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="f40a4-103">ASP.NET Core Blazor Konfigurace modelu hostování</span><span class="sxs-lookup"><span data-stu-id="f40a4-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="f40a4-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f40a4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f40a4-105">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="f40a4-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="f40a4-106">vyjednávání mezi zdroji pro ověřování</span><span class="sxs-lookup"><span data-stu-id="f40a4-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="f40a4-107">*Tato část se týká Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="f40a4-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="f40a4-108">Konfigurace SignalR základního klienta pro odesílání přihlašovacích údajů, jako jsou soubory cookie nebo hlavičky ověřování http:</span><span class="sxs-lookup"><span data-stu-id="f40a4-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="f40a4-109">Použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> k nastavení <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> u požadavků mezi zdroji [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="f40a4-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="f40a4-110">Přiřaďte <xref:System.Net.Http.HttpMessageHandler> k <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Možnosti:</span><span class="sxs-lookup"><span data-stu-id="f40a4-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="f40a4-111">Další informace naleznete v tématu <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="f40a4-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="f40a4-112">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="f40a4-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="f40a4-113">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="f40a4-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="f40a4-114">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="f40a4-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="f40a4-115">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="f40a4-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="f40a4-116">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s prvkem `id` z `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="f40a4-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="f40a4-117">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="f40a4-117">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="f40a4-118">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="f40a4-118">CSS class</span></span>                       | <span data-ttu-id="f40a4-119">Označující&hellip;</span><span class="sxs-lookup"><span data-stu-id="f40a4-119">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="f40a4-120">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="f40a4-120">A lost connection.</span></span> <span data-ttu-id="f40a4-121">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="f40a4-121">The client is attempting to reconnect.</span></span> <span data-ttu-id="f40a4-122">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="f40a4-122">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="f40a4-123">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="f40a4-123">An active connection is re-established to the server.</span></span> <span data-ttu-id="f40a4-124">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="f40a4-124">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="f40a4-125">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="f40a4-125">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="f40a4-126">Chcete-li se pokusit znovu připojit, zavolejte `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="f40a4-126">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="f40a4-127">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="f40a4-127">Reconnection rejected.</span></span> <span data-ttu-id="f40a4-128">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="f40a4-128">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="f40a4-129">K opětovnému načtení aplikace zavolejte `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="f40a4-129">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="f40a4-130">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="f40a4-130">This connection state may result when:</span></span><ul><li><span data-ttu-id="f40a4-131">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="f40a4-131">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="f40a4-132">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="f40a4-132">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="f40a4-133">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="f40a4-133">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="f40a4-134">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="f40a4-134">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="f40a4-135">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="f40a4-135">Render mode</span></span>

<span data-ttu-id="f40a4-136">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="f40a4-136">*This section applies to Blazor Server.*</span></span>

Blazor Server<span data-ttu-id="f40a4-137">aplikace se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="f40a4-137"> apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="f40a4-138">Tato stránka je nastavená na `_Host.cshtml` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="f40a4-138">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="f40a4-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="f40a4-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="f40a4-140">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="f40a4-140">Is prerendered into the page.</span></span>
* <span data-ttu-id="f40a4-141">Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="f40a4-141">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="f40a4-142">Description</span><span class="sxs-lookup"><span data-stu-id="f40a4-142">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="f40a4-143">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f40a4-143">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f40a4-144">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="f40a4-144">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="f40a4-145">Vykreslí značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f40a4-145">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f40a4-146">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="f40a4-146">Output from the component isn't included.</span></span> <span data-ttu-id="f40a4-147">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="f40a4-147">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="f40a4-148">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="f40a4-148">Renders the component into static HTML.</span></span> |

<span data-ttu-id="f40a4-149">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="f40a4-149">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="f40a4-150">Konfigurace SignalR klienta pro Blazor Server aplikace</span><span class="sxs-lookup"><span data-stu-id="f40a4-150">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="f40a4-151">*Tato část se týká Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="f40a4-151">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="f40a4-152">V některých případech je potřeba nakonfigurovat SignalR klienta používaného Blazor Server aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="f40a4-152">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="f40a4-153">Například můžete chtít nakonfigurovat protokolování na SignalR straně klienta, aby bylo možné diagnostikovat problém s připojením.</span><span class="sxs-lookup"><span data-stu-id="f40a4-153">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="f40a4-154">Konfigurace SignalR klienta v `Pages/_Host.cshtml` souboru:</span><span class="sxs-lookup"><span data-stu-id="f40a4-154">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="f40a4-155">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="f40a4-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="f40a4-156">Zavolejte `Blazor.start` a předejte do konfiguračního objektu, který určuje SignalR Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="f40a4-156">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f40a4-157">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f40a4-157">Additional resources</span></span>

* <xref:fundamentals/logging/index>