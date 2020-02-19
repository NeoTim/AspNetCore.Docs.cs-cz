---
title: Konfigurace modelu hostování ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o Blazor konfiguraci modelu hostování, včetně toho, jak integrovat komponenty Razor do aplikací Razor Pages a MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: bd44643877e45c5b48b0972bcc2f637fbc5d98f2
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447032"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="bb5a2-103">Konfigurace modelu hostování ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="bb5a2-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="bb5a2-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="bb5a2-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="bb5a2-105">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="bb5a2-106">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="bb5a2-106">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="bb5a2-107">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="bb5a2-107">Reflect the connection state in the UI</span></span>

<span data-ttu-id="bb5a2-108">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-108">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="bb5a2-109">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-109">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="bb5a2-110">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `id` `components-reconnect-modal` v `<body>` stránky *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="bb5a2-110">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="bb5a2-111">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-111">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="bb5a2-112">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="bb5a2-112">CSS class</span></span>                       | <span data-ttu-id="bb5a2-113">Indikuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="bb5a2-113">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="bb5a2-114">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-114">A lost connection.</span></span> <span data-ttu-id="bb5a2-115">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-115">The client is attempting to reconnect.</span></span> <span data-ttu-id="bb5a2-116">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-116">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="bb5a2-117">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-117">An active connection is re-established to the server.</span></span> <span data-ttu-id="bb5a2-118">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-118">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="bb5a2-119">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-119">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="bb5a2-120">Chcete-li se pokusit o opětovné připojení, zavolejte `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-120">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="bb5a2-121">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-121">Reconnection rejected.</span></span> <span data-ttu-id="bb5a2-122">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-122">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="bb5a2-123">Chcete-li aplikaci znovu načíst, zavolejte `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-123">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="bb5a2-124">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="bb5a2-124">This connection state may result when:</span></span><ul><li><span data-ttu-id="bb5a2-125">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-125">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="bb5a2-126">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-126">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="bb5a2-127">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-127">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="bb5a2-128">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-128">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="bb5a2-129">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="bb5a2-129">Render mode</span></span>

<span data-ttu-id="bb5a2-130">Aplikace Blazor serveru se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-130">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="bb5a2-131">To je nastaveno na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="bb5a2-131">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="bb5a2-132">`RenderMode` nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="bb5a2-132">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="bb5a2-133">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-133">Is prerendered into the page.</span></span>
* <span data-ttu-id="bb5a2-134">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-134">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="bb5a2-135">Popis</span><span class="sxs-lookup"><span data-stu-id="bb5a2-135">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="bb5a2-136">Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-136">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="bb5a2-137">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="bb5a2-138">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-138">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="bb5a2-139">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-139">Output from the component isn't included.</span></span> <span data-ttu-id="bb5a2-140">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-140">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="bb5a2-141">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-141">Renders the component into static HTML.</span></span> |

<span data-ttu-id="bb5a2-142">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-142">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="bb5a2-143">Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="bb5a2-143">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="bb5a2-144">Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-144">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="bb5a2-145">Při vykreslení stránky nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="bb5a2-145">When the page or view renders:</span></span>

* <span data-ttu-id="bb5a2-146">Komponenta je předem vykreslena se stránkou nebo zobrazením.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-146">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="bb5a2-147">Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-147">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="bb5a2-148">Po navázání SignalRho připojení se vytvoří nový stav součásti.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-148">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="bb5a2-149">Následující stránka Razor vykresluje komponentu `Counter`:</span><span class="sxs-lookup"><span data-stu-id="bb5a2-149">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="bb5a2-150">Vykreslování neinteraktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="bb5a2-150">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="bb5a2-151">Na následující stránce Razor je součást `Counter` staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="bb5a2-151">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="bb5a2-152">Vzhledem k tomu, že `MyComponent` jsou staticky vykresleny, nemůže být komponenta interaktivní.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-152">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="bb5a2-153">Konfigurace klienta SignalR pro aplikace Blazor serveru</span><span class="sxs-lookup"><span data-stu-id="bb5a2-153">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="bb5a2-154">V některých případech je třeba nakonfigurovat klienta SignalR používaného serverovými aplikacemi Blazor.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="bb5a2-155">Například můžete chtít nakonfigurovat protokolování klienta SignalR pro diagnostiku potíží s připojením.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="bb5a2-156">Konfigurace klienta SignalR v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bb5a2-156">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="bb5a2-157">Přidejte atribut `autostart="false"` do značky `<script>` pro skript `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="bb5a2-158">Zavolejte `Blazor.start` a předejte objekt konfigurace, který určuje SignalR Builder.</span><span class="sxs-lookup"><span data-stu-id="bb5a2-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
