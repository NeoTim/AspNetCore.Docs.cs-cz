---
title: ASP.NET Core Blazor Konfigurace modelu hostování
author: guardrex
description: Přečtěte si o dalších scénářích ASP.NET Core Blazor konfiguraci modelu hostování.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 236d95e54b772ea522911421084ec0d9022c45ff
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424136"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>ASP.NET Core Blazor Konfigurace modelu hostování

Od [Daniel Skořepa](https://github.com/danroth27), [MacKinnon Buck](https://github.com/MackinnonBuck)a [Luke Latham](https://github.com/guardrex)

Tento článek popisuje konfiguraci modelu hostování.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>SignalR vyjednávání mezi zdroji pro ověřování

*Tato část se týká Blazor WebAssembly .*

Pro konfiguraci SignalR základního klienta pro odesílání přihlašovacích údajů, jako jsou například cookie s nebo HLAVIČKY ověřování http:

* Použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> k nastavení <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> u požadavků mezi zdroji [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* Přiřaďte <xref:System.Net.Http.HttpMessageHandler> k <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> Možnosti:

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Další informace naleznete v tématu <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Odrážet stav připojení v uživatelském rozhraní

*Tato část se týká Blazor Server .*

Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit. Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.

Chcete-li přizpůsobit uživatelské rozhraní, definujte element s prvkem `id` z `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stránce:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Do šablony stylů aplikace přidejte následující `wwwroot/css/app.css` `wwwroot/css/site.css` :

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.

| CSS – třída                       | Označující&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Ztracené připojení. Klient se pokouší znovu připojit. Zobrazit modální okno. |
| `components-reconnect-hide`     | K serveru se znovu naváže aktivní připojení. Skryje modální okno. |
| `components-reconnect-failed`   | Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě. Chcete-li se pokusit znovu připojit, zavolejte `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Opětovné připojení bylo zamítnuto. Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen. K opětovnému načtení aplikace zavolejte `location.reload()` . Tento stav připojení může mít za následek:<ul><li>Dojde k chybě okruhu na straně serveru.</li><li>Klient je dostatečně odpojený, aby server vynechal stav uživatele. Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</li><li>Server se restartuje nebo se pracovní proces aplikace recykluje.</li></ul> |

## <a name="render-mode"></a>Režim vykreslování

*Tato část se týká Blazor Server .*

Blazor Server aplikace se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru. Tato stránka je nastavená na `_Host.cshtml` Razor stránce:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> nakonfiguruje, jestli součást:

* Je předem vykreslen na stránku.
* Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.

| Režim vykreslování | Popis |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor Server aplikaci. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Vykreslí značku pro Blazor Server aplikaci. Výstup komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Vykreslí komponentu do statického HTML. |

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

## <a name="initialize-the-no-locblazor-circuit"></a>Inicializovat Blazor okruh

*Tato část se týká Blazor Server .*

Konfigurace ručního spuštění Blazor Server [ SignalR okruhu](xref:blazor/hosting-models#circuits) aplikace v `Pages/_Host.cshtml` souboru:

* Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.
* Umístěte skript, který volá `Blazor.start` za `blazor.server.js` značku skriptu a uvnitř uzavírací `</body>` značky.

Pokud `autostart` je zakázaný, všechny aspekty aplikace, které nezávisí na okruhu, fungují normálně. Například směrování na straně klienta je funkční. Nicméně jakýkoliv aspekt, který závisí na okruhu, není funkční, dokud `Blazor.start` se nevolá. Chování aplikace je nepředvídatelné bez vytvořeného okruhu. Například metody komponenty se nedaří spustit, pokud je okruh odpojen.

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a>Inicializovat Blazor při přípravě dokumentu

Inicializace aplikace, Blazor když je dokument připravený:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>Řetězení k `Promise` , které je výsledkem ručního spuštění

Chcete-li provést další úkoly, jako je například Inicializace zprostředkovatele spolupráce v JS, použijte příkaz `then` k zřetězení s `Promise` výsledkem ručního Blazor spuštění aplikace:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a>Konfigurace SignalR klienta

#### <a name="logging"></a>protokolování

Pokud chcete nakonfigurovat SignalR protokolování klienta, předejte do konfiguračního objektu ( `configureSignalR` ), který se zavolá na `configureLogging` úrovni protokolu na tvůrci klienta:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

V předchozím příkladu `information` je ekvivalentem úrovně protokolu <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .

### <a name="modify-the-reconnection-handler"></a>Úprava obslužné rutiny opětovného připojení

Události připojení okruhu obslužné rutiny opětovného připojení lze upravit pro vlastní chování, například:

* Upozorní uživatele, pokud bylo připojení vyřazeno.
* K provedení protokolování (z klienta) při připojení okruhu.

Chcete-li upravit události připojení, zaregistrujte zpětná volání pro následující změny připojení:

* Byla přerušena použití připojení `onConnectionDown` .
* Navázalo se opakované použití připojení `onConnectionUp` .

**Oba směry** `onConnectionDown` `onConnectionUp` musí být zadány:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Upravit počet a interval opakování připojení

Pokud chcete upravit počet a interval opakování připojení, nastavte počet opakování ( `maxRetries` ) a dobu v milisekundách povolenou pro každý pokus o opakování ( `retryIntervalMilliseconds` ):

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a>Skrýt nebo nahradit zobrazení opětovného připojení

Chcete-li skrýt zobrazení opětovného připojení, nastavte popisovač opětovného připojení `_reconnectionDisplay` na prázdný objekt ( `{}` nebo `new Object()` ):

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

Chcete-li nahradit zobrazení opětovného připojení, nastavte `_reconnectionDisplay` v předchozím příkladu na prvek pro zobrazení:

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

Zástupný symbol `{ELEMENT ID}` je ID prvku HTML, který se má zobrazit.

::: moniker range=">= aspnetcore-5.0"

Přizpůsobte zpoždění před tím, než se zobrazí zobrazení opětovného připojení nastavením `transition-delay` vlastnosti v CSS ( `wwwroot/css/site.css` ) aplikace pro modální prvek. Následující příklad nastaví zpoždění přechodu z 500 ms (výchozí) na 1 000 ms (1 sekunda):

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a>Odpojit Blazor okruh od klienta

Ve výchozím nastavení Blazor je okruh odpojen při aktivaci [ `unload` události stránky](https://developer.mozilla.org/docs/Web/API/Window/unload_event) . Chcete-li odpojit okruh pro jiné scénáře v klientovi, vyvolejte `Blazor.disconnect` v příslušné obslužné rutině události. V následujícím příkladu je okruh odpojený, když je stránka skrytá ([ `pagehide` událost](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

## <a name="influence-html-head-tag-elements"></a>Ovlivnění `<head>` elementů značek HTML

*Tato část se vztahuje na nadcházející ASP.NET Core 5,0 verze Blazor WebAssembly a Blazor Server .*

Při vykreslení, `Title` komponenty, `Link` a `Meta` přidají nebo aktualizují data v `<head>` prvcích značek HTML:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

V předchozím příkladu zástupné symboly pro `{TITLE}` , `{URL}` a `{DESCRIPTION}` jsou řetězcové hodnoty, Razor proměnné nebo Razor výrazy.

Platí následující vlastnosti:

* Je podporováno předběžné vykreslování na straně serveru.
* `Value`Parametr je jediným platným parametrem pro `Title` komponentu.
* Atributy HTML, které jsou zadány `Meta` `Link` komponentám a jsou zachyceny v [dalších atributech](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) a předány do vykreslené značky HTML.
* U více `Title` komponent se v názvu stránky zobrazuje `Value` Poslední `Title` vykreslená komponenta.
* Je `Meta` -li `Link` pro stejné atributy zahrnuto více komponent nebo, je k dispozici pouze jedna značka HTML vykreslená na jednu `Meta` nebo `Link` komponentu. Dvě `Meta` nebo `Link` komponenty nemůžou odkazovat na stejnou vykreslenou značku HTML.
* Změny parametrů existujících `Meta` nebo `Link` komponent se projeví ve vykreslených značkách HTML.
* Když `Link` komponenty nebo `Meta` již nejsou vykreslovány a jsou následně odstraněny rozhraním, jsou odebrány jejich vykreslené značky HTML.

Pokud se jedna z komponent rozhraní používá v podřízené komponentě, vykreslená značka HTML ovlivňuje všechny ostatní podřízené komponenty nadřazené komponenty, pokud je vykreslena podřízená komponenta obsahující komponentu rozhraní. Rozdíl mezi použitím jedné z těchto komponent rozhraní v podřízené komponentě a umístěním značky HTML do `wwwroot/index.html` nebo `Pages/_Host.cshtml` je vykreslená značka HTML komponenty rozhraní:

* Může být upraveno stavem aplikace. Pevně kódovaná značka HTML nemůže být upravena stavem aplikace.
* Je odebrán z kódu HTML, `<head>` Pokud již není vykreslena nadřazená komponenta.

::: moniker-end

## <a name="static-files"></a>Statické soubory

*Tato část se týká Blazor Server .*

Pokud chcete vytvořit další mapování souborů pomocí <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> nebo nakonfigurovat jinou <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , použijte **jeden** z následujících přístupů. V následujících příkladech `{EXTENSION}` je zástupný symbol Přípona souboru a `{CONTENT TYPE}` zástupný symbol je typ obsahu.

* Nakonfigurujte možnosti prostřednictvím [Injektáže závislosti (di)](xref:blazor/fundamentals/dependency-injection) v `Startup.ConfigureServices` ( `Startup.cs` ) pomocí <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  Vzhledem k tomu, že tento přístup nakonfiguruje stejného poskytovatele souborů používaného pro obsluhu, ujistěte se `blazor.server.js` , že vaše vlastní konfigurace nekoliduje s obsluhou `blazor.server.js` . Neodstraňujte například mapování pro soubory JavaScriptu konfigurací poskytovatele pomocí `provider.Mappings.Remove(".js")` .

* Použijte dvě volání do <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> `Startup.Configure` ( `Startup.cs` ):
  * Nakonfigurujte vlastního zprostředkovatele souborů v prvním volání pomocí <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .
  * Druhý middleware slouží `blazor.server.js` k tomu, který používá výchozí konfiguraci statických souborů poskytovanou Blazor rozhraním.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/logging/index>
