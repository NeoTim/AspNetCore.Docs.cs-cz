---
title: Blazor modelech hostování
author: guardrex
description: Seznamte se s na straně klienta i stranu serveru Blazor hostování modely.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2019
uid: blazor/hosting-models
ms.openlocfilehash: 7de93e8721b06e545b3125d78d5e9e0e34c04511
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982988"
---
# <a name="blazor-hosting-models"></a>Blazor modelech hostování

Podle [Daniel Roth](https://github.com/danroth27)

Blazor je webové rozhraní navržené ke spuštění na straně klienta v prohlížeči na [WebAssembly](http://webassembly.org/)– na základě modulu runtime .NET (*Blazor na straně klienta*) nebo na serveru ASP.NET Core (*Blazor na straně serveru* ). Bez ohledu na modelech hostování modelu, aplikace a komponenty *zůstávají stejné*.

## <a name="client-side"></a>Na straně klienta

Hlavní model hostingu pro Blazor je spuštěné v prohlížeči na WebAssembly straně klienta. Aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče. Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní. Aktualizace uživatelského rozhraní a zpracování událostí dochází v rámci stejného procesu. Prostředky aplikace se nasadí jako statické soubory na webový server nebo služba je schopná obsluhuje statický obsah, na klienty.

![Blazor straně klienta: Blazor aplikace běží na vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/client-side.png)

Chcete-li vytvořit aplikaci Blazor používá model hostování na straně klienta, použijte jednu z následujících šablon:

* **Blazor** ([nové blazor dotnet](/dotnet/core/tools/dotnet-new)) &ndash; nasazený jako sada statické soubory.
* **Blazor (ASP.NET Core v prostředí)** ([nové blazorhosted dotnet](/dotnet/core/tools/dotnet-new)) &ndash; hostované serveru ASP.NET Core. Aplikace ASP.NET Core obsluhuje Blazor aplikaci pro klienty. Aplikace na straně klienta Blazor můžete spolupracovat se serverem přes síť pomocí volání webového rozhraní API nebo [SignalR](xref:signalr/introduction).

Šablony zahrnují *blazor.webassembly.js* skript, který zpracovává:

* Stahuje se modul .NET runtime, aplikace a závislostí aplikace.
* Inicializace modulu runtime a spusťte tak aplikaci.

Model hostingu na straně klienta nabízí několik výhod. Blazor na straně klienta:

* Nemá žádné závislosti .NET na straně serveru.
* Plně využívá prostředky klienta a možnosti.
* Snižování zátěže pracovat ze serveru do klienta.
* Podporuje scénáře v režimu offline.

Existují nevýhody hostování na straně klienta. Blazor na straně klienta:

* Omezí aplikace funkcí v prohlížeči.
* Vyžaduje klienta s podporou, hardware a software (třeba podporu WebAssembly).
* Má větší velikost ke stažení a delší dobu načítání aplikace.
* Má menší modul .NET runtime a podpora nástrojů pro dospělé (například omezení [.NET Standard](/dotnet/standard/net-standard) podpory a ladění).

## <a name="server-side"></a>Na straně serveru

S model hostingu na straně serveru aplikace spuštěné na serveru z v rámci aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.

![V prohlížeči komunikuje s aplikaci (už je hostovaná v rámci aplikace ASP.NET Core) na serveru pomocí připojení SignalR.](hosting-models/_static/server-side.png)

Chcete-li vytvořit aplikaci Blazor používá model hostování na straně serveru, použijte ASP.NET Core **Blazor (serverové)** šablony ([nové blazorserverside dotnet](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core hostitelem aplikace na straně serveru a nastaví koncových bodů SignalR, ve kterém se klienti připojují.

Aplikace ASP.NET Core odkazuje aplikaci `Startup` třídy přidejte:

* Služby na straně serveru.
* Aplikace na žádost o zpracování kanálu.

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

*Blazor.server.js* skript&dagger; naváže připojení klienta. Je zodpovědností aplikace k zachování a obnovení stavu aplikace podle potřeby (například v případě ztráty připojení).

Model hostingu na straně serveru nabízí několik výhod:

* Výrazně menší aplikace velikost než aplikace na straně klienta a načtou mnohem rychleji.
* Plně využijte serverových funkcí, včetně použití libovolné rozhraní API .NET Core kompatibilní.
* Spusťte na .NET Core na serveru, takže existující .NET nástrojů, jako je ladění, funguje podle očekávání.
* Funguje s tencí klienti (například prohlížeče, které nepodporují WebAssembly a prostředků omezené zařízení).
* .NET /C# základu kódu, včetně kódu komponenty aplikace, není poskytováni, na klienty.

Existují nevýhody hostování na straně serveru:

* Vyšší latence: Každá interakce uživatele zahrnuje směrování v síti.
* Žádná podpora pro offline: Pokud klienta nepovede, aplikace přestane fungovat.
* Snížení škálovatelnosti: Server musí spravovat připojení více klientů a zpracování stavu klienta.
* Server služby ASP.NET Core je zapotřebí pro zpracování aplikace. Nasazení bez serveru (například ze sítě CDN) není možné.

&dagger;*Blazor.server.js* do následujícího umístění je publikován skriptu: *bin / {ladění | Verze} / {CÍLOVÁ ARCHITEKTURA} /publish/ {název aplikace}. Aplikace/dist/_architektura*.

### <a name="reconnection-to-the-same-server"></a>Opětovné připojení ke stejnému serveru

Blazor serverové aplikace vyžadují aktivní připojení k serveru funkce SignalR. Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru. Za předpokladu, stav klienta je stále v paměti, bude pokračovat bez ztráty některému ze stavů relace klienta.
 
Když klient zjistí, že připojení bylo ztraceno, je výchozí uživatelské rozhraní a klient se pokusí znovu připojit, zobrazit uživateli. Pokud opětovného připojení selže, uživateli se nabídnou možnost opakovat. Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `components-reconnect-modal` jako jeho `id`. Klient aktualizuje tento element s jedním z následujících tříd šablon stylů CSS na základě stavu připojení:
 
* `components-reconnect-show` &ndash; Zobrazit uživatelské rozhraní, které označíte, se ztratí připojení a klient se pokouší o obnovení připojení.
* `components-reconnect-hide` &ndash; Klient má aktivní připojení, Skrýt uživatelské rozhraní.
* `components-reconnect-failed` &ndash; Opětovné připojení se nezdařilo. Pokusu o opětovné připojení znovu, volání `window.Blazor.reconnect()`.

### <a name="stateful-reconnection-after-prerendering"></a>Stavové opětovného připojení po dokončení fáze před vykreslením
 
Aplikace na straně serveru Blazor nastavení jsou ve výchozím nastavení prerender uživatelského rozhraní na serveru před navázáním připojení klienta k serveru. Nastavuje se to *_Host.cshtml* stránky Razor:
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
Klient znovu připojí k serveru pomocí stejného stavu, který se použil k prerender aplikace. Pokud státu aplikace je stále v paměti, nemusí se stav komponenty rerendered, jakmile se naváže připojení SignalR.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Vykreslení stavové interaktivní součásti ze stránky Razor a zobrazení
 
Stavové interaktivní komponenty lze přidat stránky Razor nebo zobrazení. Při vykreslení stránky nebo zobrazení, komponenta je předkreslených s ním. Aplikace potom se znovu připojí k stav komponenty po připojení klienta pokládáme stav, stav je stále v paměti.
 
Například následující stránky Razor vykreslí součást čítače s počáteční počet, který je určen pomocí formuláře:
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialCount" />
    <button type="submit">Set initial count</button>
</form>
 
@(await Html.RenderComponentAsync<Counter>(new { InitialCount = InitialCount }))
 
@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialCount { get; set; }
}
```

### <a name="detect-when-the-app-is-prerendering"></a>Rozpoznat, kdy aplikace je před vykreslením
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a>Konfigurace klienta SignalR pro Blazor serverové aplikace
 
V některých případech budete muset nakonfigurovat klienta SignalR používat Blazor aplikace na straně serveru. Například můžete chtít konfigurovat protokolování na straně klienta SignalR pro diagnostiku problému připojení.
 
Abyste mohli nakonfigurovat klienta SignalR v *wwwroot/index.htm* souboru:

* Přidat `autostart="false"` atribut `<script>` značky *blazor.server.js* skriptu.
* Volání `Blazor.start` a předat objekt konfigurace, který určuje SignalR Tvůrce:
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

### <a name="improved-signalr-connection-lifetime-handling"></a>Vylepšené zpracování životnost připojení SignalR

Automatické připojování se dá nastavit pomocí volání `withAutomaticReconnect` metodu na `HubConnectionBuilder`:

```csharp
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Bez zadání parametrů, `withAutomaticReconnect` nakonfiguruje klienta se znovu připojit, 0, 2, 10 a 30 sekund mezi jednotlivými pokusy o čekání.

Chcete-li nakonfigurovat jiné než výchozí počet pokusů o nové připojení před selháním nebo změnit časování volání metody reconnect `withAutomaticReconnect` přijímá pole čísel představující zpoždění v milisekundách pro čekání před zahájením každý pokus o volání metody reconnect.

```csharp
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 2000, 5000]) // defaults to [0, 2000, 10000, 30000]
    .build();
```

### <a name="improved-disconnect-and-reconnect-handling"></a>Vylepšené odpojit a znovu připojte zpracování

Před zahájením jakékoli pokusy o volání metody reconnect HubConnection přechází do `Reconnecting` stavu a aktivuje její `onreconnecting` zpětného volání. To představuje příležitost k upozornit uživatele, že připojení bylo přerušeno, zakážete prvky uživatelského rozhraní a zmírnit matoucí uživatelské scénáře, které mohou nastat z důvodu odpojeném stavu.

```javascript
connection.onreconnecting((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
  document.getElementById("messagesList").appendChild(li);
});
```

Pokud klient úspěšně obnoví v rámci své první čtyři pokusy `HubConnection`přechody zpět `Connected` stavu a aktivuje se v `onreconnected` zpětná volání. To poskytuje vývojářům příležitost k informování uživatelů o tom, zda je připojení znovu navázáno.

```javascript
connection.onreconnected((connectionId) => {
  console.assert(connection.state === signalR.HubConnectionState.Connected);

  document.getElementById("messageInput").disabled = false;

  const li = document.createElement("li");
  li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
  document.getElementById("messagesList").appendChild(li);
});
```

Pokud klient nebude znovu připojit úspěšně v rámci své první čtyři pokusy `HubConnection` přejde do `Disconnected` stavu a aktivuje její `onclosed` zpětná volání. Jde o vhodnou příležitost k informování uživatelů o tom, že se trvale ztratí připojení a doporučujeme aktualizovat stránku.

```javascript
connection.onclose((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Disconnected);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
  document.getElementById("messagesList").appendChild(li);
})
```

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/introduction>
