---
title: Integrace komponent ASP.NET Core Razor do břitvových stránek a aplikací MVC
author: guardrex
description: Seznamte se se scénáři datových Blazor vazeb pro komponenty a prvky modelu DOM v aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 6efa84c550a4605bde5e1f2bca4f2d1aa4a2667b
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123367"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="71de4-103">Integrace komponent ASP.NET Core Razor do břitvových stránek a aplikací MVC</span><span class="sxs-lookup"><span data-stu-id="71de4-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="71de4-104">[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="71de4-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="71de4-105">Komponenty Razor lze integrovat do stránek Razor pages a aplikací MVC.</span><span class="sxs-lookup"><span data-stu-id="71de4-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="71de4-106">Při vykreslení stránky nebo zobrazení lze komponenty předběžně vykreslit současně.</span><span class="sxs-lookup"><span data-stu-id="71de4-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="71de4-107">Příprava aplikace na používání komponent na stránkách a v zobrazeních</span><span class="sxs-lookup"><span data-stu-id="71de4-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="71de4-108">Stávající aplikace Razor Pages nebo MVC dokáže integrovat komponenty Razor do stránek a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="71de4-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="71de4-109">V souboru rozložení aplikace (*_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="71de4-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="71de4-110">Do `<head>` prvku `<base>` přidejte následující značku:</span><span class="sxs-lookup"><span data-stu-id="71de4-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="71de4-111">Hodnota `href` *(cesta základu aplikace*) v předchozím příkladu předpokládá, že aplikace`/`je umístěna na kořenové cestě URL ( ).</span><span class="sxs-lookup"><span data-stu-id="71de4-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="71de4-112">Pokud je aplikace podaplikace, postupujte podle pokynů v části <xref:host-and-deploy/blazor/index#app-base-path> cesta základní *aplikace* článku.</span><span class="sxs-lookup"><span data-stu-id="71de4-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="71de4-113">Soubor *_Layout.cshtml* se nachází ve složce *Stránky/Sdílené* v aplikaci Razor Pages nebo V aplikaci *Zobrazení/Sdílené* v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="71de4-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="71de4-114">Přidejte `<script>` značku pro skript *blazor.server.js* bezprostředně `</body>` před uzavírací značkou:</span><span class="sxs-lookup"><span data-stu-id="71de4-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="71de4-115">Rozhraní framework přidá do aplikace skript *blazor.server.js.*</span><span class="sxs-lookup"><span data-stu-id="71de4-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="71de4-116">Není třeba ručně přidávat skript do aplikace.</span><span class="sxs-lookup"><span data-stu-id="71de4-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="71de4-117">Přidejte soubor *_Imports.razor* do kořenové složky projektu s následujícím obsahem (změňte obor posledního oboru názvů , `MyAppNamespace`do oboru názvů aplikace):</span><span class="sxs-lookup"><span data-stu-id="71de4-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="71de4-118">V `Startup.ConfigureServices`, zaregistrujte službu Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="71de4-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="71de4-119">V `Startup.Configure`doplňku Blazor Hub `app.UseEndpoints`přidejte do doplňku :</span><span class="sxs-lookup"><span data-stu-id="71de4-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="71de4-120">Integrujte součásti do libovolné stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="71de4-120">Integrate components into any page or view.</span></span> <span data-ttu-id="71de4-121">Další informace naleznete v tématu [Render komponenty ze stránky nebo zobrazení](#render-components-from-a-page-or-view) oddílu.</span><span class="sxs-lookup"><span data-stu-id="71de4-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="71de4-122">Použití směrovatelných součástí v aplikaci Razor Pages</span><span class="sxs-lookup"><span data-stu-id="71de4-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="71de4-123">*Tato část se týkajících se přidání komponent, které jsou přímo směrovatelné z požadavků uživatelů.*</span><span class="sxs-lookup"><span data-stu-id="71de4-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="71de4-124">Podpora směrovatelných komponent Razor v aplikacích Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="71de4-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="71de4-125">Postupujte podle pokynů v části [Příprava aplikace na používání komponent na stránkách a v](#prepare-the-app-to-use-components-in-pages-and-views) zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="71de4-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="71de4-126">Přidejte soubor *App.razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="71de4-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="71de4-127">Přidejte do složky *Stránky* soubor *_Host.cshtml* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="71de4-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="71de4-128">Komponenty používají pro své rozložení sdílený soubor *_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="71de4-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="71de4-129"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>konfiguruje, `App` zda součást:</span><span class="sxs-lookup"><span data-stu-id="71de4-129"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="71de4-130">Je prerendered do stránky.</span><span class="sxs-lookup"><span data-stu-id="71de4-130">Is prerendered into the page.</span></span>
   * <span data-ttu-id="71de4-131">Je vykreslen jako statické HTML na stránce, nebo pokud obsahuje potřebné informace k bootstrap aplikace Blazor od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="71de4-131">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="71de4-132">Režim vykreslení</span><span class="sxs-lookup"><span data-stu-id="71de4-132">Render Mode</span></span> | <span data-ttu-id="71de4-133">Popis</span><span class="sxs-lookup"><span data-stu-id="71de4-133">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="71de4-134">Vykreslí `App` komponentu do statického KÓDU Blazor HTML a obsahuje značku pro serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="71de4-134">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="71de4-135">Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace.</span><span class="sxs-lookup"><span data-stu-id="71de4-135">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="71de4-136">Vykreslí značku Blazor pro serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="71de4-136">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="71de4-137">Výstup z `App` komponenty není zahrnut.</span><span class="sxs-lookup"><span data-stu-id="71de4-137">Output from the `App` component isn't included.</span></span> <span data-ttu-id="71de4-138">Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace.</span><span class="sxs-lookup"><span data-stu-id="71de4-138">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="71de4-139">Vykreslí `App` komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="71de4-139">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="71de4-140">Další informace o pomocníku pro <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>označení součásti naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="71de4-140">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="71de4-141">Přidejte trasu s nízkou prioritou pro konfiguraci stránky `Startup.Configure` *_Host.cshtml* do konfigurace koncového bodu v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="71de4-141">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="71de4-142">Přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="71de4-142">Add routable components to the app.</span></span> <span data-ttu-id="71de4-143">Příklad:</span><span class="sxs-lookup"><span data-stu-id="71de4-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="71de4-144">Další informace o oborech názvů naleznete v části [Obory názvů komponenty.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="71de4-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="71de4-145">Použití směrovatelných součástí v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="71de4-145">Use routable components in an MVC app</span></span>

<span data-ttu-id="71de4-146">*Tato část se týkajících se přidání komponent, které jsou přímo směrovatelné z požadavků uživatelů.*</span><span class="sxs-lookup"><span data-stu-id="71de4-146">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="71de4-147">Podpora směrovatelných komponent Razor v aplikacích MVC:</span><span class="sxs-lookup"><span data-stu-id="71de4-147">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="71de4-148">Postupujte podle pokynů v části [Příprava aplikace na používání komponent na stránkách a v](#prepare-the-app-to-use-components-in-pages-and-views) zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="71de4-148">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="71de4-149">Přidejte soubor *App.razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="71de4-149">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="71de4-150">Přidejte soubor *_Host.cshtml* do složky *Zobrazení/Domov* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="71de4-150">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="71de4-151">Komponenty používají pro své rozložení sdílený soubor *_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="71de4-151">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="71de4-152">Přidejte akci do domácího ovladače:</span><span class="sxs-lookup"><span data-stu-id="71de4-152">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="71de4-153">Přidejte trasu s nízkou prioritou pro akci kontroléru, která vrátí `Startup.Configure`zobrazení *_Host.cshtml* do konfigurace koncového bodu v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="71de4-153">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="71de4-154">Vytvořte složku *Stránky* a přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="71de4-154">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="71de4-155">Příklad:</span><span class="sxs-lookup"><span data-stu-id="71de4-155">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="71de4-156">Další informace o oborech názvů naleznete v části [Obory názvů komponenty.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="71de4-156">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="71de4-157">Obory názvů komponent</span><span class="sxs-lookup"><span data-stu-id="71de4-157">Component namespaces</span></span>

<span data-ttu-id="71de4-158">Při použití vlastní složky k uložení součástí aplikace přidejte obor názvů představující složku buď na stránku nebo zobrazení, nebo do souboru *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="71de4-158">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="71de4-159">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="71de4-159">In the following example:</span></span>

* <span data-ttu-id="71de4-160">Změňte `MyAppNamespace` obor názvů aplikace.</span><span class="sxs-lookup"><span data-stu-id="71de4-160">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="71de4-161">Pokud složka s názvem *Components* není použita k `Components` uložení součástí, změňte na složku, ve které jsou součásti umístěny.</span><span class="sxs-lookup"><span data-stu-id="71de4-161">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="71de4-162">Soubor *_ViewImports.cshtml* se nachází ve složce *Stránky* aplikace Razor Pages nebo ve složce *Zobrazení* aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="71de4-162">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="71de4-163">Další informace naleznete v tématu <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="71de4-163">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="71de4-164">Vykreslení součástí ze stránky nebo zobrazení</span><span class="sxs-lookup"><span data-stu-id="71de4-164">Render components from a page or view</span></span>

<span data-ttu-id="71de4-165">*Tato část se týká přidávání součástí na stránky nebo zobrazení, kde součásti nejsou přímo směrovatelné z požadavků uživatelů.*</span><span class="sxs-lookup"><span data-stu-id="71de4-165">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="71de4-166">Chcete-li vykreslit komponentu ze stránky nebo ze zobrazení, použijte [pomocníka pro označení součásti](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="71de4-166">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="71de4-167">Další informace o způsobu vykreslení součástí, stavu `Component` komponent a pomocné značce naleznete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="71de4-167">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
