---
<span data-ttu-id="2b3ab-101">title: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-101">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-103">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-104">'Identity'</span></span>
- <span data-ttu-id="2b3ab-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-106">'Razor'</span></span>
- <span data-ttu-id="2b3ab-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-107">'SignalR' uid:</span></span> 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="2b3ab-108">Integrace Razor součástí ASP.NET Core do Razor stránek a aplikací MVC</span><span class="sxs-lookup"><span data-stu-id="2b3ab-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="2b3ab-109">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2b3ab-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="2b3ab-110">komponenty lze integrovat do Razor stránek a aplikací MVC.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-110"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="2b3ab-111">Po vykreslení stránky nebo zobrazení mohou být komponenty předem vykresleny ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="2b3ab-112">Po [přípravě aplikace](#prepare-the-app)použijte pokyny v následujících částech v závislosti na požadavcích aplikace:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="2b3ab-113">Směrovatelné komponenty: pro součásti, které jsou přímo směrovatelné od uživatelských požadavků.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-113">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="2b3ab-114">Postupujte podle těchto pokynů, pokud by Návštěvníci mohli ve svém prohlížeči vytvořit požadavek HTTP pro komponentu s [`@page`](xref:mvc/views/razor#page) direktivou.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="2b3ab-115">[Použití směrovatelných komponent v Razor aplikaci Pages](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="2b3ab-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="2b3ab-116">Použití směrovatelných komponent v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="2b3ab-116">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="2b3ab-117">[Vykreslit součásti ze stránky nebo zobrazení](#render-components-from-a-page-or-view): pro součásti, které přímo Nesměrovatelné od uživatelských požadavků.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="2b3ab-118">Postupujte podle těchto pokynů, pokud aplikace vloží komponenty do existujících stránek a zobrazení pomocí [pomocné rutiny tag komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2b3ab-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="2b3ab-119">Příprava aplikace</span><span class="sxs-lookup"><span data-stu-id="2b3ab-119">Prepare the app</span></span>

<span data-ttu-id="2b3ab-120">Existující Razor stránky nebo aplikace MVC mohou integrovat Razor komponenty do stránek a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="2b3ab-121">V souboru rozložení aplikace (*_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="2b3ab-121">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="2b3ab-122">Přidejte následující `<base>` značku do `<head>` prvku:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-122">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="2b3ab-123">`href`Hodnota ( *základní cesta aplikace*) v předchozím příkladu předpokládá, že se aplikace nachází v kořenové cestě URL ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="2b3ab-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="2b3ab-124">Pokud je aplikace podaplikace, postupujte podle pokynů v části *základní cesta k aplikaci* v <xref:host-and-deploy/blazor/index#app-base-path> článku.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="2b3ab-125">Soubor *_Layout. cshtml* se nachází ve složce *Pages/Shared* v Razor aplikaci Pages nebo v *zobrazení/sdílené* složce v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="2b3ab-126">Přidejte `<script>` značku pro skript *blazor. Server. js* těsně před uzavírací `</body>` značku:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="2b3ab-127">Rozhraní přidá do aplikace skript *blazor. Server. js* .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="2b3ab-128">Nemusíte ručně přidávat do aplikace skript.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-128">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="2b3ab-129">Přidejte soubor *_Imports. Razor* do kořenové složky projektu s následujícím obsahem (změňte poslední obor názvů `MyAppNamespace` na obor názvů aplikace):</span><span class="sxs-lookup"><span data-stu-id="2b3ab-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="2b3ab-130">V nástroji `Startup.ConfigureServices` Zaregistrujte Blazor službu serveru:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="2b3ab-131">Do `Startup.Configure` přidejte Blazor koncový bod centra do `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="2b3ab-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="2b3ab-132">Integrujte součásti na libovolnou stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-132">Integrate components into any page or view.</span></span> <span data-ttu-id="2b3ab-133">Další informace naleznete v části [vykreslení komponent ze stránky nebo zobrazení](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="2b3ab-134">Použití směrovatelných komponent v Razor aplikaci Pages</span><span class="sxs-lookup"><span data-stu-id="2b3ab-134">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="2b3ab-135">*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="2b3ab-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="2b3ab-136">Podpora směrování Razor komponent v Razor aplikacích Pages:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-136">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="2b3ab-137">Postupujte podle pokynů v části [Příprava aplikace](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="2b3ab-138">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-138">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="2b3ab-139">Do složky *Pages* přidejte *_Host soubor. cshtml* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="2b3ab-140">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="2b3ab-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>nakonfiguruje, jestli `App` součást:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="2b3ab-142">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-142">Is prerendered into the page.</span></span>
   * <span data-ttu-id="2b3ab-143">Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="2b3ab-144">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="2b3ab-144">Render Mode</span></span> | <span data-ttu-id="2b3ab-145">Description</span><span class="sxs-lookup"><span data-stu-id="2b3ab-145">Description</span></span> |
   | ---
<span data-ttu-id="2b3ab-146">title: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-147">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-148">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-149">'Identity'</span></span>
- <span data-ttu-id="2b3ab-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-151">'Razor'</span></span>
- <span data-ttu-id="2b3ab-152">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2b3ab-153">title: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-154">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-155">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-156">'Identity'</span></span>
- <span data-ttu-id="2b3ab-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-158">'Razor'</span></span>
- <span data-ttu-id="2b3ab-159">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2b3ab-160">title: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-161">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-162">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-163">'Identity'</span></span>
- <span data-ttu-id="2b3ab-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-165">'Razor'</span></span>
- <span data-ttu-id="2b3ab-166">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-166">'SignalR' uid:</span></span> 

<span data-ttu-id="2b3ab-167">------ | ---Název: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-168">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-169">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-170">'Identity'</span></span>
- <span data-ttu-id="2b3ab-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-172">'Razor'</span></span>
- <span data-ttu-id="2b3ab-173">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2b3ab-174">title: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-175">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-176">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-177">'Identity'</span></span>
- <span data-ttu-id="2b3ab-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-179">'Razor'</span></span>
- <span data-ttu-id="2b3ab-180">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2b3ab-181">title: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-182">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-183">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-184">'Identity'</span></span>
- <span data-ttu-id="2b3ab-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-186">'Razor'</span></span>
- <span data-ttu-id="2b3ab-187">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-187">'SignalR' uid:</span></span> 

<span data-ttu-id="2b3ab-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Vykreslí `App` komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="2b3ab-189">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="2b3ab-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Vykreslí značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="2b3ab-191">Výstup `App` komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-191">Output from the `App` component isn't included.</span></span> <span data-ttu-id="2b3ab-192">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="2b3ab-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Vykreslí `App` komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="2b3ab-194">Další informace o pomocníka značek komponenty naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="2b3ab-195">Přidejte trasu s nízkou prioritou pro stránku *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="2b3ab-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="2b3ab-196">Přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-196">Add routable components to the app.</span></span> <span data-ttu-id="2b3ab-197">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-197">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="2b3ab-198">Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-198">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="2b3ab-199">Použití směrovatelných komponent v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="2b3ab-199">Use routable components in an MVC app</span></span>

<span data-ttu-id="2b3ab-200">*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="2b3ab-200">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="2b3ab-201">Podpora směrování Razor komponent v aplikacích MVC:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-201">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="2b3ab-202">Postupujte podle pokynů v části [Příprava aplikace](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-202">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="2b3ab-203">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-203">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="2b3ab-204">Přidejte *_Host soubor. cshtml* do *zobrazení/domovské* složky s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-204">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="2b3ab-205">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-205">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="2b3ab-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>nakonfiguruje, jestli `App` součást:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="2b3ab-207">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-207">Is prerendered into the page.</span></span>
   * <span data-ttu-id="2b3ab-208">Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-208">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="2b3ab-209">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="2b3ab-209">Render Mode</span></span> | <span data-ttu-id="2b3ab-210">Description</span><span class="sxs-lookup"><span data-stu-id="2b3ab-210">Description</span></span> |
   | ---
<span data-ttu-id="2b3ab-211">title: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-211">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-212">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-213">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-214">'Identity'</span></span>
- <span data-ttu-id="2b3ab-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-216">'Razor'</span></span>
- <span data-ttu-id="2b3ab-217">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2b3ab-218">title: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-218">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-219">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-220">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-221">'Identity'</span></span>
- <span data-ttu-id="2b3ab-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-223">'Razor'</span></span>
- <span data-ttu-id="2b3ab-224">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2b3ab-225">title: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-225">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-226">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-227">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-228">'Identity'</span></span>
- <span data-ttu-id="2b3ab-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-230">'Razor'</span></span>
- <span data-ttu-id="2b3ab-231">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-231">'SignalR' uid:</span></span> 

<span data-ttu-id="2b3ab-232">------ | ---Název: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-232">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-233">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-234">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-235">'Identity'</span></span>
- <span data-ttu-id="2b3ab-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-237">'Razor'</span></span>
- <span data-ttu-id="2b3ab-238">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2b3ab-239">title: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-239">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-240">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-241">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-242">'Identity'</span></span>
- <span data-ttu-id="2b3ab-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-244">'Razor'</span></span>
- <span data-ttu-id="2b3ab-245">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2b3ab-246">title: ' integrace Razor komponent ASP.NET Core do Razor stránek a aplikací MVC ' Autor: Popis: ' informace o scénářích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="2b3ab-246">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="2b3ab-247">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2b3ab-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-248">'Blazor'</span></span>
- <span data-ttu-id="2b3ab-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-249">'Identity'</span></span>
- <span data-ttu-id="2b3ab-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="2b3ab-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2b3ab-251">'Razor'</span></span>
- <span data-ttu-id="2b3ab-252">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-252">'SignalR' uid:</span></span> 

<span data-ttu-id="2b3ab-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Vykreslí `App` komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="2b3ab-254">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="2b3ab-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Vykreslí značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="2b3ab-256">Výstup `App` komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-256">Output from the `App` component isn't included.</span></span> <span data-ttu-id="2b3ab-257">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-257">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="2b3ab-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Vykreslí `App` komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="2b3ab-259">Další informace o pomocníka značek komponenty naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-259">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="2b3ab-260">Přidat akci do domovského kontroleru:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-260">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="2b3ab-261">Přidejte trasu s nízkou prioritou pro akci kontroleru, která vrací zobrazení *_Host. cshtml* do konfigurace koncového bodu v nástroji `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="2b3ab-261">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="2b3ab-262">Vytvořte složku *Pages* a přidejte do ní součásti s funkcí směrování.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-262">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="2b3ab-263">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-263">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="2b3ab-264">Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-264">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="2b3ab-265">Vykreslení komponent ze stránky nebo zobrazení</span><span class="sxs-lookup"><span data-stu-id="2b3ab-265">Render components from a page or view</span></span>

<span data-ttu-id="2b3ab-266">*Tato část se vztahuje na přidávání komponent na stránky nebo zobrazení, kde součásti nejsou přímo směrovatelné od uživatelských požadavků.*</span><span class="sxs-lookup"><span data-stu-id="2b3ab-266">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="2b3ab-267">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2b3ab-267">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="2b3ab-268">Vykreslení stavových interaktivních komponent</span><span class="sxs-lookup"><span data-stu-id="2b3ab-268">Render stateful interactive components</span></span>

<span data-ttu-id="2b3ab-269">Stavové interaktivní komponenty lze přidat na Razor stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-269">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="2b3ab-270">Při vykreslení stránky nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-270">When the page or view renders:</span></span>

* <span data-ttu-id="2b3ab-271">Komponenta je předem vykreslena se stránkou nebo zobrazením.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-271">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="2b3ab-272">Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-272">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="2b3ab-273">Po navázání připojení se vytvoří nový stav součásti SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-273">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="2b3ab-274">Následující Razor stránka vykreslí `Counter` součást:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-274">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="2b3ab-275">Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-275">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="2b3ab-276">Vykreslit neinteraktivní součásti</span><span class="sxs-lookup"><span data-stu-id="2b3ab-276">Render noninteractive components</span></span>

<span data-ttu-id="2b3ab-277">Na následující Razor stránce `Counter` je komponenta staticky vykreslena s počáteční hodnotou, která je zadána pomocí formuláře.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-277">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="2b3ab-278">Vzhledem k tomu, že je komponenta staticky vykreslena, tato součást není interaktivní:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-278">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="2b3ab-279">Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-279">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="2b3ab-280">Obory názvů součásti</span><span class="sxs-lookup"><span data-stu-id="2b3ab-280">Component namespaces</span></span>

<span data-ttu-id="2b3ab-281">Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do stránky nebo zobrazení nebo do souboru *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="2b3ab-281">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2b3ab-282">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2b3ab-282">In the following example:</span></span>

* <span data-ttu-id="2b3ab-283">Přejděte `MyAppNamespace` do oboru názvů aplikace.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-283">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="2b3ab-284">Pokud se složka s názvem *Components* nepoužívá k ukládání součástí, přejděte `Components` do složky, kde jsou umístěny součásti.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-284">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="2b3ab-285">Soubor *_ViewImports. cshtml* je umístěný ve složce *Pages* Razor aplikace Pages nebo ve složce *zobrazení* aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-285">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="2b3ab-286">Další informace naleznete v tématu <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="2b3ab-286">For more information, see <xref:blazor/components#import-components>.</span></span>
