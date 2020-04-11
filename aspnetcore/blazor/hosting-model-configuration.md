---
title: konfigurace Blazor modelu ASP.NET hostingu
author: guardrex
description: Přečtěte Blazor si o hostování konfigurace modelu, včetně toho, jak integrovat komponenty Razor do stránek Razor a aplikací MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: ca1b3ea9092640ca561b3fbe02ddce6f974c525e
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123377"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="3044d-103">ASP.NET konfigurace hostingového modelu Core Blazor</span><span class="sxs-lookup"><span data-stu-id="3044d-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="3044d-104">Podle [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3044d-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="3044d-105">Tento článek popisuje konfiguraci hostitelského modelu.</span><span class="sxs-lookup"><span data-stu-id="3044d-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="3044d-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3044d-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="3044d-107">Prostředí</span><span class="sxs-lookup"><span data-stu-id="3044d-107">Environment</span></span>

<span data-ttu-id="3044d-108">Při spuštění aplikace místně, prostředí výchozí vývoj.</span><span class="sxs-lookup"><span data-stu-id="3044d-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="3044d-109">Po publikování aplikace prostředí výchozí produkční.</span><span class="sxs-lookup"><span data-stu-id="3044d-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="3044d-110">Hostovaná aplikace Blazor WebAssembly snímá prostředí ze serveru prostřednictvím middlewaru, který `blazor-environment` komunikuje prostředí s prohlížečem přidáním záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3044d-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="3044d-111">Hodnota záhlaví je prostředí.</span><span class="sxs-lookup"><span data-stu-id="3044d-111">The value of the header is the environment.</span></span> <span data-ttu-id="3044d-112">Hostovaná aplikace Blazor a serverová aplikace sdílejí stejné prostředí.</span><span class="sxs-lookup"><span data-stu-id="3044d-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="3044d-113">Další informace, včetně konfigurace prostředí, <xref:fundamentals/environments>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="3044d-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3044d-114">Pro samostatnou aplikaci spuštěnou místně přidá `blazor-environment` vývojový server záhlaví, které určuje vývojové prostředí.</span><span class="sxs-lookup"><span data-stu-id="3044d-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="3044d-115">Chcete-li určit prostředí pro jiná `blazor-environment` hostitelská prostředí, přidejte záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3044d-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="3044d-116">V následujícím příkladu služby IIS přidejte vlastní záhlaví do publikovaného souboru *web.config.*</span><span class="sxs-lookup"><span data-stu-id="3044d-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="3044d-117">Soubor *web.config* je umístěn ve složce *bin/Release/{TARGET FRAMEWORK}/publish:*</span><span class="sxs-lookup"><span data-stu-id="3044d-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="3044d-118">Informace o použití vlastního souboru *web.config* pro službu IIS, který *publish* není při <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>publikování aplikace do složky publikování přepsán, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="3044d-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="3044d-119">Získejte prostředí aplikace v komponentě `IWebAssemblyHostEnvironment` vložením `Environment` a přečtením vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3044d-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

### <a name="configuration"></a><span data-ttu-id="3044d-120">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="3044d-120">Configuration</span></span>

<span data-ttu-id="3044d-121">Od vydání ASP.NET Core 3.2 Preview 3 podporuje Blazor WebAssembly konfiguraci z:</span><span class="sxs-lookup"><span data-stu-id="3044d-121">As of the ASP.NET Core 3.2 Preview 3 release, Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="3044d-122">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="3044d-122">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="3044d-123">*wwwroot/appsettings. {PROSTŘEDÍ}.json*</span><span class="sxs-lookup"><span data-stu-id="3044d-123">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="3044d-124">Přidejte soubor *appsettings.json* do složky *wwwroot:*</span><span class="sxs-lookup"><span data-stu-id="3044d-124">Add an *appsettings.json* file in the *wwwroot* folder:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="3044d-125">Vstříkněte <xref:Microsoft.Extensions.Configuration.IConfiguration> instanci do komponenty pro přístup k konfiguračním datům:</span><span class="sxs-lookup"><span data-stu-id="3044d-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

> [!WARNING]
> <span data-ttu-id="3044d-126">Konfigurace v aplikaci Blazor WebAssembly je viditelná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="3044d-126">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="3044d-127">**Neuklápejte tajné kódy aplikací nebo přihlašovací údaje v konfiguraci.**</span><span class="sxs-lookup"><span data-stu-id="3044d-127">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="3044d-128">Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="3044d-128">Configuration files are cached for offline use.</span></span> <span data-ttu-id="3044d-129">Pomocí [progresivních webových aplikací (PWA)](xref:blazor/progressive-web-app)můžete aktualizovat konfigurační soubory pouze při vytváření nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="3044d-129">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="3044d-130">Úpravy konfiguračních souborů mezi nasazeními nemají žádný vliv, protože:</span><span class="sxs-lookup"><span data-stu-id="3044d-130">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="3044d-131">Uživatelé mají uložené verze souborů, které nadále používají, uložené v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="3044d-131">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="3044d-132">Pwa *service-worker.js* a *service-worker-assets.js* soubory musí být znovu sestaveny na kompilaci, které signalizují aplikaci na další online návštěvu uživatele, že aplikace byla znovu nasazena.</span><span class="sxs-lookup"><span data-stu-id="3044d-132">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="3044d-133">Další informace o tom, jak pwa <xref:blazor/progressive-web-app#background-updates>zpracovávají aktualizace na pozadí, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="3044d-133">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="3044d-134">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="3044d-134">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="3044d-135">Odrážet stav připojení v ui</span><span class="sxs-lookup"><span data-stu-id="3044d-135">Reflect the connection state in the UI</span></span>

<span data-ttu-id="3044d-136">Když klient zjistí, že připojení bylo ztraceno, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="3044d-136">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="3044d-137">Pokud se opětovné připojení nezdaří, uživatel i možnost opakovat.</span><span class="sxs-lookup"><span data-stu-id="3044d-137">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="3044d-138">Chcete-li přizpůsobit ui, definujte `components-reconnect-modal` prvek `<body>` s `id` v *_Host.cshtml* Razor stránky:</span><span class="sxs-lookup"><span data-stu-id="3044d-138">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="3044d-139">Následující tabulka popisuje třídy CSS `components-reconnect-modal` použité pro prvek.</span><span class="sxs-lookup"><span data-stu-id="3044d-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="3044d-140">Třída CSS</span><span class="sxs-lookup"><span data-stu-id="3044d-140">CSS class</span></span>                       | <span data-ttu-id="3044d-141">Označuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="3044d-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="3044d-142">Ztracené spojení.</span><span class="sxs-lookup"><span data-stu-id="3044d-142">A lost connection.</span></span> <span data-ttu-id="3044d-143">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="3044d-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="3044d-144">Zobrazit modální.</span><span class="sxs-lookup"><span data-stu-id="3044d-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="3044d-145">K serveru je obnoveno aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="3044d-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="3044d-146">Skryjte modální.</span><span class="sxs-lookup"><span data-stu-id="3044d-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="3044d-147">Opětovné připojení se nezdařilo, pravděpodobně z důvodu selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="3044d-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="3044d-148">Chcete-li se `window.Blazor.reconnect()`pokusit o opětovné připojení, volejte .</span><span class="sxs-lookup"><span data-stu-id="3044d-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="3044d-149">Opětovné připojení bylo odmítnuto.</span><span class="sxs-lookup"><span data-stu-id="3044d-149">Reconnection rejected.</span></span> <span data-ttu-id="3044d-150">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="3044d-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="3044d-151">Chcete-li aplikaci `location.reload()`znovu načíst, zavolejte .</span><span class="sxs-lookup"><span data-stu-id="3044d-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="3044d-152">Tento stav připojení může vyústit v těchto možnostech:</span><span class="sxs-lookup"><span data-stu-id="3044d-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="3044d-153">Dojde k chybě v obvodu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="3044d-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="3044d-154">Klient je odpojen dostatečně dlouho na to, aby server přetavit stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="3044d-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="3044d-155">Instance součástí, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="3044d-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="3044d-156">Server je restartován nebo pracovní proces aplikace je recyklován.</span><span class="sxs-lookup"><span data-stu-id="3044d-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="3044d-157">Režim vykreslení</span><span class="sxs-lookup"><span data-stu-id="3044d-157">Render mode</span></span>

<span data-ttu-id="3044d-158">Aplikace Blazor Server jsou ve výchozím nastavení nastaveny tak, aby předběžně vykreslovaly uI na serveru před navázáním připojení klienta k serveru.</span><span class="sxs-lookup"><span data-stu-id="3044d-158">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="3044d-159">Toto nastavení je nastaveno na stránce *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="3044d-159">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="3044d-160">`RenderMode`konfiguruje, zda součást:</span><span class="sxs-lookup"><span data-stu-id="3044d-160">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="3044d-161">Je prerendered do stránky.</span><span class="sxs-lookup"><span data-stu-id="3044d-161">Is prerendered into the page.</span></span>
* <span data-ttu-id="3044d-162">Je vykreslen jako statické HTML na stránce, nebo pokud obsahuje potřebné informace k bootstrap aplikace Blazor od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="3044d-162">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="3044d-163">Popis</span><span class="sxs-lookup"><span data-stu-id="3044d-163">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="3044d-164">Vykreslí komponentu do statického KÓDU Blazor HTML a obsahuje značku pro serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3044d-164">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3044d-165">Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3044d-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="3044d-166">Vykreslí značku Blazor pro serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3044d-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3044d-167">Výstup z komponenty není zahrnut.</span><span class="sxs-lookup"><span data-stu-id="3044d-167">Output from the component isn't included.</span></span> <span data-ttu-id="3044d-168">Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3044d-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="3044d-169">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="3044d-169">Renders the component into static HTML.</span></span> |

<span data-ttu-id="3044d-170">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="3044d-170">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="3044d-171">Vykreslení stavových interaktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="3044d-171">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="3044d-172">Stavové interaktivní komponenty lze přidat na stránku Razor nebo do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3044d-172">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="3044d-173">Když se stránka nebo zobrazení vykreslí:</span><span class="sxs-lookup"><span data-stu-id="3044d-173">When the page or view renders:</span></span>

* <span data-ttu-id="3044d-174">Komponenta je předobrazována pomocí stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3044d-174">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="3044d-175">Počáteční stav součásti použitý pro předběžné vykreslování je ztracen.</span><span class="sxs-lookup"><span data-stu-id="3044d-175">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="3044d-176">Nový stav součásti SignalR je vytvořen při navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="3044d-176">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="3044d-177">Následující stránka Razor vykreslí komponentu: `Counter`</span><span class="sxs-lookup"><span data-stu-id="3044d-177">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="3044d-178">Vykreslení neinteraktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="3044d-178">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="3044d-179">Na následující stránce Razor `Counter` je komponenta staticky vykreslena s počáteční hodnotou, která je určena pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="3044d-179">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="3044d-180">Vzhledem k tomu, `MyComponent` že je staticky vykreslen, komponenta nemůže být interaktivní.</span><span class="sxs-lookup"><span data-stu-id="3044d-180">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="3044d-181">Konfigurace SignalR klienta Blazor pro serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="3044d-181">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="3044d-182">Někdy je třeba nakonfigurovat klienta SignalR používaného serverovými aplikacemi. Blazor</span><span class="sxs-lookup"><span data-stu-id="3044d-182">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="3044d-183">Můžete například nakonfigurovat protokolování SignalR klienta tak, aby diagnostikoval problém s připojením.</span><span class="sxs-lookup"><span data-stu-id="3044d-183">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="3044d-184">Postup konfigurace SignalR klienta v souboru *Pages/_Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="3044d-184">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="3044d-185">Přidejte `autostart="false"` atribut `<script>` ke značce skriptu. `blazor.server.js`</span><span class="sxs-lookup"><span data-stu-id="3044d-185">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="3044d-186">Volání `Blazor.start` a předání v objektu SignalR konfigurace, který určuje tvůrce.</span><span class="sxs-lookup"><span data-stu-id="3044d-186">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
