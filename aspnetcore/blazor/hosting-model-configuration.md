---
title: konfigurace Blazor modelu ASP.NET hostingu
author: guardrex
description: Přečtěte Blazor si o hostování konfigurace modelu, včetně toho, jak integrovat komponenty Razor do stránek Razor a aplikací MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306425"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="6d8e9-103">ASP.NET konfigurace hostingového modelu Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6d8e9-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="6d8e9-104">Podle [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6d8e9-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6d8e9-105">Tento článek popisuje konfiguraci hostitelského modelu.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="6d8e9-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6d8e9-106">Blazor WebAssembly</span></span>

<span data-ttu-id="6d8e9-107">Od vydání ASP.NET Core 3.2 Preview 3 podporuje Blazor WebAssembly konfiguraci z:</span><span class="sxs-lookup"><span data-stu-id="6d8e9-107">As of the ASP.NET Core 3.2 Preview 3 release, Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="6d8e9-108">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="6d8e9-108">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="6d8e9-109">*wwwroot/appsettings. {PROSTŘEDÍ}.json*</span><span class="sxs-lookup"><span data-stu-id="6d8e9-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="6d8e9-110">V aplikaci Blazor Hosted je [runtime prostředí](xref:fundamentals/environments) stejné jako hodnota serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-110">In a Blazor Hosted app, the [runtime environment](xref:fundamentals/environments) is the same as the server app's value.</span></span>

<span data-ttu-id="6d8e9-111">Při spuštění aplikace místně, prostředí výchozí vývoj.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-111">When running the app locally, the environment defaults to Development.</span></span> <span data-ttu-id="6d8e9-112">Po publikování aplikace prostředí výchozí produkční.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-112">When the app is published, the environment defaults to Production.</span></span> <span data-ttu-id="6d8e9-113">Další informace, včetně konfigurace prostředí, <xref:fundamentals/environments>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="6d8e9-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

> [!WARNING]
> <span data-ttu-id="6d8e9-114">Konfigurace v aplikaci Blazor WebAssembly je viditelná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-114">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="6d8e9-115">**Neuklápejte tajné kódy aplikací nebo přihlašovací údaje v konfiguraci.**</span><span class="sxs-lookup"><span data-stu-id="6d8e9-115">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="6d8e9-116">Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-116">Configuration files are cached for offline use.</span></span> <span data-ttu-id="6d8e9-117">Pomocí [progresivních webových aplikací (PWA)](xref:blazor/progressive-web-app)můžete aktualizovat konfigurační soubory pouze při vytváření nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-117">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="6d8e9-118">Úpravy konfiguračních souborů mezi nasazeními nemají žádný vliv, protože:</span><span class="sxs-lookup"><span data-stu-id="6d8e9-118">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="6d8e9-119">Uživatelé mají uložené verze souborů, které nadále používají, uložené v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-119">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="6d8e9-120">Pwa *service-worker.js* a *service-worker-assets.js* soubory musí být znovu sestaveny na kompilaci, které signalizují aplikaci na další online návštěvu uživatele, že aplikace byla znovu nasazena.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-120">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="6d8e9-121">Další informace o tom, jak pwa <xref:blazor/progressive-web-app#background-updates>zpracovávají aktualizace na pozadí, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="6d8e9-121">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="6d8e9-122">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="6d8e9-122">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="6d8e9-123">Odrážet stav připojení v ui</span><span class="sxs-lookup"><span data-stu-id="6d8e9-123">Reflect the connection state in the UI</span></span>

<span data-ttu-id="6d8e9-124">Když klient zjistí, že připojení bylo ztraceno, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-124">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="6d8e9-125">Pokud se opětovné připojení nezdaří, uživatel i možnost opakovat.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-125">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="6d8e9-126">Chcete-li přizpůsobit ui, definujte `components-reconnect-modal` prvek `<body>` s `id` v *_Host.cshtml* Razor stránky:</span><span class="sxs-lookup"><span data-stu-id="6d8e9-126">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="6d8e9-127">Následující tabulka popisuje třídy CSS `components-reconnect-modal` použité pro prvek.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-127">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="6d8e9-128">Třída CSS</span><span class="sxs-lookup"><span data-stu-id="6d8e9-128">CSS class</span></span>                       | <span data-ttu-id="6d8e9-129">Označuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="6d8e9-129">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="6d8e9-130">Ztracené spojení.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-130">A lost connection.</span></span> <span data-ttu-id="6d8e9-131">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-131">The client is attempting to reconnect.</span></span> <span data-ttu-id="6d8e9-132">Zobrazit modální.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-132">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="6d8e9-133">K serveru je obnoveno aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-133">An active connection is re-established to the server.</span></span> <span data-ttu-id="6d8e9-134">Skryjte modální.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-134">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="6d8e9-135">Opětovné připojení se nezdařilo, pravděpodobně z důvodu selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-135">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="6d8e9-136">Chcete-li se `window.Blazor.reconnect()`pokusit o opětovné připojení, volejte .</span><span class="sxs-lookup"><span data-stu-id="6d8e9-136">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="6d8e9-137">Opětovné připojení bylo odmítnuto.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-137">Reconnection rejected.</span></span> <span data-ttu-id="6d8e9-138">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-138">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="6d8e9-139">Chcete-li aplikaci `location.reload()`znovu načíst, zavolejte .</span><span class="sxs-lookup"><span data-stu-id="6d8e9-139">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="6d8e9-140">Tento stav připojení může vyústit v těchto možnostech:</span><span class="sxs-lookup"><span data-stu-id="6d8e9-140">This connection state may result when:</span></span><ul><li><span data-ttu-id="6d8e9-141">Dojde k chybě v obvodu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-141">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="6d8e9-142">Klient je odpojen dostatečně dlouho na to, aby server přetavit stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-142">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="6d8e9-143">Instance součástí, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-143">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="6d8e9-144">Server je restartován nebo pracovní proces aplikace je recyklován.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-144">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="6d8e9-145">Režim vykreslení</span><span class="sxs-lookup"><span data-stu-id="6d8e9-145">Render mode</span></span>

<span data-ttu-id="6d8e9-146">Aplikace Blazor Server jsou ve výchozím nastavení nastaveny tak, aby předběžně vykreslovaly uI na serveru před navázáním připojení klienta k serveru.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-146">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="6d8e9-147">Toto nastavení je nastaveno na stránce *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="6d8e9-147">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="6d8e9-148">`RenderMode`konfiguruje, zda součást:</span><span class="sxs-lookup"><span data-stu-id="6d8e9-148">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="6d8e9-149">Je prerendered do stránky.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-149">Is prerendered into the page.</span></span>
* <span data-ttu-id="6d8e9-150">Je vykreslen jako statické HTML na stránce, nebo pokud obsahuje potřebné informace k bootstrap aplikace Blazor od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-150">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="6d8e9-151">Popis</span><span class="sxs-lookup"><span data-stu-id="6d8e9-151">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="6d8e9-152">Vykreslí komponentu do statického KÓDU Blazor HTML a obsahuje značku pro serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-152">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="6d8e9-153">Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-153">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="6d8e9-154">Vykreslí značku Blazor pro serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-154">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="6d8e9-155">Výstup z komponenty není zahrnut.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-155">Output from the component isn't included.</span></span> <span data-ttu-id="6d8e9-156">Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-156">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="6d8e9-157">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-157">Renders the component into static HTML.</span></span> |

<span data-ttu-id="6d8e9-158">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-158">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="6d8e9-159">Vykreslení stavových interaktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="6d8e9-159">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="6d8e9-160">Stavové interaktivní komponenty lze přidat na stránku Razor nebo do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-160">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="6d8e9-161">Když se stránka nebo zobrazení vykreslí:</span><span class="sxs-lookup"><span data-stu-id="6d8e9-161">When the page or view renders:</span></span>

* <span data-ttu-id="6d8e9-162">Komponenta je předobrazována pomocí stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-162">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="6d8e9-163">Počáteční stav součásti použitý pro předběžné vykreslování je ztracen.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-163">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="6d8e9-164">Nový stav součásti SignalR je vytvořen při navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-164">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="6d8e9-165">Následující stránka Razor vykreslí komponentu: `Counter`</span><span class="sxs-lookup"><span data-stu-id="6d8e9-165">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="6d8e9-166">Vykreslení neinteraktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="6d8e9-166">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="6d8e9-167">Na následující stránce Razor `Counter` je komponenta staticky vykreslena s počáteční hodnotou, která je určena pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="6d8e9-167">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="6d8e9-168">Vzhledem k tomu, `MyComponent` že je staticky vykreslen, komponenta nemůže být interaktivní.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-168">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="6d8e9-169">Konfigurace SignalR klienta Blazor pro serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="6d8e9-169">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="6d8e9-170">Někdy je třeba nakonfigurovat klienta SignalR používaného serverovými aplikacemi. Blazor</span><span class="sxs-lookup"><span data-stu-id="6d8e9-170">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="6d8e9-171">Můžete například nakonfigurovat protokolování SignalR klienta tak, aby diagnostikoval problém s připojením.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-171">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="6d8e9-172">Postup konfigurace SignalR klienta v souboru *Pages/_Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6d8e9-172">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="6d8e9-173">Přidejte `autostart="false"` atribut `<script>` ke značce skriptu. `blazor.server.js`</span><span class="sxs-lookup"><span data-stu-id="6d8e9-173">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="6d8e9-174">Volání `Blazor.start` a předání v objektu SignalR konfigurace, který určuje tvůrce.</span><span class="sxs-lookup"><span data-stu-id="6d8e9-174">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
