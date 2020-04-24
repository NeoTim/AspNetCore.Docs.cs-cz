---
title: ASP.NET Core Blazor konfigurace modelu hostování
author: guardrex
description: Přečtěte Blazor si o konfiguraci modelu hostování, včetně toho, jak integrovat komponenty Razor do aplikací Razor Pages a MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: cf5776109368dc7353d7e21bcad1e947561e7eb4
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111055"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Konfigurace modelu hostování ASP.NET Core Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Tento článek popisuje konfiguraci modelu hostování.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

### <a name="environment"></a>Prostředí

Při místním spuštění aplikace je prostředí standardně vyvíjené. Při publikování aplikace je prostředí standardně v produkčním prostředí.

Hostovaná aplikace WebAssembly Blazor vybírá prostředí ze serveru prostřednictvím middlewaru, který toto prostředí komunikuje do prohlížeče přidáním `blazor-environment` hlavičky. Hodnota hlavičky je prostředí. Hostovaná aplikace Blazor a serverová aplikace sdílejí stejné prostředí. Další informace, včetně postupu konfigurace prostředí, najdete v tématu <xref:fundamentals/environments>.

Pro samostatnou spuštěnou aplikaci, která je spuštěna místně, `blazor-environment` vývojový server přidá hlavičku pro určení vývojového prostředí. Chcete-li určit prostředí pro jiná hostující prostředí, přidejte `blazor-environment` hlavičku.

V následujícím příkladu pro službu IIS přidejte vlastní hlavičku do publikovaného souboru *Web. config* . Soubor *Web. config* je umístěný ve složce *bin/Release/{Target Framework}/Publish* :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Chcete-li použít vlastní soubor *Web. config* pro službu IIS, který není po publikování aplikace ve složce pro *publikování* přepsán, <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>Přečtěte si téma.

Získání prostředí aplikace v součásti vložením `IWebAssemblyHostEnvironment` a čtením `Environment` vlastnosti:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Během spouštění `WebAssemblyHostBuilder` zpřístupňuje `IWebAssemblyHostEnvironment` `HostEnvironment` vlastnost prostřednictvím vlastnosti, která vývojářům umožňuje mít v kódu logiku konkrétního prostředí:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Následující rozšiřující metody umožňují kontrolu aktuálního prostředí pro názvy pro vývoj, produkci, přípravu a vlastní prostředí:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* "Prostředí" ("{název prostředí}")

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

`IWebAssemblyHostEnvironment.BaseAddress` Vlastnost lze použít při spuštění, když není `NavigationManager` služba k dispozici.

### <a name="configuration"></a>Konfigurace

Blazor WebAssembly podporuje konfiguraci z:

* [Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider) pro soubory nastavení aplikace ve výchozím nastavení:
  * *wwwroot/appSettings. JSON*
  * *wwwroot/appSettings. {ENVIRONMENT}. JSON*
* Další [poskytovatelé konfigurace](xref:fundamentals/configuration/index) zaregistrované aplikací

> [!WARNING]
> Konfigurace v aplikaci WebAssembly v Blazor je viditelná pro uživatele. **Neukládejte tajné klíče aplikace ani přihlašovací údaje v konfiguraci.**

Další informace o poskytovatelích konfigurace najdete v <xref:fundamentals/configuration/index>tématu.

#### <a name="app-settings-configuration"></a>Konfigurace nastavení aplikace

*wwwroot/appSettings. JSON*:

```json
{
  "message": "Hello from config!"
}
```

Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>Konfigurace zprostředkovatele

Následující příklad používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> a [zprostředkovatele konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider) k poskytnutí další konfigurace:

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration
    .Add(memoryConfig)
    .AddJsonFile("cars.json", optional: false, reloadOnChange: true);
```

Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</p>
    <li>Brand: @Configuration["wheels:brand"]</p>
    <li>Type: @Configuration["wheels:brand:type"]</p>
    <li>Year: @Configuration["wheels:year"]</p>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

#### <a name="authentication-configuration"></a>Konfigurace ověřování

*wwwroot/appSettings. JSON*:

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a>Konfigurace protokolování

*wwwroot/appSettings. JSON*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

`Program.Main`:

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>Konfigurace tvůrce hostitele

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Konfigurace uložená v mezipaměti

Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline. S [progresivními webovými aplikacemi (PWAs)](xref:blazor/progressive-web-app)můžete aktualizovat pouze konfigurační soubory při vytváření nového nasazení. Úprava konfiguračních souborů mezi nasazeními nemá žádný vliv z těchto důvodů:

* Uživatelé mají verze souborů uložených v mezipaměti, které jsou nadále používány.
* Soubory *Service-Worker. js* a *Service-Worker-assets. js* aplikace PWA je nutné znovu sestavit při kompilaci, který signalizuje aplikaci na další stránce uživatele online, na kterou se aplikace znovu nasadila.

Další informace o tom, jak služba PWAs zpracovává aktualizace na pozadí, <xref:blazor/progressive-web-app#background-updates>naleznete v tématu.

### <a name="logging"></a>protokolování

Informace o podpoře protokolování WebAssembly v Blazor naleznete v <xref:fundamentals/logging/index#create-logs-in-blazor>tématu.

## <a name="blazor-server"></a>Blazor Server

### <a name="reflect-the-connection-state-in-the-ui"></a>Odrážet stav připojení v uživatelském rozhraní

Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit. Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.

Chcete-li přizpůsobit uživatelské rozhraní, `id` definujte element `components-reconnect-modal` `<body>` na stránce *_Host. cshtml* Razor:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.

| CSS – třída                       | Označující&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Ztracené připojení. Klient se pokouší znovu připojit. Zobrazit modální okno. |
| `components-reconnect-hide`     | K serveru se znovu naváže aktivní připojení. Skryje modální okno. |
| `components-reconnect-failed`   | Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě. Chcete-li se pokusit `window.Blazor.reconnect()`znovu připojit, zavolejte. |
| `components-reconnect-rejected` | Opětovné připojení bylo zamítnuto. Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen. K opětovnému načtení aplikace zavolejte `location.reload()`. Tento stav připojení může mít za následek:<ul><li>Dojde k chybě okruhu na straně serveru.</li><li>Klient je dostatečně odpojený, aby server vynechal stav uživatele. Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</li><li>Server se restartuje nebo se pracovní proces aplikace recykluje.</li></ul> |

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

`RenderMode`nakonfiguruje, jestli součást:

* Je předem vykreslen na stránku.
* Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.

| `RenderMode`        | Popis |
| ------------------- | ----------- |
| `ServerPrerendered` | Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
| `Server`            | Vykreslí značku pro Blazor serverovou aplikaci. Výstup komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
| `Static`            | Vykreslí komponentu do statického HTML. |

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor

Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.

Při vykreslení stránky nebo zobrazení:

* Komponenta je předem vykreslena se stránkou nebo zobrazením.
* Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.
* Po navázání SignalR připojení se vytvoří nový stav součásti.

Následující stránka Razor vykresluje `Counter` komponentu:

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

Na následující stránce Razor je `Counter` komponenta staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:

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

Vzhledem `MyComponent` k tomu, že se staticky vykreslují, komponenta nemůže být interaktivní.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Konfigurace SignalR klienta pro Blazor serverové aplikace

V SignalR některých případech je potřeba nakonfigurovat klienta používaného Blazor serverovými aplikacemi. Například můžete chtít nakonfigurovat protokolování na SignalR straně klienta, aby bylo možné diagnostikovat problém s připojením.

Chcete-li SignalR nakonfigurovat klienta v souboru *pages/_Host. cshtml* :

* Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.
* Zavolejte `Blazor.start` a předejte do konfiguračního objektu, který určuje SignalR tvůrce.

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

### <a name="logging"></a>protokolování

Informace o Blazor podpoře protokolování serveru najdete v tématu <xref:fundamentals/logging/index#create-logs-in-blazor>.
