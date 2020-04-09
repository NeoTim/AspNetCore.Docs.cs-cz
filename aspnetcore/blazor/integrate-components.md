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
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integrace komponent ASP.NET Core Razor do břitvových stránek a aplikací MVC

[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)

Komponenty Razor lze integrovat do stránek Razor pages a aplikací MVC. Při vykreslení stránky nebo zobrazení lze komponenty předběžně vykreslit současně.

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>Příprava aplikace na používání komponent na stránkách a v zobrazeních

Stávající aplikace Razor Pages nebo MVC dokáže integrovat komponenty Razor do stránek a zobrazení:

1. V souboru rozložení aplikace (*_Layout.cshtml*):

   * Do `<head>` prvku `<base>` přidejte následující značku:

     ```html
     <base href="~/" />
     ```

     Hodnota `href` *(cesta základu aplikace*) v předchozím příkladu předpokládá, že aplikace`/`je umístěna na kořenové cestě URL ( ). Pokud je aplikace podaplikace, postupujte podle pokynů v části <xref:host-and-deploy/blazor/index#app-base-path> cesta základní *aplikace* článku.

     Soubor *_Layout.cshtml* se nachází ve složce *Stránky/Sdílené* v aplikaci Razor Pages nebo V aplikaci *Zobrazení/Sdílené* v aplikaci MVC.

   * Přidejte `<script>` značku pro skript *blazor.server.js* bezprostředně `</body>` před uzavírací značkou:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Rozhraní framework přidá do aplikace skript *blazor.server.js.* Není třeba ručně přidávat skript do aplikace.

1. Přidejte soubor *_Imports.razor* do kořenové složky projektu s následujícím obsahem (změňte obor posledního oboru názvů , `MyAppNamespace`do oboru názvů aplikace):

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

1. V `Startup.ConfigureServices`, Blazor zaregistrujte serverovou službu:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. V `Startup.Configure`bodě Blazor přidejte koncový `app.UseEndpoints`bod centra do :

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integrujte součásti do libovolné stránky nebo zobrazení. Další informace naleznete v tématu [Render komponenty ze stránky nebo zobrazení](#render-components-from-a-page-or-view) oddílu.

## <a name="use-routable-components-in-a-razor-pages-app"></a>Použití směrovatelných součástí v aplikaci Razor Pages

*Tato část se týkajících se přidání komponent, které jsou přímo směrovatelné z požadavků uživatelů.*

Podpora směrovatelných komponent Razor v aplikacích Razor Pages:

1. Postupujte podle pokynů v části [Příprava aplikace na používání komponent na stránkách a v](#prepare-the-app-to-use-components-in-pages-and-views) zobrazeních.

1. Přidejte soubor *App.razor* do kořenového adresáře projektu s následujícím obsahem:

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

1. Přidejte do složky *Stránky* soubor *_Host.cshtml* s následujícím obsahem:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenty používají pro své rozložení sdílený soubor *_Layout.cshtml.*

1. Přidejte trasu s nízkou prioritou pro konfiguraci stránky `Startup.Configure` *_Host.cshtml* do konfigurace koncového bodu v aplikaci :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Přidejte do aplikace směrovatelné součásti. Příklad:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Další informace o oborech názvů naleznete v části [Obory názvů komponenty.](#component-namespaces)

## <a name="use-routable-components-in-an-mvc-app"></a>Použití směrovatelných součástí v aplikaci MVC

*Tato část se týkajících se přidání komponent, které jsou přímo směrovatelné z požadavků uživatelů.*

Podpora směrovatelných komponent Razor v aplikacích MVC:

1. Postupujte podle pokynů v části [Příprava aplikace na používání komponent na stránkách a v](#prepare-the-app-to-use-components-in-pages-and-views) zobrazeních.

1. Přidejte soubor *App.razor* do kořenového adresáře projektu s následujícím obsahem:

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

1. Přidejte soubor *_Host.cshtml* do složky *Zobrazení/Domov* s následujícím obsahem:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenty používají pro své rozložení sdílený soubor *_Layout.cshtml.*

1. Přidejte akci do domácího ovladače:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Přidejte trasu s nízkou prioritou pro akci kontroléru, která vrátí `Startup.Configure`zobrazení *_Host.cshtml* do konfigurace koncového bodu v aplikaci :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Vytvořte složku *Stránky* a přidejte do aplikace směrovatelné součásti. Příklad:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Další informace o oborech názvů naleznete v části [Obory názvů komponenty.](#component-namespaces)

## <a name="component-namespaces"></a>Obory názvů komponent

Při použití vlastní složky k uložení součástí aplikace přidejte obor názvů představující složku buď na stránku nebo zobrazení, nebo do souboru *_ViewImports.cshtml.* V následujícím příkladu:

* Změňte `MyAppNamespace` obor názvů aplikace.
* Pokud složka s názvem *Components* není použita k `Components` uložení součástí, změňte na složku, ve které jsou součásti umístěny.

```cshtml
@using MyAppNamespace.Components
```

Soubor *_ViewImports.cshtml* se nachází ve složce *Stránky* aplikace Razor Pages nebo ve složce *Zobrazení* aplikace MVC.

Další informace naleznete v tématu <xref:blazor/components#import-components>.

## <a name="render-components-from-a-page-or-view"></a>Vykreslení součástí ze stránky nebo zobrazení

*Tato část se týká přidávání součástí na stránky nebo zobrazení, kde součásti nejsou přímo směrovatelné z požadavků uživatelů.*

Chcete-li vykreslit komponentu ze stránky nebo ze zobrazení, použijte [pomocníka pro označení součásti](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

Další informace o způsobu vykreslení součástí, stavu `Component` komponent a pomocné značce naleznete v následujících článcích:

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
