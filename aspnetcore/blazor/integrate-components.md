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
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integrace součástí ASP.NET Core Razor do Razor Pages a aplikací MVC

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Komponenty Razor lze integrovat do aplikací Razor Pages a MVC. Po vykreslení stránky nebo zobrazení mohou být komponenty předem vykresleny ve stejnou dobu.

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>Příprava aplikace na používání komponent na stránkách a zobrazeních

Existující Razor Pages nebo aplikace MVC mohou integrovat součásti Razor do stránek a zobrazení:

1. V souboru rozložení aplikace ( *_Layout. cshtml*):

   * Přidejte následující značku `<base>` do prvku `<head>`:

     ```html
     <base href="~/" />
     ```

     Hodnota `href` ( *základní cesta aplikace*) v předchozím příkladu předpokládá, že se aplikace nachází v kořenové cestě URL (`/`). Pokud je aplikace podaplikace, postupujte podle pokynů v části *základní cesta k aplikaci* v <xref:host-and-deploy/blazor/index#app-base-path> článku.

     Soubor *_Layout. cshtml* se nachází ve složce *stránky nebo sdílené* složky v aplikaci Razor Pages nebo *zobrazení/sdílená* složka v aplikaci MVC.

   * Přidejte značku `<script>` pro skript *blazor. Server. js* těsně před uzavírací značku `</body>`:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Rozhraní přidá do aplikace skript *blazor. Server. js* . Nemusíte ručně přidávat do aplikace skript.

1. Přidejte soubor *_Imports. Razor* do kořenové složky projektu s následujícím obsahem (změňte poslední obor názvů `MyAppNamespace`na obor názvů aplikace):

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

1. V `Startup.ConfigureServices`Zaregistrujte službu Blazor serveru:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. V `Startup.Configure`přidejte koncový bod centra Blazor do `app.UseEndpoints`:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integrujte součásti na libovolnou stránku nebo zobrazení. Další informace naleznete v části [vykreslení komponent ze stránky nebo zobrazení](#render-components-from-a-page-or-view) .

## <a name="use-routable-components-in-a-razor-pages-app"></a>Použití směrovatelných komponent v aplikaci Razor Pages

*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*

Podpora směrování komponent s více prvky Razor v aplikacích Razor Pages:

1. Postupujte podle pokynů v části [Příprava aplikace na používání komponent v částech stránky a zobrazení](#prepare-the-app-to-use-components-in-pages-and-views) .

1. Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:

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

1. Do složky *Pages* přidejte *_Host soubor. cshtml* s následujícím obsahem:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .

1. Přidejte trasu s nízkou prioritou pro stránku *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Přidejte do aplikace směrovatelné součásti. Například:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .

## <a name="use-routable-components-in-an-mvc-app"></a>Použití směrovatelných komponent v aplikaci MVC

*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*

Podpora směrovatelných komponent Razor v aplikacích MVC:

1. Postupujte podle pokynů v části [Příprava aplikace na používání komponent v částech stránky a zobrazení](#prepare-the-app-to-use-components-in-pages-and-views) .

1. Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:

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

1. Přidejte *_Host soubor. cshtml* do *zobrazení/domovské* složky s následujícím obsahem:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .

1. Přidat akci do domovského kontroleru:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Přidejte trasu s nízkou prioritou pro akci kontroleru, která vrací zobrazení *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Vytvořte složku *Pages* a přidejte do ní součásti s funkcí směrování. Například:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .

## <a name="component-namespaces"></a>Obory názvů součásti

Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do stránky nebo zobrazení nebo do souboru *_ViewImports. cshtml* . V následujícím příkladu:

* Změňte `MyAppNamespace` na obor názvů aplikace.
* Pokud se složka s názvem *Components* nepoužívá k ukládání součástí, změňte `Components` do složky, kde jsou umístěny součásti.

```cshtml
@using MyAppNamespace.Components
```

Soubor *_ViewImports. cshtml* je umístěný ve složce *pages* aplikace Razor Pages nebo ve složce *zobrazení* aplikace MVC.

Další informace naleznete v tématu <xref:blazor/components#import-components>.

## <a name="render-components-from-a-page-or-view"></a>Vykreslení komponent ze stránky nebo zobrazení

*Tato část se vztahuje na přidávání komponent na stránky nebo zobrazení, kde součásti nejsou přímo směrovatelné od uživatelských požadavků.*

Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

Další informace o vykreslování komponent, stavu komponent a pomocníka značek `Component` naleznete v následujících článcích:

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
