---
title: Konfigurace modelu hostování ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o Blazor konfiguraci modelu hostování, včetně toho, jak integrovat komponenty Razor do aplikací Razor Pages a MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 91dd1aa32bb5165845eb4794377a50ccbd01adc3
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77215058"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Konfigurace modelu hostování ASP.NET Core Blazor

Od [Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Tento článek popisuje konfiguraci modelu hostování.

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Blazor Server

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a>Integrace součástí Razor do aplikací Razor Pages a MVC

#### <a name="use-components-in-pages-and-views"></a>Použití součástí na stránkách a pohledech

Existující Razor Pages nebo aplikace MVC mohou integrovat součásti Razor do stránek a zobrazení:

1. V souboru rozložení aplikace ( *_Layout. cshtml*):

   * Přidejte následující značku `<base>` do prvku `<head>`:

     ```html
     <base href="~/" />
     ```

     Hodnota `href` ( *základní cesta aplikace*) v předchozím příkladu předpokládá, že se aplikace nachází v kořenové cestě URL (`/`). Pokud je aplikace podaplikace, postupujte podle pokynů v části *základní cesta k aplikaci* v <xref:host-and-deploy/blazor/index#app-base-path> článku.

     Soubor *_Layout. cshtml* se nachází ve složce *stránky nebo sdílené* složky v aplikaci Razor Pages nebo *zobrazení/sdílená* složka v aplikaci MVC.

   * Přidejte značku `<script>` pro skript *blazor. Server. js* přímo před uzavírací značku `</body>`:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Rozhraní přidá do aplikace skript *blazor. Server. js* . Nemusíte ručně přidávat do aplikace skript.

1. Přidejte soubor *_Imports. Razor* do kořenové složky projektu s následujícím obsahem (změňte poslední obor názvů `MyAppNamespace`na obor názvů aplikace):

   ```csharp
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

1. Integrujte součásti na libovolnou stránku nebo zobrazení. Další informace najdete v části *integrace součástí do Razor Pages a MVC Apps* v článku <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.

#### <a name="use-routable-components-in-a-razor-pages-app"></a>Použití směrovatelných komponent v aplikaci Razor Pages

Podpora směrování komponent s více prvky Razor v aplikacích Razor Pages:

1. Postupujte podle pokynů v části [použití součástí v části stránky a zobrazení](#use-components-in-pages-and-views) .

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

1. Přidejte do aplikace směrovatelné součásti. Příklad:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do souboru *Pages/_ViewImports. cshtml* . Další informace najdete v tématu <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.

#### <a name="use-routable-components-in-an-mvc-app"></a>Použití směrovatelných komponent v aplikaci MVC

Podpora směrovatelných komponent Razor v aplikacích MVC:

1. Postupujte podle pokynů v části [použití součástí v části stránky a zobrazení](#use-components-in-pages-and-views) .

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

1. Vytvořte složku *Pages* a přidejte do ní součásti s funkcí směrování. Příklad:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do souboru *views/_ViewImports. cshtml* . Další informace najdete v tématu <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.

### <a name="reflect-the-connection-state-in-the-ui"></a>Odrážet stav připojení v uživatelském rozhraní

Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit. Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.

Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `id` `components-reconnect-modal` v `<body>` stránky *_Host. cshtml* Razor:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.

| CSS – třída                       | Indikuje&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Ztracené připojení. Klient se pokouší znovu připojit. Zobrazit modální okno. |
| `components-reconnect-hide`     | K serveru se znovu naváže aktivní připojení. Skryje modální okno. |
| `components-reconnect-failed`   | Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě. Chcete-li se pokusit o opětovné připojení, zavolejte `window.Blazor.reconnect()`. |
| `components-reconnect-rejected` | Opětovné připojení bylo zamítnuto. Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen. Chcete-li aplikaci znovu načíst, zavolejte `location.reload()`. Tento stav připojení může mít za následek:<ul><li>Dojde k chybě okruhu na straně serveru.</li><li>Klient je dostatečně odpojený, aby server vynechal stav uživatele. Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</li><li>Server se restartuje nebo se pracovní proces aplikace recykluje.</li></ul> |

### <a name="render-mode"></a>Režim vykreslování

Aplikace Blazor serveru se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru. To je nastaveno na stránce *_Host. cshtml* Razor:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode` nakonfiguruje, jestli součást:

* Je předem vykreslen na stránku.
* Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.

| `RenderMode`        | Popis |
| ------------------- | ----------- |
| `ServerPrerendered` | Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. |
| `Server`            | Vykreslí značku pro aplikaci Blazor serveru. Výstup komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. |
| `Static`            | Vykreslí komponentu do statického HTML. |

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor

Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.

Při vykreslení stránky nebo zobrazení:

* Komponenta je předem vykreslena se stránkou nebo zobrazením.
* Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.
* Po navázání SignalRho připojení se vytvoří nový stav součásti.

Následující stránka Razor vykresluje komponentu `Counter`:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Vykreslování neinteraktivních komponent ze stránek a zobrazení Razor

Na následující stránce Razor je součást `Counter` staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Vzhledem k tomu, že `MyComponent` jsou staticky vykresleny, nemůže být komponenta interaktivní.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Konfigurace klienta SignalR pro aplikace Blazor serveru

V některých případech je třeba nakonfigurovat klienta SignalR používaného serverovými aplikacemi Blazor. Například můžete chtít nakonfigurovat protokolování klienta SignalR pro diagnostiku potíží s připojením.

Konfigurace klienta SignalR v souboru *Pages/_Host. cshtml* :

* Přidejte atribut `autostart="false"` do značky `<script>` pro skript `blazor.server.js`.
* Zavolejte `Blazor.start` a předejte objekt konfigurace, který určuje SignalR Builder.

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
