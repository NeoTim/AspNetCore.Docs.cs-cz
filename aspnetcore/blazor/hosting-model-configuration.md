---
title: konfigurace Blazor modelu ASP.NET hostingu
author: guardrex
description: Přečtěte Blazor si o hostování konfigurace modelu, včetně toho, jak integrovat komponenty Razor do stránek Razor a aplikací MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306425"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET konfigurace hostingového modelu Core Blazor

Podle [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Tento článek popisuje konfiguraci hostitelského modelu.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

Od vydání ASP.NET Core 3.2 Preview 3 podporuje Blazor WebAssembly konfiguraci z:

* *wwwroot/appsettings.json*
* *wwwroot/appsettings. {PROSTŘEDÍ}.json*

V aplikaci Blazor Hosted je [runtime prostředí](xref:fundamentals/environments) stejné jako hodnota serverové aplikace.

Při spuštění aplikace místně, prostředí výchozí vývoj. Po publikování aplikace prostředí výchozí produkční. Další informace, včetně konfigurace prostředí, <xref:fundamentals/environments>naleznete v tématu .

> [!WARNING]
> Konfigurace v aplikaci Blazor WebAssembly je viditelná pro uživatele. **Neuklápejte tajné kódy aplikací nebo přihlašovací údaje v konfiguraci.**

Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline. Pomocí [progresivních webových aplikací (PWA)](xref:blazor/progressive-web-app)můžete aktualizovat konfigurační soubory pouze při vytváření nového nasazení. Úpravy konfiguračních souborů mezi nasazeními nemají žádný vliv, protože:

* Uživatelé mají uložené verze souborů, které nadále používají, uložené v mezipaměti.
* Pwa *service-worker.js* a *service-worker-assets.js* soubory musí být znovu sestaveny na kompilaci, které signalizují aplikaci na další online návštěvu uživatele, že aplikace byla znovu nasazena.

Další informace o tom, jak pwa <xref:blazor/progressive-web-app#background-updates>zpracovávají aktualizace na pozadí, naleznete v tématu .

## <a name="blazor-server"></a>Blazor Server

### <a name="reflect-the-connection-state-in-the-ui"></a>Odrážet stav připojení v ui

Když klient zjistí, že připojení bylo ztraceno, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit. Pokud se opětovné připojení nezdaří, uživatel i možnost opakovat.

Chcete-li přizpůsobit ui, definujte `components-reconnect-modal` prvek `<body>` s `id` v *_Host.cshtml* Razor stránky:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Následující tabulka popisuje třídy CSS `components-reconnect-modal` použité pro prvek.

| Třída CSS                       | Označuje&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Ztracené spojení. Klient se pokouší znovu připojit. Zobrazit modální. |
| `components-reconnect-hide`     | K serveru je obnoveno aktivní připojení. Skryjte modální. |
| `components-reconnect-failed`   | Opětovné připojení se nezdařilo, pravděpodobně z důvodu selhání sítě. Chcete-li se `window.Blazor.reconnect()`pokusit o opětovné připojení, volejte . |
| `components-reconnect-rejected` | Opětovné připojení bylo odmítnuto. Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen. Chcete-li aplikaci `location.reload()`znovu načíst, zavolejte . Tento stav připojení může vyústit v těchto možnostech:<ul><li>Dojde k chybě v obvodu na straně serveru.</li><li>Klient je odpojen dostatečně dlouho na to, aby server přetavit stav uživatele. Instance součástí, se kterými uživatel pracuje, jsou vyřazeny.</li><li>Server je restartován nebo pracovní proces aplikace je recyklován.</li></ul> |

### <a name="render-mode"></a>Režim vykreslení

Aplikace Blazor Server jsou ve výchozím nastavení nastaveny tak, aby předběžně vykreslovaly uI na serveru před navázáním připojení klienta k serveru. Toto nastavení je nastaveno na stránce *_Host.cshtml* Razor:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`konfiguruje, zda součást:

* Je prerendered do stránky.
* Je vykreslen jako statické HTML na stránce, nebo pokud obsahuje potřebné informace k bootstrap aplikace Blazor od uživatelského agenta.

| `RenderMode`        | Popis |
| ------------------- | ----------- |
| `ServerPrerendered` | Vykreslí komponentu do statického KÓDU Blazor HTML a obsahuje značku pro serverovou aplikaci. Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace. |
| `Server`            | Vykreslí značku Blazor pro serverovou aplikaci. Výstup z komponenty není zahrnut. Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace. |
| `Static`            | Vykreslí komponentu do statického HTML. |

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Vykreslení stavových interaktivních komponent ze stránek a zobrazení Razor

Stavové interaktivní komponenty lze přidat na stránku Razor nebo do zobrazení.

Když se stránka nebo zobrazení vykreslí:

* Komponenta je předobrazována pomocí stránky nebo zobrazení.
* Počáteční stav součásti použitý pro předběžné vykreslování je ztracen.
* Nový stav součásti SignalR je vytvořen při navázání připojení.

Následující stránka Razor vykreslí komponentu: `Counter`

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Vykreslení neinteraktivních komponent ze stránek a zobrazení Razor

Na následující stránce Razor `Counter` je komponenta staticky vykreslena s počáteční hodnotou, která je určena pomocí formuláře:

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

Vzhledem k tomu, `MyComponent` že je staticky vykreslen, komponenta nemůže být interaktivní.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Konfigurace SignalR klienta Blazor pro serverové aplikace

Někdy je třeba nakonfigurovat klienta SignalR používaného serverovými aplikacemi. Blazor Můžete například nakonfigurovat protokolování SignalR klienta tak, aby diagnostikoval problém s připojením.

Postup konfigurace SignalR klienta v souboru *Pages/_Host.cshtml:*

* Přidejte `autostart="false"` atribut `<script>` ke značce skriptu. `blazor.server.js`
* Volání `Blazor.start` a předání v objektu SignalR konfigurace, který určuje tvůrce.

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
