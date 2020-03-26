---
title: Konfigurace modelu hostování ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o Blazor konfiguraci modelu hostování, včetně toho, jak integrovat komponenty Razor do aplikací Razor Pages a MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306425"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="dca8c-103">Konfigurace modelu hostování ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="dca8c-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="dca8c-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="dca8c-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="dca8c-105">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="dca8c-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="dca8c-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="dca8c-106">Blazor WebAssembly</span></span>

<span data-ttu-id="dca8c-107">Od verze ASP.NET Core 3,2 Preview 3 podporuje Blazor WebAssembly konfiguraci z:</span><span class="sxs-lookup"><span data-stu-id="dca8c-107">As of the ASP.NET Core 3.2 Preview 3 release, Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="dca8c-108">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="dca8c-108">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="dca8c-109">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="dca8c-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="dca8c-110">V aplikaci hostované v Blazor je [běhové prostředí](xref:fundamentals/environments) stejné jako hodnota serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="dca8c-110">In a Blazor Hosted app, the [runtime environment](xref:fundamentals/environments) is the same as the server app's value.</span></span>

<span data-ttu-id="dca8c-111">Když aplikaci spouštíte místně, prostředí se nastaví na výchozí vývoj.</span><span class="sxs-lookup"><span data-stu-id="dca8c-111">When running the app locally, the environment defaults to Development.</span></span> <span data-ttu-id="dca8c-112">Při publikování aplikace je prostředí standardně v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="dca8c-112">When the app is published, the environment defaults to Production.</span></span> <span data-ttu-id="dca8c-113">Další informace, včetně postupu konfigurace prostředí, najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="dca8c-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

> [!WARNING]
> <span data-ttu-id="dca8c-114">Konfigurace v aplikaci WebAssembly v Blazor je viditelná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="dca8c-114">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="dca8c-115">**Neukládejte tajné klíče aplikace ani přihlašovací údaje v konfiguraci.**</span><span class="sxs-lookup"><span data-stu-id="dca8c-115">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="dca8c-116">Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="dca8c-116">Configuration files are cached for offline use.</span></span> <span data-ttu-id="dca8c-117">S [progresivními webovými aplikacemi (PWAs)](xref:blazor/progressive-web-app)můžete aktualizovat pouze konfigurační soubory při vytváření nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="dca8c-117">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="dca8c-118">Úprava konfiguračních souborů mezi nasazeními nemá žádný vliv z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="dca8c-118">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="dca8c-119">Uživatelé mají verze souborů uložených v mezipaměti, které jsou nadále používány.</span><span class="sxs-lookup"><span data-stu-id="dca8c-119">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="dca8c-120">Soubory *Service-Worker. js* a *Service-Worker-assets. js* aplikace PWA je nutné znovu sestavit při kompilaci, který signalizuje aplikaci na další stránce uživatele online, na kterou se aplikace znovu nasadila.</span><span class="sxs-lookup"><span data-stu-id="dca8c-120">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="dca8c-121">Další informace o tom, jak služba PWAs zpracovává aktualizace na pozadí, najdete v tématu <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="dca8c-121">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="dca8c-122">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="dca8c-122">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="dca8c-123">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="dca8c-123">Reflect the connection state in the UI</span></span>

<span data-ttu-id="dca8c-124">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="dca8c-124">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="dca8c-125">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="dca8c-125">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="dca8c-126">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `id` `components-reconnect-modal` v `<body>` stránky *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="dca8c-126">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="dca8c-127">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="dca8c-127">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="dca8c-128">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="dca8c-128">CSS class</span></span>                       | <span data-ttu-id="dca8c-129">Indikuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="dca8c-129">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="dca8c-130">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="dca8c-130">A lost connection.</span></span> <span data-ttu-id="dca8c-131">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="dca8c-131">The client is attempting to reconnect.</span></span> <span data-ttu-id="dca8c-132">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="dca8c-132">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="dca8c-133">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="dca8c-133">An active connection is re-established to the server.</span></span> <span data-ttu-id="dca8c-134">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="dca8c-134">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="dca8c-135">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="dca8c-135">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="dca8c-136">Chcete-li se pokusit o opětovné připojení, zavolejte `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="dca8c-136">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="dca8c-137">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="dca8c-137">Reconnection rejected.</span></span> <span data-ttu-id="dca8c-138">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="dca8c-138">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="dca8c-139">Chcete-li aplikaci znovu načíst, zavolejte `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="dca8c-139">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="dca8c-140">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="dca8c-140">This connection state may result when:</span></span><ul><li><span data-ttu-id="dca8c-141">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="dca8c-141">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="dca8c-142">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="dca8c-142">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="dca8c-143">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="dca8c-143">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="dca8c-144">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="dca8c-144">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="dca8c-145">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="dca8c-145">Render mode</span></span>

<span data-ttu-id="dca8c-146">Aplikace Blazor serveru se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="dca8c-146">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="dca8c-147">To je nastaveno na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="dca8c-147">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="dca8c-148">`RenderMode` nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="dca8c-148">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="dca8c-149">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="dca8c-149">Is prerendered into the page.</span></span>
* <span data-ttu-id="dca8c-150">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="dca8c-150">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="dca8c-151">Popis</span><span class="sxs-lookup"><span data-stu-id="dca8c-151">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="dca8c-152">Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="dca8c-152">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="dca8c-153">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="dca8c-153">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="dca8c-154">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="dca8c-154">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="dca8c-155">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="dca8c-155">Output from the component isn't included.</span></span> <span data-ttu-id="dca8c-156">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="dca8c-156">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="dca8c-157">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="dca8c-157">Renders the component into static HTML.</span></span> |

<span data-ttu-id="dca8c-158">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="dca8c-158">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="dca8c-159">Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="dca8c-159">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="dca8c-160">Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="dca8c-160">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="dca8c-161">Při vykreslení stránky nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="dca8c-161">When the page or view renders:</span></span>

* <span data-ttu-id="dca8c-162">Komponenta je předem vykreslena se stránkou nebo zobrazením.</span><span class="sxs-lookup"><span data-stu-id="dca8c-162">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="dca8c-163">Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="dca8c-163">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="dca8c-164">Po navázání SignalRho připojení se vytvoří nový stav součásti.</span><span class="sxs-lookup"><span data-stu-id="dca8c-164">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="dca8c-165">Následující stránka Razor vykresluje komponentu `Counter`:</span><span class="sxs-lookup"><span data-stu-id="dca8c-165">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="dca8c-166">Vykreslování neinteraktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="dca8c-166">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="dca8c-167">Na následující stránce Razor je součást `Counter` staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="dca8c-167">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="dca8c-168">Vzhledem k tomu, že `MyComponent` jsou staticky vykresleny, nemůže být komponenta interaktivní.</span><span class="sxs-lookup"><span data-stu-id="dca8c-168">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="dca8c-169">Konfigurace klienta SignalR pro aplikace Blazor serveru</span><span class="sxs-lookup"><span data-stu-id="dca8c-169">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="dca8c-170">V některých případech je třeba nakonfigurovat klienta SignalR používaného serverovými aplikacemi Blazor.</span><span class="sxs-lookup"><span data-stu-id="dca8c-170">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="dca8c-171">Například můžete chtít nakonfigurovat protokolování klienta SignalR pro diagnostiku potíží s připojením.</span><span class="sxs-lookup"><span data-stu-id="dca8c-171">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="dca8c-172">Konfigurace klienta SignalR v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="dca8c-172">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="dca8c-173">Přidejte atribut `autostart="false"` do značky `<script>` pro skript `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="dca8c-173">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="dca8c-174">Zavolejte `Blazor.start` a předejte objekt konfigurace, který určuje SignalR Builder.</span><span class="sxs-lookup"><span data-stu-id="dca8c-174">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
