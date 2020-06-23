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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: 1c71067528fb34ab141bb1ee846716834204ee40
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242456"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integrace Razor součástí ASP.NET Core do Razor stránek a aplikací MVC

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Razorkomponenty lze integrovat do Razor stránek a aplikací MVC. Po vykreslení stránky nebo zobrazení mohou být komponenty předem vykresleny ve stejnou dobu.

Po [přípravě aplikace](#prepare-the-app)použijte pokyny v následujících částech v závislosti na požadavcích aplikace:

* Směrovatelné komponenty: pro součásti, které jsou přímo směrovatelné od uživatelských požadavků. Postupujte podle těchto pokynů, pokud by Návštěvníci mohli ve svém prohlížeči vytvořit požadavek HTTP pro komponentu s [`@page`](xref:mvc/views/razor#page) direktivou.
  * [Použití směrovatelných komponent v Razor aplikaci Pages](#use-routable-components-in-a-razor-pages-app)
  * [Použití směrovatelných komponent v aplikaci MVC](#use-routable-components-in-an-mvc-app)
* [Vykreslit součásti ze stránky nebo zobrazení](#render-components-from-a-page-or-view): pro součásti, které přímo Nesměrovatelné od uživatelských požadavků. Postupujte podle těchto pokynů, pokud aplikace vloží komponenty do existujících stránek a zobrazení pomocí [pomocné rutiny tag komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="prepare-the-app"></a>Příprava aplikace

Existující Razor stránky nebo aplikace MVC mohou integrovat Razor komponenty do stránek a zobrazení:

1. V souboru rozložení aplikace ( `_Layout.cshtml` ):

   * Přidejte následující `<base>` značku do `<head>` prvku:

     ```html
     <base href="~/" />
     ```

     `href`Hodnota ( *základní cesta aplikace*) v předchozím příkladu předpokládá, že se aplikace nachází v kořenové cestě URL ( `/` ). Pokud je aplikace podaplikace, postupujte podle pokynů v části *základní cesta k aplikaci* v <xref:blazor/host-and-deploy/index#app-base-path> článku.

     `_Layout.cshtml`Soubor se nachází ve složce *stránky/sdílená* složka v Razor aplikaci stránky nebo *zobrazení/sdílená* složka v aplikaci MVC.

   * Přidejte `<script>` značku pro skript *blazor.server.js* těsně před uzavírací `</body>` značku:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Rozhraní přidá do aplikace skript *blazor.server.js* . Nemusíte ručně přidávat do aplikace skript.

1. Přidejte `_Imports.razor` soubor do kořenové složky projektu s následujícím obsahem (změňte poslední obor názvů `MyAppNamespace` na obor názvů aplikace):

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

1. V nástroji `Startup.ConfigureServices` Zaregistrujte Blazor službu serveru:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Do `Startup.Configure` přidejte Blazor koncový bod centra do `app.UseEndpoints` :

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integrujte součásti na libovolnou stránku nebo zobrazení. Další informace naleznete v části [vykreslení komponent ze stránky nebo zobrazení](#render-components-from-a-page-or-view) .

## <a name="use-routable-components-in-a-razor-pages-app"></a>Použití směrovatelných komponent v Razor aplikaci Pages

*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*

Podpora směrování Razor komponent v Razor aplikacích Pages:

1. Postupujte podle pokynů v části [Příprava aplikace](#prepare-the-app) .

1. `App.razor`Do kořenového adresáře projektu přidejte soubor s následujícím obsahem:

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

1. Přidejte `_Host.cshtml` do `Pages` složky soubor s následujícím obsahem:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenty používají `_Layout.cshtml` pro své rozložení sdílený soubor.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>nakonfiguruje, jestli `App` součást:

   * Je předem vykreslen na stránku.
   * Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.

   | Režim vykreslování | Description |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Vykreslí `App` komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Vykreslí značku pro Blazor serverovou aplikaci. Výstup `App` komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Vykreslí `App` komponentu do statického HTML. |

   Další informace o pomocníka značek komponenty naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

1. Přidejte do `_Host.cshtml` Konfigurace koncového bodu v nástroji trasu s nízkou prioritou `Startup.Configure` :

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

Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .

## <a name="use-routable-components-in-an-mvc-app"></a>Použití směrovatelných komponent v aplikaci MVC

*Tato část se týká přidávání komponent, které jsou přímo směrovatelné od uživatelských požadavků.*

Podpora směrování Razor komponent v aplikacích MVC:

1. Postupujte podle pokynů v části [Příprava aplikace](#prepare-the-app) .

1. Přidejte `App.razor` soubor do kořenového adresáře projektu s následujícím obsahem:

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

1. Přidejte `_Host.cshtml` do `Views/Home` složky soubor s následujícím obsahem:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenty používají `_Layout.cshtml` pro své rozložení sdílený soubor.
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>nakonfiguruje, jestli `App` součást:

   * Je předem vykreslen na stránku.
   * Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.

   | Režim vykreslování | Description |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Vykreslí `App` komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Vykreslí značku pro Blazor serverovou aplikaci. Výstup `App` komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Vykreslí `App` komponentu do statického HTML. |

   Další informace o pomocníka značek komponenty naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

1. Přidat akci do domovského kontroleru:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Přidejte trasu s nízkou prioritou pro akci kontroleru, která vrátí `_Host.cshtml` zobrazení do konfigurace koncového bodu v nástroji `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Vytvořte `Pages` složku a přidejte do ní komponenty s funkcí směrování. Příklad:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Další informace o oborech názvů najdete v části věnované [oborům názvů komponent](#component-namespaces) .

## <a name="render-components-from-a-page-or-view"></a>Vykreslení komponent ze stránky nebo zobrazení

*Tato část se vztahuje na přidávání komponent na stránky nebo zobrazení, kde součásti nejsou přímo směrovatelné od uživatelských požadavků.*

Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

### <a name="render-stateful-interactive-components"></a>Vykreslení stavových interaktivních komponent

Stavové interaktivní komponenty lze přidat na Razor stránku nebo zobrazení.

Při vykreslení stránky nebo zobrazení:

* Komponenta je předem vykreslena se stránkou nebo zobrazením.
* Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.
* Po navázání připojení se vytvoří nový stav součásti SignalR .

Následující Razor stránka vykreslí `Counter` součást:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Vykreslit neinteraktivní součásti

Na následující Razor stránce `Counter` je komponenta staticky vykreslena s počáteční hodnotou, která je zadána pomocí formuláře. Vzhledem k tomu, že je komponenta staticky vykreslena, tato součást není interaktivní:

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

Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Obory názvů součásti

Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do stránky nebo zobrazení nebo do `_ViewImports.cshtml` souboru. V následujícím příkladu:

* Přejděte `MyAppNamespace` do oboru názvů aplikace.
* Pokud se složka s názvem *Components* nepoužívá k ukládání součástí, přejděte `Components` do složky, kde jsou umístěny součásti.

```cshtml
@using MyAppNamespace.Components
```

`_ViewImports.cshtml`Soubor se nachází ve `Pages` složce Razor aplikace Pages nebo ve `Views` složce aplikace MVC.

Další informace naleznete v tématu <xref:blazor/components/index#namespaces>.
