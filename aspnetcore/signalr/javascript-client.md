---
title: ASP.NET SignalR klient javascriptu Core
author: bradygaster
description: Přehled ASP.NET SignalR javascriptového klienta.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: a99c1dd2aba6ef6ff925783762a98e2c81ed7225
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994574"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET SignalR klient javascriptu Core

Podle [Rachel Appel](https://twitter.com/rachelappel)

Klientská SignalR knihovna ASP.NET JavaScript umožňuje vývojářům volat kód rozbočovače na straně serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="install-the-opno-locsignalr-client-package"></a>Instalace SignalR klientského balíčku

Klientská knihovna SignalR JavaScriptu je dodávána jako balíček [npm.](https://www.npmjs.com/) V následujících částech jsou popsány různé způsoby instalace klientské knihovny.

### <a name="install-with-npm"></a>Instalace s npm

Pokud používáte Visual Studio, spusťte následující příkazy z **konzoly Správce balíčků** v kořenové složce. Kód sady Visual Studio spusťte následující příkazy z **integrovaného terminálu**.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm nainstaluje obsah balíčku do *node_modules\\ * složky. Vytvořte novou složku s názvem *signalr* pod stolitou *wwwroot\\lib.* Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr.*

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm nainstaluje obsah balíčku do *node_modules\\ * složky. Vytvořte novou složku s názvem *signalr* pod stolitou *wwwroot\\lib.* Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr.*

::: moniker-end

Odkazna SignalR klienta JavaScript u `<script>` prvku. Příklad:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Použití sítě pro doručování obsahu (CDN)

Chcete-li použít klientskou knihovnu bez předpokladů npm, odkazujte na kopii klientské knihovny hostovodou cdn. Příklad:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

Klientská knihovna je k dispozici na následujících nedostupných seznamech CDN:

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>Instalace s LibManem

[LibMan](xref:client-side/libman/index) lze použít k instalaci konkrétních souborů klientské knihovny z klientské knihovny hostované v CDN. Například přidejte do projektu pouze minifikovaný soubor JavaScript. Podrobnosti o tomto [přístupu SignalR naleznete v tématu Přidání klientské knihovny](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Připojení k rozbočovači

Následující kód vytvoří a spustí připojení. Název rozbočovače je malá a velká písmena.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Spojení s křížovým původem

Prohlížeče obvykle načítají připojení ze stejné domény jako požadovaná stránka. Existují však případy, kdy je vyžadováno připojení k jiné doméně.

Chcete-li zabránit škodlivému webu ve čtení citlivých dat z jiné lokality, jsou [připojení mezi počátky](xref:security/cors) ve výchozím nastavení zakázána. Chcete-li povolit požadavek na `Startup` příčný původ, povolte jej ve třídě.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Metody centra volání z klienta

Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [hubconnection](/javascript/api/%40aspnet/signalr/hubconnection). Metoda `invoke` přijímá dva argumenty:

* Název metody rozbočovače. V následujícím příkladu je `SendMessage`název metody v rozbočovači .
* Všechny argumenty definované v metodě rozbočovače. V následujícím příkladu je `message`název argumentu . Ukázkový kód používá syntaxi funkce šipky, která je podporována v aktuálních verzích všech hlavních prohlížečů s výjimkou aplikace Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Pokud používáte službu Azure SignalR service v *režimu bez serveru*, nelze volat metody rozbočovače z klienta. Další informace naleznete v [ SignalR dokumentaci ke službě](/azure/azure-signalr/signalr-concept-serverless-development-config).

Metoda `invoke` vrátí JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise). Je `Promise` vyřešen s vrácenou hodnotou (pokud existuje) při metoda na serveru vrátí. Pokud metoda na serveru vyvolá chybu, `Promise` je odmítnut s chybovou zprávou. Použijte `then` metody `catch` a `Promise` na sobě ke zpracování `await` těchto případů (nebo syntaxe).

Metoda `send` vrátí JavaScript `Promise`. Je `Promise` vyřešen, když byla zpráva odeslána na server. Pokud dojde k chybě při `Promise` odesílání zprávy, je odmítnut a chybová zpráva. Použijte `then` metody `catch` a `Promise` na sobě ke zpracování `await` těchto případů (nebo syntaxe).

> [!NOTE]
> Použití `send` nečeká, dokud server zprávu neobdrží. V důsledku toho není možné vrátit data nebo chyby ze serveru.

## <a name="call-client-methods-from-hub"></a>Volání klientských metod z centra

Chcete-li přijímat zprávy z centra, definujte `HubConnection`metodu pomocí metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) .

* Název metody klienta JavaScript. V následujícím příkladu je `ReceiveMessage`název metody .
* Argumenty rozbočovač předá metodu. V následujícím příkladu je `message`hodnota argumentu .

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Předchozí kód v `connection.on` systému se spustí, když kód na straně serveru volá pomocí metody [SendAsync.](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRurčuje, kterou metodu klienta má volat porovnáním `SendAsync` `connection.on`názvu metody a argumentů definovaných v písmenech a) a.

> [!NOTE]
> Jako osvědčený postup volejte [start](/javascript/api/%40aspnet/signalr/hubconnection#start) metodu `HubConnection` start `on`na after . Tím zajistíte, že vaše obslužné rutiny jsou registrovány před přijetím všech zpráv.

## <a name="error-handling-and-logging"></a>Zpracování chyb a protokolování

Zřetězení `catch` metody na `start` konec metody pro zpracování chyb na straně klienta. Slouží `console.error` k výstupu chyb do konzole prohlížeče.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

Nastavení trasování protokolu na straně klienta předáním protokolovacího procesu a typu události, která se má přihlásit při navádění připojení. Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší. Dostupné úrovně protokolu jsou následující:

* `signalR.LogLevel.Error`&ndash; Chybové zprávy. Zaznamenává `Error` pouze zprávy.
* `signalR.LogLevel.Warning`&ndash; Varovné zprávy o možných chybách. Protokoly `Warning`a `Error` zprávy.
* `signalR.LogLevel.Information`&ndash; Stavové zprávy bez chyb. Protokoly `Information` `Warning`a `Error` zprávy.
* `signalR.LogLevel.Trace`&ndash; Vysledovat zprávy. Protokoluje vše, včetně dat přenášených mezi rozbočovačem a klientem.

Ke [konfiguraci](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) úrovně protokolu použijte metodu configureLogging v [HubConnectionBuilder.](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) Zprávy jsou zaznamenány do konzole prohlížeče.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Opětovné připojení klientů

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automatické opětovné připojení

Klient javascriptu SignalR lze nakonfigurovat tak, `withAutomaticReconnect` aby se automaticky znovu připojoval pomocí metody na [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Ve výchozím nastavení se automaticky nepřipojí.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Bez jakýchkoli parametrů `withAutomaticReconnect()` nakonfiguruje klienta tak, aby před pokusem o každý pokus o opětovné připojení čekal 0, 2, 10 a 30 sekund, a to po čtyřech neúspěšných pokusech.

Před zahájením jakékoli pokusy `HubConnection` o opětovné `HubConnectionState.Reconnecting` připojení bude `onreconnecting` přechod do stavu a požární `Disconnected` jeho zpětná `onclose` volání namísto `HubConnection` přechodu do stavu a aktivaci jeho zpětná volání jako bez automatického opětovného připojení nakonfigurován. To poskytuje příležitost upozornit uživatele, že připojení bylo ztraceno a zakázat prvky uživatelského rozhraní.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Pokud klient úspěšně znovu připojí v rámci své `HubConnection` první čtyři pokusy, bude přechod zpět do `Connected` stavu a požární jeho `onreconnected` zpětná volání. To poskytuje příležitost informovat uživatele, že připojení bylo obnoveno.

Vzhledem k tomu, že připojení vypadá `connectionId` zcela nové na `onreconnected` server, bude k dispozici nový zpětné volání.

> [!WARNING]
> Parametr `onreconnected` zpětného `connectionId` volání nebude definován, `HubConnection` pokud byl nakonfigurován tak, aby [přeskočoval vyjednávání](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`nenakonfiguruje opakování selhání počátečního `HubConnection` spuštění, takže selhání spuštění je nutné zpracovat ručně:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

Pokud klient není úspěšně znovu připojit v rámci své `HubConnection` první čtyři `Disconnected` pokusy, bude přechod do stavu a požární jeho připojení [k uzavření](/javascript/api/%40aspnet/signalr/hubconnection#onclose) zpětná volání. To poskytuje příležitost informovat uživatele, že připojení bylo trvale ztraceno, a doporučujeme aktualizovat stránku:

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Chcete-li nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování opětovného připojení, `withAutomaticReconnect` přijme pole čísel představující zpoždění v milisekundách, které čeká před spuštěním každého pokusu o opětovné připojení.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Předchozí příklad nakonfiguruje `HubConnection` zahájení pokusu o opětovné připojení ihned po ztrátě připojení. To platí také pro výchozí konfiguraci.

Pokud se první pokus o opětovné připojení nezdaří, druhý pokus o opětovné připojení se také spustí okamžitě namísto čekání 2 sekundy, jako by to bylo ve výchozí konfiguraci.

Pokud se druhý pokus o opětovné připojení nezdaří, třetí pokus o opětovné připojení se spustí za 10 sekund, což je opět jako výchozí konfigurace.

Vlastní chování se pak znovu odchyluje od výchozího chování zastavením po selhání třetího pokusu o opětovné připojení namísto pokusu o další pokus o opětovné připojení v jiné 30 sekundě, jako by to bylo ve výchozí konfiguraci.

Pokud chcete ještě větší kontrolu nad časování a počet `withAutomaticReconnect` pokusů o automatické `IRetryPolicy` opětovné připojení, přijme objekt `nextRetryDelayInMilliseconds`implementující rozhraní, který má jednu metodu s názvem .

`nextRetryDelayInMilliseconds`trvá jeden argument s `RetryContext`typem . Má `RetryContext` tři vlastnosti: `previousRetryCount`, `elapsedMilliseconds` a `retryReason` `number` které `Error` jsou `number`, a a příslušně. Před prvním pokusem o `previousRetryCount` `elapsedMilliseconds` opětovné připojení, a `retryReason` to jak a bude nula a bude chyba, která způsobila ztrátu připojení. Po každém neúspěšném `previousRetryCount` pokusu o opakování, bude `elapsedMilliseconds` zvýší o jeden, bude aktualizován tak, aby odrážely množství `retryReason` času stráveného opětovné připojení tak daleko v milisekundách a bude chyba, která způsobila poslední pokus o opětovné připojení k selhání.

`nextRetryDelayInMilliseconds`musí vrátit číslo představující počet milisekund čekat před dalším pokusem `null` o `HubConnection` opětovné připojení nebo pokud by měl přestat znovu připojit.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

Případně můžete napsat kód, který bude znovu klienta ručně, jak je znázorněno v [ručně znovu připojit](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ruční opětovné připojení

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Před 3.0 se klient SignalR JavaScriptu automaticky nepřipojí. Musíte napsat kód, který bude znovu klienta ručně.

::: moniker-end

Následující kód ukazuje typický přístup ručního opětovného připojení:

1. Funkce (v tomto případě `start` funkce) je vytvořen pro spuštění připojení.
1. Volání `start` funkce v obslužné `onclose` rutině události připojení.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Implementace v reálném světě by použít exponenciální back-off nebo opakovat zadaný počet opakování před vzdání.

## <a name="additional-resources"></a>Další zdroje

* [Referenční dokumentace k rozhraní API v JavaScriptu](/javascript/api/?view=signalr-js-latest)
* [Výukový program javascriptu](xref:tutorials/signalr)
* [Kurz webpacku a jazyka TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Rozbočovače](xref:signalr/hubs)
* [Klient rozhraní .NET](xref:signalr/dotnet-client)
* [Publikování aplikací do Azure](xref:signalr/publish-to-azure-web-app)
* [Žádosti o křížový původ (CORS)](xref:security/cors)
* [Dokumentace SignalR azure service bez serveru](/azure/azure-signalr/signalr-concept-serverless-development-config)
