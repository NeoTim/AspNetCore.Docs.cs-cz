---
title: Integrace součástí ASP.NET Core Razor do Razor Pages a aplikací MVC
author: guardrex
description: Přečtěte si o scénářích datových vazeb pro komponenty a prvky modelu DOM v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: cf6056e0985d5433bddecac8dd183ca3f4c2af5b
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218931"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="58d52-103">Integrace součástí ASP.NET Core Razor do Razor Pages a aplikací MVC</span><span class="sxs-lookup"><span data-stu-id="58d52-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="58d52-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="58d52-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="58d52-105">Komponenty Razor lze integrovat do aplikací Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="58d52-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="58d52-106">Po vykreslení stránky nebo zobrazení mohou být komponenty předem vykresleny ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="58d52-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="58d52-107">Příprava aplikace na používání komponent na stránkách a zobrazeních</span><span class="sxs-lookup"><span data-stu-id="58d52-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="58d52-108">Existující Razor Pages nebo aplikace MVC mohou integrovat součásti Razor do stránek a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="58d52-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="58d52-109">V souboru rozložení aplikace ( *_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="58d52-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="58d52-110">Přidejte následující značku `<base>` do prvku `<head>`:</span><span class="sxs-lookup"><span data-stu-id="58d52-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="58d52-111">Hodnota `href` ( *základní cesta aplikace*) v předchozím příkladu předpokládá, že se aplikace nachází v kořenové cestě URL (`/`).</span><span class="sxs-lookup"><span data-stu-id="58d52-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="58d52-112">Pokud je aplikace podaplikace, postupujte podle pokynů v části *základní cesta k aplikaci* v <xref:host-and-deploy/blazor/index#app-base-path> článku.</span><span class="sxs-lookup"><span data-stu-id="58d52-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="58d52-113">Soubor *_Layout. cshtml* se nachází ve složce *stránky nebo sdílené* složky v aplikaci Razor Pages nebo *zobrazení/sdílená* složka v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="58d52-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="58d52-114">Přidejte značku `<script>` pro skript *blazor. Server. js* těsně před uzavírací značku `</body>`:</span><span class="sxs-lookup"><span data-stu-id="58d52-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="58d52-115">Rozhraní přidá do aplikace skript *blazor. Server. js* .</span><span class="sxs-lookup"><span data-stu-id="58d52-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="58d52-116">Nemusíte ručně přidávat do aplikace skript.</span><span class="sxs-lookup"><span data-stu-id="58d52-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="58d52-117">Přidejte soubor *_Imports. Razor* do kořenové složky projektu s následujícím obsahem (změňte poslední obor názvů `MyAppNamespace`na obor názvů aplikace):</span><span class="sxs-lookup"><span data-stu-id="58d52-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="58d52-118">V `Startup.ConfigureServices`Zaregistrujte službu Blazor serveru:</span><span class="sxs-lookup"><span data-stu-id="58d52-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="58d52-119">V `Startup.Configure`přidejte koncový bod centra Blazor do `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="58d52-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="58d52-120">Integrujte součásti na libovolnou stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="58d52-120">Integrate components into any page or view.</span></span> <span data-ttu-id="58d52-121">Další informace naleznete v části [vykreslení komponent ze stránky nebo zobrazení](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="58d52-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="58d52-122">Použití směrovatelných komponent v aplikaci Razor Pages</span><span class="sxs-lookup"><span data-stu-id="58d52-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="58d52-123">*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="58d52-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="58d52-124">Podpora směrování komponent s více prvky Razor v aplikacích Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="58d52-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="58d52-125">Postupujte podle pokynů v části [Příprava aplikace na používání komponent v částech stránky a zobrazení](#prepare-the-app-to-use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="58d52-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="58d52-126">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="58d52-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="58d52-127">Do složky *Pages* přidejte *_Host soubor. cshtml* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="58d52-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="58d52-128">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58d52-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="58d52-129">Přidejte trasu s nízkou prioritou pro stránku *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="58d52-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="58d52-130">Přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="58d52-130">Add routable components to the app.</span></span> <span data-ttu-id="58d52-131">Například:</span><span class="sxs-lookup"><span data-stu-id="58d52-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="58d52-132">Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="58d52-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="58d52-133">Použití směrovatelných komponent v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="58d52-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="58d52-134">*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="58d52-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="58d52-135">Podpora směrovatelných komponent Razor v aplikacích MVC:</span><span class="sxs-lookup"><span data-stu-id="58d52-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="58d52-136">Postupujte podle pokynů v části [Příprava aplikace na používání komponent v částech stránky a zobrazení](#prepare-the-app-to-use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="58d52-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="58d52-137">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="58d52-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="58d52-138">Přidejte *_Host soubor. cshtml* do *zobrazení/domovské* složky s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="58d52-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="58d52-139">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58d52-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="58d52-140">Přidat akci do domovského kontroleru:</span><span class="sxs-lookup"><span data-stu-id="58d52-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="58d52-141">Přidejte trasu s nízkou prioritou pro akci kontroleru, která vrací zobrazení *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="58d52-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="58d52-142">Vytvořte složku *Pages* a přidejte do ní součásti s funkcí směrování.</span><span class="sxs-lookup"><span data-stu-id="58d52-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="58d52-143">Například:</span><span class="sxs-lookup"><span data-stu-id="58d52-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="58d52-144">Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="58d52-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="58d52-145">Obory názvů součásti</span><span class="sxs-lookup"><span data-stu-id="58d52-145">Component namespaces</span></span>

<span data-ttu-id="58d52-146">Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do stránky nebo zobrazení nebo do souboru *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58d52-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="58d52-147">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="58d52-147">In the following example:</span></span>

* <span data-ttu-id="58d52-148">Změňte `MyAppNamespace` na obor názvů aplikace.</span><span class="sxs-lookup"><span data-stu-id="58d52-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="58d52-149">Pokud se složka s názvem *Components* nepoužívá k ukládání součástí, změňte `Components` do složky, kde jsou umístěny součásti.</span><span class="sxs-lookup"><span data-stu-id="58d52-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="58d52-150">Soubor *_ViewImports. cshtml* je umístěný ve složce *pages* aplikace Razor Pages nebo ve složce *zobrazení* aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="58d52-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="58d52-151">Další informace naleznete v tématu <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="58d52-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="58d52-152">Vykreslení komponent ze stránky nebo zobrazení</span><span class="sxs-lookup"><span data-stu-id="58d52-152">Render components from a page or view</span></span>

<span data-ttu-id="58d52-153">*Tato část se vztahuje na přidávání komponent na stránky nebo zobrazení, kde součásti nejsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="58d52-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="58d52-154">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="58d52-154">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="58d52-155">Další informace o vykreslování komponent, stavu komponent a pomocníka značek `Component` naleznete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="58d52-155">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
