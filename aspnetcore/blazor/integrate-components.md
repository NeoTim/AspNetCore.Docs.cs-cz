---
title: Integrace komponent ASP.NET Core Razor do břitvových stránek a aplikací MVC
author: guardrex
description: Seznamte se se scénáři datových Blazor vazeb pro komponenty a prvky modelu DOM v aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: c242fbef70d289929d5c005abc0aa431619862b3
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383971"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="97aa2-103">Integrace komponent ASP.NET Core Razor do břitvových stránek a aplikací MVC</span><span class="sxs-lookup"><span data-stu-id="97aa2-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="97aa2-104">[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="97aa2-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="97aa2-105">Komponenty Razor lze integrovat do stránek Razor pages a aplikací MVC.</span><span class="sxs-lookup"><span data-stu-id="97aa2-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="97aa2-106">Při vykreslení stránky nebo zobrazení lze komponenty předběžně vykreslit současně.</span><span class="sxs-lookup"><span data-stu-id="97aa2-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="97aa2-107">Po [přípravě aplikace](#prepare-the-app)použijte pokyny v následujících částech v závislosti na požadavcích aplikace:</span><span class="sxs-lookup"><span data-stu-id="97aa2-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="97aa2-108">Směrovatelné součásti &ndash; Pro součásti, které jsou přímo směrovatelné z požadavků uživatelů.</span><span class="sxs-lookup"><span data-stu-id="97aa2-108">Routable components &ndash; For components that are directly routable from user requests.</span></span> <span data-ttu-id="97aa2-109">Postupujte podle těchto pokynů, pokud návštěvníci by měli mít možnost [`@page`](xref:mvc/views/razor#page) provést požadavek HTTP ve svém prohlížeči pro komponentu se směrnicí.</span><span class="sxs-lookup"><span data-stu-id="97aa2-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="97aa2-110">Použití směrovatelných součástí v aplikaci Razor Pages</span><span class="sxs-lookup"><span data-stu-id="97aa2-110">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="97aa2-111">Použití směrovatelných součástí v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="97aa2-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="97aa2-112">[Vykreslení součástí ze stránky nebo zobrazení](#render-components-from-a-page-or-view) &ndash; Pro součásti, které nejsou přímo směrovatelné z požadavků uživatelů.</span><span class="sxs-lookup"><span data-stu-id="97aa2-112">[Render components from a page or view](#render-components-from-a-page-or-view) &ndash; For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="97aa2-113">Tento návod postupujte takto, když aplikace vloží součásti do existujících stránek a zobrazení pomocí [pomocníka pro značku komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="97aa2-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="97aa2-114">Příprava aplikace</span><span class="sxs-lookup"><span data-stu-id="97aa2-114">Prepare the app</span></span>

<span data-ttu-id="97aa2-115">Stávající aplikace Razor Pages nebo MVC dokáže integrovat komponenty Razor do stránek a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="97aa2-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="97aa2-116">V souboru rozložení aplikace (*_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="97aa2-116">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="97aa2-117">Do `<head>` prvku `<base>` přidejte následující značku:</span><span class="sxs-lookup"><span data-stu-id="97aa2-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="97aa2-118">Hodnota `href` *(cesta základu aplikace*) v předchozím příkladu předpokládá, že aplikace`/`je umístěna na kořenové cestě URL ( ).</span><span class="sxs-lookup"><span data-stu-id="97aa2-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="97aa2-119">Pokud je aplikace podaplikace, postupujte podle pokynů v části <xref:host-and-deploy/blazor/index#app-base-path> cesta základní *aplikace* článku.</span><span class="sxs-lookup"><span data-stu-id="97aa2-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="97aa2-120">Soubor *_Layout.cshtml* se nachází ve složce *Stránky/Sdílené* v aplikaci Razor Pages nebo V aplikaci *Zobrazení/Sdílené* v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="97aa2-120">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="97aa2-121">Přidejte `<script>` značku pro skript *blazor.server.js* bezprostředně `</body>` před uzavírací značkou:</span><span class="sxs-lookup"><span data-stu-id="97aa2-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="97aa2-122">Rozhraní framework přidá do aplikace skript *blazor.server.js.*</span><span class="sxs-lookup"><span data-stu-id="97aa2-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="97aa2-123">Není třeba ručně přidávat skript do aplikace.</span><span class="sxs-lookup"><span data-stu-id="97aa2-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="97aa2-124">Přidejte soubor *_Imports.razor* do kořenové složky projektu s následujícím obsahem (změňte obor posledního oboru názvů , `MyAppNamespace`do oboru názvů aplikace):</span><span class="sxs-lookup"><span data-stu-id="97aa2-124">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="97aa2-125">V `Startup.ConfigureServices`, zaregistrujte službu Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="97aa2-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="97aa2-126">V `Startup.Configure`doplňku Blazor Hub `app.UseEndpoints`přidejte do doplňku :</span><span class="sxs-lookup"><span data-stu-id="97aa2-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="97aa2-127">Integrujte součásti do libovolné stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="97aa2-127">Integrate components into any page or view.</span></span> <span data-ttu-id="97aa2-128">Další informace naleznete v tématu [Render komponenty ze stránky nebo zobrazení](#render-components-from-a-page-or-view) oddílu.</span><span class="sxs-lookup"><span data-stu-id="97aa2-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="97aa2-129">Použití směrovatelných součástí v aplikaci Razor Pages</span><span class="sxs-lookup"><span data-stu-id="97aa2-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="97aa2-130">*Tato část se týkajících se přidání komponent, které jsou přímo směrovatelné z požadavků uživatelů.*</span><span class="sxs-lookup"><span data-stu-id="97aa2-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="97aa2-131">Podpora směrovatelných komponent Razor v aplikacích Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="97aa2-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="97aa2-132">Postupujte podle pokynů v části [Příprava aplikace.](#prepare-the-app)</span><span class="sxs-lookup"><span data-stu-id="97aa2-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="97aa2-133">Přidejte soubor *App.razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="97aa2-133">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="97aa2-134">Přidejte do složky *Stránky* soubor *_Host.cshtml* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="97aa2-134">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="97aa2-135">Komponenty používají pro své rozložení sdílený soubor *_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="97aa2-135">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="97aa2-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>konfiguruje, `App` zda součást:</span><span class="sxs-lookup"><span data-stu-id="97aa2-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="97aa2-137">Je prerendered do stránky.</span><span class="sxs-lookup"><span data-stu-id="97aa2-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="97aa2-138">Je vykreslen jako statické HTML na stránce, nebo pokud obsahuje potřebné informace k bootstrap aplikace Blazor od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="97aa2-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="97aa2-139">Režim vykreslení</span><span class="sxs-lookup"><span data-stu-id="97aa2-139">Render Mode</span></span> | <span data-ttu-id="97aa2-140">Popis</span><span class="sxs-lookup"><span data-stu-id="97aa2-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="97aa2-141">Vykreslí `App` komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="97aa2-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="97aa2-142">Když se spustí user-agent, tato značka se používá k zavádění aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="97aa2-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="97aa2-143">Vykreslí značku pro aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="97aa2-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="97aa2-144">Výstup z `App` komponenty není zahrnut.</span><span class="sxs-lookup"><span data-stu-id="97aa2-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="97aa2-145">Když se spustí user-agent, tato značka se používá k zavádění aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="97aa2-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="97aa2-146">Vykreslí `App` komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="97aa2-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="97aa2-147">Další informace o pomocníku pro <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>označení součásti naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="97aa2-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="97aa2-148">Přidejte trasu s nízkou prioritou pro konfiguraci stránky `Startup.Configure` *_Host.cshtml* do konfigurace koncového bodu v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="97aa2-148">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="97aa2-149">Přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="97aa2-149">Add routable components to the app.</span></span> <span data-ttu-id="97aa2-150">Příklad:</span><span class="sxs-lookup"><span data-stu-id="97aa2-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="97aa2-151">Další informace o oborech názvů naleznete v části [Obory názvů komponenty.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="97aa2-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="97aa2-152">Použití směrovatelných součástí v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="97aa2-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="97aa2-153">*Tato část se týkajících se přidání komponent, které jsou přímo směrovatelné z požadavků uživatelů.*</span><span class="sxs-lookup"><span data-stu-id="97aa2-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="97aa2-154">Podpora směrovatelných komponent Razor v aplikacích MVC:</span><span class="sxs-lookup"><span data-stu-id="97aa2-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="97aa2-155">Postupujte podle pokynů v části [Příprava aplikace.](#prepare-the-app)</span><span class="sxs-lookup"><span data-stu-id="97aa2-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="97aa2-156">Přidejte soubor *App.razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="97aa2-156">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="97aa2-157">Přidejte soubor *_Host.cshtml* do složky *Zobrazení/Domov* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="97aa2-157">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="97aa2-158">Komponenty používají pro své rozložení sdílený soubor *_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="97aa2-158">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="97aa2-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>konfiguruje, `App` zda součást:</span><span class="sxs-lookup"><span data-stu-id="97aa2-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="97aa2-160">Je prerendered do stránky.</span><span class="sxs-lookup"><span data-stu-id="97aa2-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="97aa2-161">Je vykreslen jako statické HTML na stránce, nebo pokud obsahuje potřebné informace k bootstrap aplikace Blazor od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="97aa2-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="97aa2-162">Režim vykreslení</span><span class="sxs-lookup"><span data-stu-id="97aa2-162">Render Mode</span></span> | <span data-ttu-id="97aa2-163">Popis</span><span class="sxs-lookup"><span data-stu-id="97aa2-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="97aa2-164">Vykreslí `App` komponentu do statického KÓDU Blazor HTML a obsahuje značku pro serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="97aa2-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="97aa2-165">Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace.</span><span class="sxs-lookup"><span data-stu-id="97aa2-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="97aa2-166">Vykreslí značku Blazor pro serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="97aa2-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="97aa2-167">Výstup z `App` komponenty není zahrnut.</span><span class="sxs-lookup"><span data-stu-id="97aa2-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="97aa2-168">Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace.</span><span class="sxs-lookup"><span data-stu-id="97aa2-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="97aa2-169">Vykreslí `App` komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="97aa2-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="97aa2-170">Další informace o pomocníku pro <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>označení součásti naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="97aa2-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="97aa2-171">Přidejte akci do domácího ovladače:</span><span class="sxs-lookup"><span data-stu-id="97aa2-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="97aa2-172">Přidejte trasu s nízkou prioritou pro akci kontroléru, která vrátí `Startup.Configure`zobrazení *_Host.cshtml* do konfigurace koncového bodu v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="97aa2-172">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="97aa2-173">Vytvořte složku *Stránky* a přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="97aa2-173">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="97aa2-174">Příklad:</span><span class="sxs-lookup"><span data-stu-id="97aa2-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="97aa2-175">Další informace o oborech názvů naleznete v části [Obory názvů komponenty.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="97aa2-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="97aa2-176">Vykreslení součástí ze stránky nebo zobrazení</span><span class="sxs-lookup"><span data-stu-id="97aa2-176">Render components from a page or view</span></span>

<span data-ttu-id="97aa2-177">*Tato část se týká přidávání součástí na stránky nebo zobrazení, kde součásti nejsou přímo směrovatelné z požadavků uživatelů.*</span><span class="sxs-lookup"><span data-stu-id="97aa2-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="97aa2-178">Chcete-li vykreslit komponentu ze stránky nebo ze zobrazení, použijte [pomocníka pro označení součásti](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="97aa2-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="97aa2-179">Další informace o způsobu vykreslení součástí, stavu `Component` komponent a pomocné značce naleznete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="97aa2-179">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>

## <a name="component-namespaces"></a><span data-ttu-id="97aa2-180">Obory názvů komponent</span><span class="sxs-lookup"><span data-stu-id="97aa2-180">Component namespaces</span></span>

<span data-ttu-id="97aa2-181">Při použití vlastní složky k uložení součástí aplikace přidejte obor názvů představující složku buď na stránku nebo zobrazení, nebo do souboru *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="97aa2-181">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="97aa2-182">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="97aa2-182">In the following example:</span></span>

* <span data-ttu-id="97aa2-183">Změňte `MyAppNamespace` obor názvů aplikace.</span><span class="sxs-lookup"><span data-stu-id="97aa2-183">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="97aa2-184">Pokud složka s názvem *Components* není použita k `Components` uložení součástí, změňte na složku, ve které jsou součásti umístěny.</span><span class="sxs-lookup"><span data-stu-id="97aa2-184">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="97aa2-185">Soubor *_ViewImports.cshtml* se nachází ve složce *Stránky* aplikace Razor Pages nebo ve složce *Zobrazení* aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="97aa2-185">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="97aa2-186">Další informace naleznete v tématu <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="97aa2-186">For more information, see <xref:blazor/components#import-components>.</span></span>
