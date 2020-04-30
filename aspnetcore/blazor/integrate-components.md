---
title: Integrace součástí ASP.NET Core Razor do Razor Pages a aplikací MVC
author: guardrex
description: Přečtěte si o scénářích datových vazeb pro komponenty a Blazor prvky modelu DOM v aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 4e2103b7e8b65478808093d7a31e8cfe29b04984
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558914"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="5733f-103">Integrace součástí ASP.NET Core Razor do Razor Pages a aplikací MVC</span><span class="sxs-lookup"><span data-stu-id="5733f-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="5733f-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5733f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5733f-105">Komponenty Razor lze integrovat do aplikací Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="5733f-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="5733f-106">Po vykreslení stránky nebo zobrazení mohou být komponenty předem vykresleny ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="5733f-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="5733f-107">Po [přípravě aplikace](#prepare-the-app)použijte pokyny v následujících částech v závislosti na požadavcích aplikace:</span><span class="sxs-lookup"><span data-stu-id="5733f-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="5733f-108">Směrovatelné &ndash; komponenty pro součásti, které jsou přímo směrovatelné od uživatelských požadavků.</span><span class="sxs-lookup"><span data-stu-id="5733f-108">Routable components &ndash; For components that are directly routable from user requests.</span></span> <span data-ttu-id="5733f-109">Postupujte podle těchto pokynů, pokud by Návštěvníci mohli ve svém prohlížeči vytvořit požadavek HTTP pro komponentu s [`@page`](xref:mvc/views/razor#page) direktivou.</span><span class="sxs-lookup"><span data-stu-id="5733f-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="5733f-110">Použití směrovatelných komponent v aplikaci Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5733f-110">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="5733f-111">Použití směrovatelných komponent v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="5733f-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="5733f-112">[Vykreslí komponenty ze stránky nebo zobrazení](#render-components-from-a-page-or-view) &ndash; pro součásti, které nejsou přímo směrovatelné od uživatelských požadavků.</span><span class="sxs-lookup"><span data-stu-id="5733f-112">[Render components from a page or view](#render-components-from-a-page-or-view) &ndash; For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="5733f-113">Postupujte podle těchto pokynů, pokud aplikace vloží komponenty do existujících stránek a zobrazení pomocí [pomocné rutiny tag komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5733f-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="5733f-114">Příprava aplikace</span><span class="sxs-lookup"><span data-stu-id="5733f-114">Prepare the app</span></span>

<span data-ttu-id="5733f-115">Existující Razor Pages nebo aplikace MVC mohou integrovat součásti Razor do stránek a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="5733f-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="5733f-116">V souboru rozložení aplikace (*_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="5733f-116">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="5733f-117">Přidejte následující `<base>` značku do `<head>` prvku:</span><span class="sxs-lookup"><span data-stu-id="5733f-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="5733f-118">Hodnota ( *základní cesta aplikace*) v předchozím příkladu předpokládá, že se aplikace nachází v kořenové cestě URL (`/`). `href`</span><span class="sxs-lookup"><span data-stu-id="5733f-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="5733f-119">Pokud je aplikace podaplikace, postupujte podle pokynů v části *základní cesta k aplikaci* v <xref:host-and-deploy/blazor/index#app-base-path> článku.</span><span class="sxs-lookup"><span data-stu-id="5733f-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="5733f-120">Soubor *_Layout. cshtml* se nachází ve složce *stránky nebo sdílené* složky v aplikaci Razor Pages nebo *zobrazení/sdílená* složka v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="5733f-120">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="5733f-121">Přidejte `<script>` značku pro skript *blazor. Server. js* těsně před uzavírací `</body>` značku:</span><span class="sxs-lookup"><span data-stu-id="5733f-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="5733f-122">Rozhraní přidá do aplikace skript *blazor. Server. js* .</span><span class="sxs-lookup"><span data-stu-id="5733f-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="5733f-123">Nemusíte ručně přidávat do aplikace skript.</span><span class="sxs-lookup"><span data-stu-id="5733f-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="5733f-124">Přidejte soubor *_Imports. Razor* do kořenové složky projektu s následujícím obsahem (změňte poslední obor názvů `MyAppNamespace`na obor názvů aplikace):</span><span class="sxs-lookup"><span data-stu-id="5733f-124">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="5733f-125">V `Startup.ConfigureServices`nástroji Zaregistrujte službu serveru Blazor:</span><span class="sxs-lookup"><span data-stu-id="5733f-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="5733f-126">Do `Startup.Configure`přidejte koncový bod centra Blazor do `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="5733f-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="5733f-127">Integrujte součásti na libovolnou stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5733f-127">Integrate components into any page or view.</span></span> <span data-ttu-id="5733f-128">Další informace naleznete v části [vykreslení komponent ze stránky nebo zobrazení](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="5733f-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="5733f-129">Použití směrovatelných komponent v aplikaci Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5733f-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="5733f-130">*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="5733f-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="5733f-131">Podpora směrování komponent s více prvky Razor v aplikacích Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="5733f-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="5733f-132">Postupujte podle pokynů v části [Příprava aplikace](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="5733f-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="5733f-133">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="5733f-133">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="5733f-134">Do složky *Pages* přidejte *_Host soubor. cshtml* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="5733f-134">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="5733f-135">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="5733f-135">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="5733f-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>nakonfiguruje, `App` jestli součást:</span><span class="sxs-lookup"><span data-stu-id="5733f-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="5733f-137">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="5733f-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="5733f-138">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="5733f-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="5733f-139">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="5733f-139">Render Mode</span></span> | <span data-ttu-id="5733f-140">Popis</span><span class="sxs-lookup"><span data-stu-id="5733f-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="5733f-141">Vykreslí `App` komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5733f-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5733f-142">Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor.</span><span class="sxs-lookup"><span data-stu-id="5733f-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="5733f-143">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="5733f-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5733f-144">Výstup `App` komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="5733f-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="5733f-145">Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor.</span><span class="sxs-lookup"><span data-stu-id="5733f-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="5733f-146">Vykreslí `App` komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="5733f-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="5733f-147">Další informace o pomocníka značek komponenty naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="5733f-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="5733f-148">Přidejte trasu s nízkou prioritou pro stránku *_Host. cshtml* do konfigurace koncového bodu `Startup.Configure`v:</span><span class="sxs-lookup"><span data-stu-id="5733f-148">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="5733f-149">Přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="5733f-149">Add routable components to the app.</span></span> <span data-ttu-id="5733f-150">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5733f-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="5733f-151">Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="5733f-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="5733f-152">Použití směrovatelných komponent v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="5733f-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="5733f-153">*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="5733f-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="5733f-154">Podpora směrovatelných komponent Razor v aplikacích MVC:</span><span class="sxs-lookup"><span data-stu-id="5733f-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="5733f-155">Postupujte podle pokynů v části [Příprava aplikace](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="5733f-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="5733f-156">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="5733f-156">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="5733f-157">Přidejte *_Host soubor. cshtml* do *zobrazení/domovské* složky s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="5733f-157">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="5733f-158">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="5733f-158">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="5733f-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>nakonfiguruje, `App` jestli součást:</span><span class="sxs-lookup"><span data-stu-id="5733f-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="5733f-160">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="5733f-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="5733f-161">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="5733f-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="5733f-162">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="5733f-162">Render Mode</span></span> | <span data-ttu-id="5733f-163">Popis</span><span class="sxs-lookup"><span data-stu-id="5733f-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="5733f-164">Vykreslí `App` komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5733f-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5733f-165">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="5733f-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="5733f-166">Vykreslí značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5733f-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5733f-167">Výstup `App` komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="5733f-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="5733f-168">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="5733f-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="5733f-169">Vykreslí `App` komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="5733f-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="5733f-170">Další informace o pomocníka značek komponenty naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="5733f-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="5733f-171">Přidat akci do domovského kontroleru:</span><span class="sxs-lookup"><span data-stu-id="5733f-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="5733f-172">Přidejte trasu s nízkou prioritou pro akci kontroleru, která vrací zobrazení *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure`nástroji:</span><span class="sxs-lookup"><span data-stu-id="5733f-172">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="5733f-173">Vytvořte složku *Pages* a přidejte do ní součásti s funkcí směrování.</span><span class="sxs-lookup"><span data-stu-id="5733f-173">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="5733f-174">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5733f-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="5733f-175">Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="5733f-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="5733f-176">Vykreslení komponent ze stránky nebo zobrazení</span><span class="sxs-lookup"><span data-stu-id="5733f-176">Render components from a page or view</span></span>

<span data-ttu-id="5733f-177">*Tato část se vztahuje na přidávání komponent na stránky nebo zobrazení, kde součásti nejsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="5733f-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="5733f-178">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5733f-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="5733f-179">Vykreslení stavových interaktivních komponent</span><span class="sxs-lookup"><span data-stu-id="5733f-179">Render stateful interactive components</span></span>

<span data-ttu-id="5733f-180">Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="5733f-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="5733f-181">Při vykreslení stránky nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="5733f-181">When the page or view renders:</span></span>

* <span data-ttu-id="5733f-182">Komponenta je předem vykreslena se stránkou nebo zobrazením.</span><span class="sxs-lookup"><span data-stu-id="5733f-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="5733f-183">Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="5733f-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="5733f-184">Po navázání SignalR připojení se vytvoří nový stav součásti.</span><span class="sxs-lookup"><span data-stu-id="5733f-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="5733f-185">Následující stránka Razor vykresluje `Counter` komponentu:</span><span class="sxs-lookup"><span data-stu-id="5733f-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="5733f-186">Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="5733f-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="5733f-187">Vykreslit neinteraktivní součásti</span><span class="sxs-lookup"><span data-stu-id="5733f-187">Render noninteractive components</span></span>

<span data-ttu-id="5733f-188">Na následující stránce Razor je `Counter` komponenta staticky vykreslena s počáteční hodnotou, která je zadána pomocí formuláře.</span><span class="sxs-lookup"><span data-stu-id="5733f-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="5733f-189">Vzhledem k tomu, že je komponenta staticky vykreslena, tato součást není interaktivní:</span><span class="sxs-lookup"><span data-stu-id="5733f-189">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="5733f-190">Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="5733f-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="5733f-191">Obory názvů součásti</span><span class="sxs-lookup"><span data-stu-id="5733f-191">Component namespaces</span></span>

<span data-ttu-id="5733f-192">Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do stránky nebo zobrazení nebo do souboru *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="5733f-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="5733f-193">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5733f-193">In the following example:</span></span>

* <span data-ttu-id="5733f-194">Přejděte `MyAppNamespace` do oboru názvů aplikace.</span><span class="sxs-lookup"><span data-stu-id="5733f-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="5733f-195">Pokud se složka s názvem *Components* nepoužívá k ukládání součástí, `Components` přejděte do složky, kde jsou umístěny součásti.</span><span class="sxs-lookup"><span data-stu-id="5733f-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="5733f-196">Soubor *_ViewImports. cshtml* je umístěný ve složce *pages* aplikace Razor Pages nebo ve složce *zobrazení* aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="5733f-196">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="5733f-197">Další informace naleznete v tématu <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="5733f-197">For more information, see <xref:blazor/components#import-components>.</span></span>
