---
title: Konfigurace modelu hostování ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o Blazor konfiguraci modelu hostování, včetně toho, jak integrovat komponenty Razor do aplikací Razor Pages a MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306425"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Konfigurace modelu hostování ASP.NET Core Blazor

Od [Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Tento článek popisuje konfiguraci modelu hostování.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

Od verze ASP.NET Core 3,2 Preview 3 podporuje Blazor WebAssembly konfiguraci z:

* *wwwroot/appSettings. JSON*
* *wwwroot/appSettings. {ENVIRONMENT}. JSON*

V aplikaci hostované v Blazor je [běhové prostředí](xref:fundamentals/environments) stejné jako hodnota serverové aplikace.

Když aplikaci spouštíte místně, prostředí se nastaví na výchozí vývoj. Při publikování aplikace je prostředí standardně v produkčním prostředí. Další informace, včetně postupu konfigurace prostředí, najdete v tématu <xref:fundamentals/environments>.

> [!WARNING]
> Konfigurace v aplikaci WebAssembly v Blazor je viditelná pro uživatele. **Neukládejte tajné klíče aplikace ani přihlašovací údaje v konfiguraci.**

Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline. S [progresivními webovými aplikacemi (PWAs)](xref:blazor/progressive-web-app)můžete aktualizovat pouze konfigurační soubory při vytváření nového nasazení. Úprava konfiguračních souborů mezi nasazeními nemá žádný vliv z těchto důvodů:

* Uživatelé mají verze souborů uložených v mezipaměti, které jsou nadále používány.
* Soubory *Service-Worker. js* a *Service-Worker-assets. js* aplikace PWA je nutné znovu sestavit při kompilaci, který signalizuje aplikaci na další stránce uživatele online, na kterou se aplikace znovu nasadila.

Další informace o tom, jak služba PWAs zpracovává aktualizace na pozadí, najdete v tématu <xref:blazor/progressive-web-app#background-updates>.

## <a name="blazor-server"></a>Blazor Server

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
