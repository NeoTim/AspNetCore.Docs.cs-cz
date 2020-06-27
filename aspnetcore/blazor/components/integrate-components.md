---
title: Integrace Razor součástí ASP.NET Core do Razor stránek a aplikací MVC
author: guardrex
description: Přečtěte si o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: 29360174ea86623491d5d8aacd4038162855bba8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399059"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="9f901-103">Integrace Razor součástí ASP.NET Core do Razor stránek a aplikací MVC</span><span class="sxs-lookup"><span data-stu-id="9f901-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="9f901-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9f901-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="9f901-105">komponenty lze integrovat do Razor stránek a aplikací MVC.</span><span class="sxs-lookup"><span data-stu-id="9f901-105"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="9f901-106">Po vykreslení stránky nebo zobrazení mohou být komponenty předem vykresleny ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="9f901-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="9f901-107">Po [přípravě aplikace](#prepare-the-app)použijte pokyny v následujících částech v závislosti na požadavcích aplikace:</span><span class="sxs-lookup"><span data-stu-id="9f901-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="9f901-108">Směrovatelné komponenty: pro součásti, které jsou přímo směrovatelné od uživatelských požadavků.</span><span class="sxs-lookup"><span data-stu-id="9f901-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="9f901-109">Postupujte podle těchto pokynů, pokud by Návštěvníci mohli ve svém prohlížeči vytvořit požadavek HTTP pro komponentu s [`@page`](xref:mvc/views/razor#page) direktivou.</span><span class="sxs-lookup"><span data-stu-id="9f901-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="9f901-110">[Použití směrovatelných komponent v Razor aplikaci Pages](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="9f901-110">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="9f901-111">Použití směrovatelných komponent v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="9f901-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="9f901-112">[Vykreslit součásti ze stránky nebo zobrazení](#render-components-from-a-page-or-view): pro součásti, které přímo Nesměrovatelné od uživatelských požadavků.</span><span class="sxs-lookup"><span data-stu-id="9f901-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="9f901-113">Postupujte podle těchto pokynů, pokud aplikace vloží komponenty do existujících stránek a zobrazení pomocí [pomocné rutiny tag komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="9f901-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="9f901-114">Příprava aplikace</span><span class="sxs-lookup"><span data-stu-id="9f901-114">Prepare the app</span></span>

<span data-ttu-id="9f901-115">Existující Razor stránky nebo aplikace MVC mohou integrovat Razor komponenty do stránek a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="9f901-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="9f901-116">V souboru rozložení aplikace ( `_Layout.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="9f901-116">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="9f901-117">Přidejte následující `<base>` značku do `<head>` prvku:</span><span class="sxs-lookup"><span data-stu-id="9f901-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="9f901-118">`href`Hodnota ( *základní cesta aplikace*) v předchozím příkladu předpokládá, že se aplikace nachází v kořenové cestě URL ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="9f901-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="9f901-119">Pokud je aplikace podaplikace, postupujte podle pokynů v části *základní cesta k aplikaci* v <xref:blazor/host-and-deploy/index#app-base-path> článku.</span><span class="sxs-lookup"><span data-stu-id="9f901-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="9f901-120">`_Layout.cshtml`Soubor se nachází ve složce *stránky/sdílená* složka v Razor aplikaci stránky nebo *zobrazení/sdílená* složka v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="9f901-120">The `_Layout.cshtml` file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="9f901-121">Přidejte `<script>` značku pro skript *blazor.server.js* těsně před uzavírací `</body>` značku:</span><span class="sxs-lookup"><span data-stu-id="9f901-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="9f901-122">Rozhraní přidá do aplikace skript *blazor.server.js* .</span><span class="sxs-lookup"><span data-stu-id="9f901-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="9f901-123">Nemusíte ručně přidávat do aplikace skript.</span><span class="sxs-lookup"><span data-stu-id="9f901-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="9f901-124">Přidejte `_Imports.razor` soubor do kořenové složky projektu s následujícím obsahem (změňte poslední obor názvů `MyAppNamespace` na obor názvů aplikace):</span><span class="sxs-lookup"><span data-stu-id="9f901-124">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="9f901-125">V nástroji `Startup.ConfigureServices` Zaregistrujte Blazor Server službu:</span><span class="sxs-lookup"><span data-stu-id="9f901-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="9f901-126">Do `Startup.Configure` přidejte Blazor koncový bod centra do `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="9f901-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="9f901-127">Integrujte součásti na libovolnou stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="9f901-127">Integrate components into any page or view.</span></span> <span data-ttu-id="9f901-128">Další informace naleznete v části [vykreslení komponent ze stránky nebo zobrazení](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="9f901-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="9f901-129">Použití směrovatelných komponent v Razor aplikaci Pages</span><span class="sxs-lookup"><span data-stu-id="9f901-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="9f901-130">*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="9f901-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="9f901-131">Podpora směrování Razor komponent v Razor aplikacích Pages:</span><span class="sxs-lookup"><span data-stu-id="9f901-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="9f901-132">Postupujte podle pokynů v části [Příprava aplikace](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="9f901-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="9f901-133">`App.razor`Do kořenového adresáře projektu přidejte soubor s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="9f901-133">Add an `App.razor` file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="9f901-134">Přidejte `_Host.cshtml` do `Pages` složky soubor s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="9f901-134">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="9f901-135">Komponenty používají `_Layout.cshtml` pro své rozložení sdílený soubor.</span><span class="sxs-lookup"><span data-stu-id="9f901-135">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="9f901-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>nakonfiguruje, jestli `App` součást:</span><span class="sxs-lookup"><span data-stu-id="9f901-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="9f901-137">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="9f901-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="9f901-138">Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="9f901-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="9f901-139">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="9f901-139">Render Mode</span></span> | <span data-ttu-id="9f901-140">Description</span><span class="sxs-lookup"><span data-stu-id="9f901-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="9f901-141">Vykreslí `App` komponentu do statického HTML a obsahuje značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9f901-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="9f901-142">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f901-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="9f901-143">Vykreslí značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9f901-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="9f901-144">Výstup `App` komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="9f901-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="9f901-145">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f901-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="9f901-146">Vykreslí `App` komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="9f901-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="9f901-147">Další informace o pomocníka značek komponenty naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="9f901-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="9f901-148">Přidejte do `_Host.cshtml` Konfigurace koncového bodu v nástroji trasu s nízkou prioritou `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9f901-148">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="9f901-149">Přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="9f901-149">Add routable components to the app.</span></span> <span data-ttu-id="9f901-150">Například:</span><span class="sxs-lookup"><span data-stu-id="9f901-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="9f901-151">Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="9f901-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="9f901-152">Použití směrovatelných komponent v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="9f901-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="9f901-153">*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="9f901-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="9f901-154">Podpora směrování Razor komponent v aplikacích MVC:</span><span class="sxs-lookup"><span data-stu-id="9f901-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="9f901-155">Postupujte podle pokynů v části [Příprava aplikace](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="9f901-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="9f901-156">Přidejte `App.razor` soubor do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="9f901-156">Add an `App.razor` file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="9f901-157">Přidejte `_Host.cshtml` do `Views/Home` složky soubor s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="9f901-157">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="9f901-158">Komponenty používají `_Layout.cshtml` pro své rozložení sdílený soubor.</span><span class="sxs-lookup"><span data-stu-id="9f901-158">Components use the shared `_Layout.cshtml` file for their layout.</span></span>
   
   <span data-ttu-id="9f901-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>nakonfiguruje, jestli `App` součást:</span><span class="sxs-lookup"><span data-stu-id="9f901-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="9f901-160">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="9f901-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="9f901-161">Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="9f901-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="9f901-162">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="9f901-162">Render Mode</span></span> | <span data-ttu-id="9f901-163">Description</span><span class="sxs-lookup"><span data-stu-id="9f901-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="9f901-164">Vykreslí `App` komponentu do statického HTML a obsahuje značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9f901-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="9f901-165">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f901-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="9f901-166">Vykreslí značku pro Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9f901-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="9f901-167">Výstup `App` komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="9f901-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="9f901-168">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f901-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="9f901-169">Vykreslí `App` komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="9f901-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="9f901-170">Další informace o pomocníka značek komponenty naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="9f901-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="9f901-171">Přidat akci do domovského kontroleru:</span><span class="sxs-lookup"><span data-stu-id="9f901-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="9f901-172">Přidejte trasu s nízkou prioritou pro akci kontroleru, která vrátí `_Host.cshtml` zobrazení do konfigurace koncového bodu v nástroji `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9f901-172">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="9f901-173">Vytvořte `Pages` složku a přidejte do ní komponenty s funkcí směrování.</span><span class="sxs-lookup"><span data-stu-id="9f901-173">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="9f901-174">Například:</span><span class="sxs-lookup"><span data-stu-id="9f901-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="9f901-175">Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="9f901-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="9f901-176">Vykreslení komponent ze stránky nebo zobrazení</span><span class="sxs-lookup"><span data-stu-id="9f901-176">Render components from a page or view</span></span>

<span data-ttu-id="9f901-177">*Tato část se vztahuje na přidávání komponent na stránky nebo zobrazení, kde součásti nejsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="9f901-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="9f901-178">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="9f901-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="9f901-179">Vykreslení stavových interaktivních komponent</span><span class="sxs-lookup"><span data-stu-id="9f901-179">Render stateful interactive components</span></span>

<span data-ttu-id="9f901-180">Stavové interaktivní komponenty lze přidat na Razor stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="9f901-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="9f901-181">Při vykreslení stránky nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="9f901-181">When the page or view renders:</span></span>

* <span data-ttu-id="9f901-182">Komponenta je předem vykreslena se stránkou nebo zobrazením.</span><span class="sxs-lookup"><span data-stu-id="9f901-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="9f901-183">Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="9f901-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="9f901-184">Po navázání připojení se vytvoří nový stav součásti SignalR .</span><span class="sxs-lookup"><span data-stu-id="9f901-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="9f901-185">Následující Razor stránka vykreslí `Counter` součást:</span><span class="sxs-lookup"><span data-stu-id="9f901-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="9f901-186">Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="9f901-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="9f901-187">Vykreslit neinteraktivní součásti</span><span class="sxs-lookup"><span data-stu-id="9f901-187">Render noninteractive components</span></span>

<span data-ttu-id="9f901-188">Na následující Razor stránce `Counter` je komponenta staticky vykreslena s počáteční hodnotou, která je zadána pomocí formuláře.</span><span class="sxs-lookup"><span data-stu-id="9f901-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="9f901-189">Vzhledem k tomu, že je komponenta staticky vykreslena, tato součást není interaktivní:</span><span class="sxs-lookup"><span data-stu-id="9f901-189">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="9f901-190">Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="9f901-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="9f901-191">Obory názvů součásti</span><span class="sxs-lookup"><span data-stu-id="9f901-191">Component namespaces</span></span>

<span data-ttu-id="9f901-192">Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do stránky nebo zobrazení nebo do `_ViewImports.cshtml` souboru.</span><span class="sxs-lookup"><span data-stu-id="9f901-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="9f901-193">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f901-193">In the following example:</span></span>

* <span data-ttu-id="9f901-194">Přejděte `MyAppNamespace` do oboru názvů aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f901-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="9f901-195">Pokud se složka s názvem *Components* nepoužívá k ukládání součástí, přejděte `Components` do složky, kde jsou umístěny součásti.</span><span class="sxs-lookup"><span data-stu-id="9f901-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="9f901-196">`_ViewImports.cshtml`Soubor se nachází ve `Pages` složce Razor aplikace Pages nebo ve `Views` složce aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="9f901-196">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="9f901-197">Další informace naleznete v tématu <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="9f901-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>