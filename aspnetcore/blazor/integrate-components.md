---
title: Integrace součástí ASP.NET Core Razor do Razor Pages a aplikací MVC
author: guardrex
description: Přečtěte si o scénářích datových vazeb pro komponenty a prvky modelu DOM v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663315"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="ccdfc-103">Integrace součástí ASP.NET Core Razor do Razor Pages a aplikací MVC</span><span class="sxs-lookup"><span data-stu-id="ccdfc-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="ccdfc-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ccdfc-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ccdfc-105">Komponenty Razor lze integrovat do aplikací Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="ccdfc-106">Po vykreslení stránky nebo zobrazení mohou být komponenty předem vykresleny ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="ccdfc-107">Příprava aplikace na používání komponent na stránkách a zobrazeních</span><span class="sxs-lookup"><span data-stu-id="ccdfc-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="ccdfc-108">Existující Razor Pages nebo aplikace MVC mohou integrovat součásti Razor do stránek a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="ccdfc-109">V souboru rozložení aplikace ( *_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="ccdfc-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="ccdfc-110">Přidejte následující značku `<base>` do prvku `<head>`:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="ccdfc-111">Hodnota `href` ( *základní cesta aplikace*) v předchozím příkladu předpokládá, že se aplikace nachází v kořenové cestě URL (`/`).</span><span class="sxs-lookup"><span data-stu-id="ccdfc-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="ccdfc-112">Pokud je aplikace podaplikace, postupujte podle pokynů v části *základní cesta k aplikaci* v <xref:host-and-deploy/blazor/index#app-base-path> článku.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="ccdfc-113">Soubor *_Layout. cshtml* se nachází ve složce *stránky nebo sdílené* složky v aplikaci Razor Pages nebo *zobrazení/sdílená* složka v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="ccdfc-114">Přidejte značku `<script>` pro skript *blazor. Server. js* těsně před uzavírací značku `</body>`:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="ccdfc-115">Rozhraní přidá do aplikace skript *blazor. Server. js* .</span><span class="sxs-lookup"><span data-stu-id="ccdfc-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="ccdfc-116">Nemusíte ručně přidávat do aplikace skript.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="ccdfc-117">Přidejte soubor *_Imports. Razor* do kořenové složky projektu s následujícím obsahem (změňte poslední obor názvů `MyAppNamespace`na obor názvů aplikace):</span><span class="sxs-lookup"><span data-stu-id="ccdfc-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="ccdfc-118">V `Startup.ConfigureServices`Zaregistrujte službu Blazor serveru:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="ccdfc-119">V `Startup.Configure`přidejte koncový bod centra Blazor do `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="ccdfc-120">Integrujte součásti na libovolnou stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-120">Integrate components into any page or view.</span></span> <span data-ttu-id="ccdfc-121">Další informace naleznete v části [vykreslení komponent ze stránky nebo zobrazení](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="ccdfc-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="ccdfc-122">Použití směrovatelných komponent v aplikaci Razor Pages</span><span class="sxs-lookup"><span data-stu-id="ccdfc-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="ccdfc-123">*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="ccdfc-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="ccdfc-124">Podpora směrování komponent s více prvky Razor v aplikacích Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="ccdfc-125">Postupujte podle pokynů v části [Příprava aplikace na používání komponent v částech stránky a zobrazení](#prepare-the-app-to-use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="ccdfc-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="ccdfc-126">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="ccdfc-127">Do složky *Pages* přidejte *_Host soubor. cshtml* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="ccdfc-128">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ccdfc-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="ccdfc-129">Přidejte trasu s nízkou prioritou pro stránku *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="ccdfc-130">Přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-130">Add routable components to the app.</span></span> <span data-ttu-id="ccdfc-131">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="ccdfc-132">Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="ccdfc-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="ccdfc-133">Použití směrovatelných komponent v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="ccdfc-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="ccdfc-134">*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="ccdfc-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="ccdfc-135">Podpora směrovatelných komponent Razor v aplikacích MVC:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="ccdfc-136">Postupujte podle pokynů v části [Příprava aplikace na používání komponent v částech stránky a zobrazení](#prepare-the-app-to-use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="ccdfc-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="ccdfc-137">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="ccdfc-138">Přidejte *_Host soubor. cshtml* do *zobrazení/domovské* složky s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="ccdfc-139">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ccdfc-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="ccdfc-140">Přidat akci do domovského kontroleru:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="ccdfc-141">Přidejte trasu s nízkou prioritou pro akci kontroleru, která vrací zobrazení *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="ccdfc-142">Vytvořte složku *Pages* a přidejte do ní součásti s funkcí směrování.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="ccdfc-143">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="ccdfc-144">Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="ccdfc-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="ccdfc-145">Obory názvů součásti</span><span class="sxs-lookup"><span data-stu-id="ccdfc-145">Component namespaces</span></span>

<span data-ttu-id="ccdfc-146">Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do stránky nebo zobrazení nebo do souboru *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ccdfc-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="ccdfc-147">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-147">In the following example:</span></span>

* <span data-ttu-id="ccdfc-148">Změňte `MyAppNamespace` na obor názvů aplikace.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="ccdfc-149">Pokud se složka s názvem *Components* nepoužívá k ukládání součástí, změňte `Components` do složky, kde jsou umístěny součásti.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="ccdfc-150">Soubor *_ViewImports. cshtml* je umístěný ve složce *pages* aplikace Razor Pages nebo ve složce *zobrazení* aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="ccdfc-151">Další informace naleznete v tématu <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="ccdfc-152">Vykreslení komponent ze stránky nebo zobrazení</span><span class="sxs-lookup"><span data-stu-id="ccdfc-152">Render components from a page or view</span></span>

<span data-ttu-id="ccdfc-153">*Tato část se vztahuje na přidávání komponent na stránky nebo zobrazení, kde součásti nejsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="ccdfc-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="ccdfc-154">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte pomocníka značky `Component`:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-154">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="ccdfc-155">Typ parametru musí být serializovatelný jako JSON, což obvykle znamená, že typ musí mít výchozí konstruktor a nastavitelné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-155">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="ccdfc-156">Můžete například zadat hodnotu pro `IncrementAmount`, protože typ `IncrementAmount` je `int`, což je primitivní typ podporovaný serializátorem JSON.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-156">For example, you can specify a value for `IncrementAmount` because the type of `IncrementAmount` is an `int`, which is a primitive type supported by the JSON serializer.</span></span>

<span data-ttu-id="ccdfc-157">`RenderMode` nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-157">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="ccdfc-158">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-158">Is prerendered into the page.</span></span>
* <span data-ttu-id="ccdfc-159">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-159">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="ccdfc-160">Popis</span><span class="sxs-lookup"><span data-stu-id="ccdfc-160">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="ccdfc-161">Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-161">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="ccdfc-162">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-162">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="ccdfc-163">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-163">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="ccdfc-164">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-164">Output from the component isn't included.</span></span> <span data-ttu-id="ccdfc-165">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="ccdfc-166">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-166">Renders the component into static HTML.</span></span> |

<span data-ttu-id="ccdfc-167">I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-167">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="ccdfc-168">Komponenty nemůžou používat scénáře zobrazení a stránky, jako jsou například částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-168">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="ccdfc-169">Chcete-li použít logiku z částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-169">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="ccdfc-170">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="ccdfc-170">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="ccdfc-171">Další informace o vykreslování komponent, stavu komponent a pomocníka značek `Component` naleznete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="ccdfc-171">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
