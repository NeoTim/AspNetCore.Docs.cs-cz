---
title: Integrace komponent ASP.NET Core Razor do břitvových stránek a aplikací MVC
author: guardrex
description: Seznamte se se scénáři datových Blazor vazeb pro komponenty a prvky modelu DOM v aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: cf6056e0985d5433bddecac8dd183ca3f4c2af5b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218931"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="847ce-103">Integrace komponent ASP.NET Core Razor do břitvových stránek a aplikací MVC</span><span class="sxs-lookup"><span data-stu-id="847ce-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="847ce-104">[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="847ce-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="847ce-105">Komponenty Razor lze integrovat do stránek Razor pages a aplikací MVC.</span><span class="sxs-lookup"><span data-stu-id="847ce-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="847ce-106">Při vykreslení stránky nebo zobrazení lze komponenty předběžně vykreslit současně.</span><span class="sxs-lookup"><span data-stu-id="847ce-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="847ce-107">Příprava aplikace na používání komponent na stránkách a v zobrazeních</span><span class="sxs-lookup"><span data-stu-id="847ce-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="847ce-108">Stávající aplikace Razor Pages nebo MVC dokáže integrovat komponenty Razor do stránek a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="847ce-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="847ce-109">V souboru rozložení aplikace (*_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="847ce-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="847ce-110">Do `<head>` prvku `<base>` přidejte následující značku:</span><span class="sxs-lookup"><span data-stu-id="847ce-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="847ce-111">Hodnota `href` *(cesta základu aplikace*) v předchozím příkladu předpokládá, že aplikace`/`je umístěna na kořenové cestě URL ( ).</span><span class="sxs-lookup"><span data-stu-id="847ce-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="847ce-112">Pokud je aplikace podaplikace, postupujte podle pokynů v části <xref:host-and-deploy/blazor/index#app-base-path> cesta základní *aplikace* článku.</span><span class="sxs-lookup"><span data-stu-id="847ce-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="847ce-113">Soubor *_Layout.cshtml* se nachází ve složce *Stránky/Sdílené* v aplikaci Razor Pages nebo V aplikaci *Zobrazení/Sdílené* v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="847ce-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="847ce-114">Přidejte `<script>` značku pro skript *blazor.server.js* bezprostředně `</body>` před uzavírací značkou:</span><span class="sxs-lookup"><span data-stu-id="847ce-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="847ce-115">Rozhraní framework přidá do aplikace skript *blazor.server.js.*</span><span class="sxs-lookup"><span data-stu-id="847ce-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="847ce-116">Není třeba ručně přidávat skript do aplikace.</span><span class="sxs-lookup"><span data-stu-id="847ce-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="847ce-117">Přidejte soubor *_Imports.razor* do kořenové složky projektu s následujícím obsahem (změňte obor posledního oboru názvů , `MyAppNamespace`do oboru názvů aplikace):</span><span class="sxs-lookup"><span data-stu-id="847ce-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="847ce-118">V `Startup.ConfigureServices`, Blazor zaregistrujte serverovou službu:</span><span class="sxs-lookup"><span data-stu-id="847ce-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="847ce-119">V `Startup.Configure`bodě Blazor přidejte koncový `app.UseEndpoints`bod centra do :</span><span class="sxs-lookup"><span data-stu-id="847ce-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="847ce-120">Integrujte součásti do libovolné stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="847ce-120">Integrate components into any page or view.</span></span> <span data-ttu-id="847ce-121">Další informace naleznete v tématu [Render komponenty ze stránky nebo zobrazení](#render-components-from-a-page-or-view) oddílu.</span><span class="sxs-lookup"><span data-stu-id="847ce-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="847ce-122">Použití směrovatelných součástí v aplikaci Razor Pages</span><span class="sxs-lookup"><span data-stu-id="847ce-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="847ce-123">*Tato část se týkajících se přidání komponent, které jsou přímo směrovatelné z požadavků uživatelů.*</span><span class="sxs-lookup"><span data-stu-id="847ce-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="847ce-124">Podpora směrovatelných komponent Razor v aplikacích Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="847ce-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="847ce-125">Postupujte podle pokynů v části [Příprava aplikace na používání komponent na stránkách a v](#prepare-the-app-to-use-components-in-pages-and-views) zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="847ce-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="847ce-126">Přidejte soubor *App.razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="847ce-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="847ce-127">Přidejte do složky *Stránky* soubor *_Host.cshtml* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="847ce-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="847ce-128">Komponenty používají pro své rozložení sdílený soubor *_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="847ce-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="847ce-129">Přidejte trasu s nízkou prioritou pro konfiguraci stránky `Startup.Configure` *_Host.cshtml* do konfigurace koncového bodu v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="847ce-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="847ce-130">Přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="847ce-130">Add routable components to the app.</span></span> <span data-ttu-id="847ce-131">Příklad:</span><span class="sxs-lookup"><span data-stu-id="847ce-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="847ce-132">Další informace o oborech názvů naleznete v části [Obory názvů komponenty.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="847ce-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="847ce-133">Použití směrovatelných součástí v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="847ce-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="847ce-134">*Tato část se týkajících se přidání komponent, které jsou přímo směrovatelné z požadavků uživatelů.*</span><span class="sxs-lookup"><span data-stu-id="847ce-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="847ce-135">Podpora směrovatelných komponent Razor v aplikacích MVC:</span><span class="sxs-lookup"><span data-stu-id="847ce-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="847ce-136">Postupujte podle pokynů v části [Příprava aplikace na používání komponent na stránkách a v](#prepare-the-app-to-use-components-in-pages-and-views) zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="847ce-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="847ce-137">Přidejte soubor *App.razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="847ce-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="847ce-138">Přidejte soubor *_Host.cshtml* do složky *Zobrazení/Domov* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="847ce-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="847ce-139">Komponenty používají pro své rozložení sdílený soubor *_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="847ce-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="847ce-140">Přidejte akci do domácího ovladače:</span><span class="sxs-lookup"><span data-stu-id="847ce-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="847ce-141">Přidejte trasu s nízkou prioritou pro akci kontroléru, která vrátí `Startup.Configure`zobrazení *_Host.cshtml* do konfigurace koncového bodu v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="847ce-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="847ce-142">Vytvořte složku *Stránky* a přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="847ce-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="847ce-143">Příklad:</span><span class="sxs-lookup"><span data-stu-id="847ce-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="847ce-144">Další informace o oborech názvů naleznete v části [Obory názvů komponenty.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="847ce-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="847ce-145">Obory názvů komponent</span><span class="sxs-lookup"><span data-stu-id="847ce-145">Component namespaces</span></span>

<span data-ttu-id="847ce-146">Při použití vlastní složky k uložení součástí aplikace přidejte obor názvů představující složku buď na stránku nebo zobrazení, nebo do souboru *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="847ce-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="847ce-147">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="847ce-147">In the following example:</span></span>

* <span data-ttu-id="847ce-148">Změňte `MyAppNamespace` obor názvů aplikace.</span><span class="sxs-lookup"><span data-stu-id="847ce-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="847ce-149">Pokud složka s názvem *Components* není použita k `Components` uložení součástí, změňte na složku, ve které jsou součásti umístěny.</span><span class="sxs-lookup"><span data-stu-id="847ce-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="847ce-150">Soubor *_ViewImports.cshtml* se nachází ve složce *Stránky* aplikace Razor Pages nebo ve složce *Zobrazení* aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="847ce-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="847ce-151">Další informace naleznete v tématu <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="847ce-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="847ce-152">Vykreslení součástí ze stránky nebo zobrazení</span><span class="sxs-lookup"><span data-stu-id="847ce-152">Render components from a page or view</span></span>

<span data-ttu-id="847ce-153">*Tato část se týká přidávání součástí na stránky nebo zobrazení, kde součásti nejsou přímo směrovatelné z požadavků uživatelů.*</span><span class="sxs-lookup"><span data-stu-id="847ce-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="847ce-154">Chcete-li vykreslit komponentu ze stránky nebo ze zobrazení, použijte [pomocníka pro označení součásti](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="847ce-154">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="847ce-155">Další informace o způsobu vykreslení součástí, stavu `Component` komponent a pomocné značce naleznete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="847ce-155">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
