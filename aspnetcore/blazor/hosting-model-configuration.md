---
title: Konfigurace modelu hostování ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o Blazor konfiguraci modelu hostování, včetně toho, jak integrovat komponenty Razor do aplikací Razor Pages a MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 91dd1aa32bb5165845eb4794377a50ccbd01adc3
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77215058"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="52c19-103">Konfigurace modelu hostování ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="52c19-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="52c19-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="52c19-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="52c19-105">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="52c19-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="52c19-106">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="52c19-106">Blazor Server</span></span>

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="52c19-107">Integrace součástí Razor do aplikací Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="52c19-107">Integrate Razor components into Razor Pages and MVC apps</span></span>

#### <a name="use-components-in-pages-and-views"></a><span data-ttu-id="52c19-108">Použití součástí na stránkách a pohledech</span><span class="sxs-lookup"><span data-stu-id="52c19-108">Use components in pages and views</span></span>

<span data-ttu-id="52c19-109">Existující Razor Pages nebo aplikace MVC mohou integrovat součásti Razor do stránek a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="52c19-109">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="52c19-110">V souboru rozložení aplikace ( *_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="52c19-110">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="52c19-111">Přidejte následující značku `<base>` do prvku `<head>`:</span><span class="sxs-lookup"><span data-stu-id="52c19-111">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="52c19-112">Hodnota `href` ( *základní cesta aplikace*) v předchozím příkladu předpokládá, že se aplikace nachází v kořenové cestě URL (`/`).</span><span class="sxs-lookup"><span data-stu-id="52c19-112">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="52c19-113">Pokud je aplikace podaplikace, postupujte podle pokynů v části *základní cesta k aplikaci* v <xref:host-and-deploy/blazor/index#app-base-path> článku.</span><span class="sxs-lookup"><span data-stu-id="52c19-113">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="52c19-114">Soubor *_Layout. cshtml* se nachází ve složce *stránky nebo sdílené* složky v aplikaci Razor Pages nebo *zobrazení/sdílená* složka v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="52c19-114">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="52c19-115">Přidejte značku `<script>` pro skript *blazor. Server. js* přímo před uzavírací značku `</body>`:</span><span class="sxs-lookup"><span data-stu-id="52c19-115">Add a `<script>` tag for the *blazor.server.js* script right before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="52c19-116">Rozhraní přidá do aplikace skript *blazor. Server. js* .</span><span class="sxs-lookup"><span data-stu-id="52c19-116">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="52c19-117">Nemusíte ručně přidávat do aplikace skript.</span><span class="sxs-lookup"><span data-stu-id="52c19-117">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="52c19-118">Přidejte soubor *_Imports. Razor* do kořenové složky projektu s následujícím obsahem (změňte poslední obor názvů `MyAppNamespace`na obor názvů aplikace):</span><span class="sxs-lookup"><span data-stu-id="52c19-118">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="52c19-119">V `Startup.ConfigureServices`Zaregistrujte službu Blazor serveru:</span><span class="sxs-lookup"><span data-stu-id="52c19-119">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="52c19-120">V `Startup.Configure`přidejte koncový bod centra Blazor do `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="52c19-120">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="52c19-121">Integrujte součásti na libovolnou stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="52c19-121">Integrate components into any page or view.</span></span> <span data-ttu-id="52c19-122">Další informace najdete v části *integrace součástí do Razor Pages a MVC Apps* v článku <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="52c19-122">For more information, see the *Integrate components into Razor Pages and MVC apps* section of the <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> article.</span></span>

#### <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="52c19-123">Použití směrovatelných komponent v aplikaci Razor Pages</span><span class="sxs-lookup"><span data-stu-id="52c19-123">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="52c19-124">Podpora směrování komponent s více prvky Razor v aplikacích Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="52c19-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="52c19-125">Postupujte podle pokynů v části [použití součástí v části stránky a zobrazení](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="52c19-125">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="52c19-126">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="52c19-126">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="52c19-127">Do složky *Pages* přidejte *_Host soubor. cshtml* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="52c19-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="52c19-128">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="52c19-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="52c19-129">Přidejte trasu s nízkou prioritou pro stránku *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="52c19-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="52c19-130">Přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="52c19-130">Add routable components to the app.</span></span> <span data-ttu-id="52c19-131">Příklad:</span><span class="sxs-lookup"><span data-stu-id="52c19-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="52c19-132">Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do souboru *Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="52c19-132">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Pages/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="52c19-133">Další informace najdete v tématu <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="52c19-133">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

#### <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="52c19-134">Použití směrovatelných komponent v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="52c19-134">Use routable components in an MVC app</span></span>

<span data-ttu-id="52c19-135">Podpora směrovatelných komponent Razor v aplikacích MVC:</span><span class="sxs-lookup"><span data-stu-id="52c19-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="52c19-136">Postupujte podle pokynů v části [použití součástí v části stránky a zobrazení](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="52c19-136">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="52c19-137">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="52c19-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="52c19-138">Přidejte *_Host soubor. cshtml* do *zobrazení/domovské* složky s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="52c19-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="52c19-139">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="52c19-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="52c19-140">Přidat akci do domovského kontroleru:</span><span class="sxs-lookup"><span data-stu-id="52c19-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="52c19-141">Přidejte trasu s nízkou prioritou pro akci kontroleru, která vrací zobrazení *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="52c19-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="52c19-142">Vytvořte složku *Pages* a přidejte do ní součásti s funkcí směrování.</span><span class="sxs-lookup"><span data-stu-id="52c19-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="52c19-143">Příklad:</span><span class="sxs-lookup"><span data-stu-id="52c19-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="52c19-144">Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do souboru *views/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="52c19-144">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="52c19-145">Další informace najdete v tématu <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="52c19-145">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="52c19-146">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="52c19-146">Reflect the connection state in the UI</span></span>

<span data-ttu-id="52c19-147">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="52c19-147">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="52c19-148">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="52c19-148">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="52c19-149">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `id` `components-reconnect-modal` v `<body>` stránky *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="52c19-149">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="52c19-150">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="52c19-150">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="52c19-151">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="52c19-151">CSS class</span></span>                       | <span data-ttu-id="52c19-152">Indikuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="52c19-152">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="52c19-153">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="52c19-153">A lost connection.</span></span> <span data-ttu-id="52c19-154">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="52c19-154">The client is attempting to reconnect.</span></span> <span data-ttu-id="52c19-155">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="52c19-155">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="52c19-156">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="52c19-156">An active connection is re-established to the server.</span></span> <span data-ttu-id="52c19-157">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="52c19-157">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="52c19-158">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="52c19-158">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="52c19-159">Chcete-li se pokusit o opětovné připojení, zavolejte `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="52c19-159">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="52c19-160">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="52c19-160">Reconnection rejected.</span></span> <span data-ttu-id="52c19-161">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="52c19-161">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="52c19-162">Chcete-li aplikaci znovu načíst, zavolejte `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="52c19-162">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="52c19-163">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="52c19-163">This connection state may result when:</span></span><ul><li><span data-ttu-id="52c19-164">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="52c19-164">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="52c19-165">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="52c19-165">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="52c19-166">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="52c19-166">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="52c19-167">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="52c19-167">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="52c19-168">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="52c19-168">Render mode</span></span>

<span data-ttu-id="52c19-169">Aplikace Blazor serveru se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="52c19-169">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="52c19-170">To je nastaveno na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="52c19-170">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="52c19-171">`RenderMode` nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="52c19-171">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="52c19-172">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="52c19-172">Is prerendered into the page.</span></span>
* <span data-ttu-id="52c19-173">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="52c19-173">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="52c19-174">Popis</span><span class="sxs-lookup"><span data-stu-id="52c19-174">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="52c19-175">Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="52c19-175">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="52c19-176">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="52c19-176">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="52c19-177">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="52c19-177">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="52c19-178">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="52c19-178">Output from the component isn't included.</span></span> <span data-ttu-id="52c19-179">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="52c19-179">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="52c19-180">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="52c19-180">Renders the component into static HTML.</span></span> |

<span data-ttu-id="52c19-181">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="52c19-181">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="52c19-182">Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="52c19-182">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="52c19-183">Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="52c19-183">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="52c19-184">Při vykreslení stránky nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="52c19-184">When the page or view renders:</span></span>

* <span data-ttu-id="52c19-185">Komponenta je předem vykreslena se stránkou nebo zobrazením.</span><span class="sxs-lookup"><span data-stu-id="52c19-185">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="52c19-186">Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="52c19-186">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="52c19-187">Po navázání SignalRho připojení se vytvoří nový stav součásti.</span><span class="sxs-lookup"><span data-stu-id="52c19-187">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="52c19-188">Následující stránka Razor vykresluje komponentu `Counter`:</span><span class="sxs-lookup"><span data-stu-id="52c19-188">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="52c19-189">Vykreslování neinteraktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="52c19-189">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="52c19-190">Na následující stránce Razor je součást `Counter` staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="52c19-190">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="52c19-191">Vzhledem k tomu, že `MyComponent` jsou staticky vykresleny, nemůže být komponenta interaktivní.</span><span class="sxs-lookup"><span data-stu-id="52c19-191">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="52c19-192">Konfigurace klienta SignalR pro aplikace Blazor serveru</span><span class="sxs-lookup"><span data-stu-id="52c19-192">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="52c19-193">V některých případech je třeba nakonfigurovat klienta SignalR používaného serverovými aplikacemi Blazor.</span><span class="sxs-lookup"><span data-stu-id="52c19-193">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="52c19-194">Například můžete chtít nakonfigurovat protokolování klienta SignalR pro diagnostiku potíží s připojením.</span><span class="sxs-lookup"><span data-stu-id="52c19-194">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="52c19-195">Konfigurace klienta SignalR v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="52c19-195">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="52c19-196">Přidejte atribut `autostart="false"` do značky `<script>` pro skript `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="52c19-196">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="52c19-197">Zavolejte `Blazor.start` a předejte objekt konfigurace, který určuje SignalR Builder.</span><span class="sxs-lookup"><span data-stu-id="52c19-197">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
