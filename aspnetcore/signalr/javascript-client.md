---
title: Klient ASP.NET Core SignalR JavaScript
author: bradygaster
description: Přehled ASP.NET Core klienta SignalR JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 926160a41c82853d83890f0d52b14d7d5561a990
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963771"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>Klient ASP.NET Core SignalR JavaScript

Od [Rachel Appel](https://twitter.com/rachelappel)

Klientská knihovna ASP.NET Core SignalR JavaScript umožňuje vývojářům volat kód centra na straně serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>Instalace balíčku klienta SignalR

Klientská knihovna SignalR JavaScript je dodávána jako balíček [npm](https://www.npmjs.com/) . Pokud používáte sadu Visual Studio, spusťte `npm install` z **konzoly Správce balíčků** v kořenové složce. Pro Visual Studio Code spusťte příkaz z **integrovaného terminálu**.

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

NPM nainstaluje obsah balíčku do složky *node_modules\\@microsoft\signalr\dist\browser* . Vytvořte novou složku s názvem *Signal* ve složce *wwwroot\\lib* . Zkopírujte soubor *Signal. js* do složky *wwwroot\lib\signalr* .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

NPM nainstaluje obsah balíčku do složky *node_modules\\@aspnet\signalr\dist\browser* . Vytvořte novou složku s názvem *Signal* ve složce *wwwroot\\lib* . Zkopírujte soubor *Signal. js* do složky *wwwroot\lib\signalr* .

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a>Použití klienta SignalR JavaScript

Odkaz na SignalR klient JavaScript v elementu `<script>`.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>Připojení k centru

Následující kód vytvoří a spustí připojení. V názvu centra se nerozlišují malá a velká písmena.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Připojení mezi zdroji

Prohlížeče obvykle načítají připojení ze stejné domény jako požadovanou stránku. Existují však situace, kdy je vyžadováno připojení k jiné doméně.

Chcete-li zabránit škodlivému webu v čtení citlivých dat z jiné lokality, [připojení mezi zdroji](xref:security/cors) jsou ve výchozím nastavení zakázána. Pokud chcete povolit žádost o více zdrojů, povolte ji ve třídě `Startup`.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Volání metod centra z klienta

Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). Metoda `invoke` přijímá dva argumenty:

* Název metody centra V následujícím příkladu je název metody v centru `SendMessage`.
* Jakékoli argumenty definované v metodě hub. V následujícím příkladu je název argumentu `message`. Vzorový kód používá syntaxi funkce šipky, která je podporována v aktuálních verzích všech hlavních prohlížečů s výjimkou aplikace Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Pokud používáte službu Azure SignalR v režimu bez *serveru*, nemůžete volat metody centra z klienta. Další informace najdete v dokumentaci ke [služběSignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).

Metoda `invoke` vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu. `Promise` se vyřeší návratovou hodnotou (pokud existuje), když metoda na serveru vrátí hodnotu. Pokud metoda na serveru vyvolá chybu, `Promise` je odmítnuta s chybovou zprávou. Pro zpracování těchto případů (nebo `await` syntaxe) použijte metody `then` a `catch` na `Promise` sebe sama.

Metoda `send` vrátí `Promise`JavaScriptu. `Promise` se vyřeší, když se do serveru pošle zpráva. Pokud při odesílání zprávy dojde k chybě, `Promise` je odmítnuta s chybovou zprávou. Pro zpracování těchto případů (nebo `await` syntaxe) použijte metody `then` a `catch` na `Promise` sebe sama.

> [!NOTE]
> Použití `send` nečeká na přijetí zprávy serverem. V důsledku toho není možné vracet data nebo chyby ze serveru.

## <a name="call-client-methods-from-hub"></a>Volání metod klienta z centra

Chcete-li přijímat zprávy z centra, definujte metodu pomocí metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection`.

* Název metody klienta jazyka JavaScript. V následujícím příkladu je název metody `ReceiveMessage`.
* Argumenty, které rozbočovač předává metodě. V následujícím příkladu je hodnota argumentu `message`.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Předchozí kód v `connection.on` se spustí, když kód na straně serveru volá metodu [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR určuje, která metoda klienta má být volána, porovnáním názvu metody a argumentů definovaných v `SendAsync` a `connection.on`.

> [!NOTE]
> Osvědčeným postupem je zavolat metodu [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) na `HubConnection` po `on`. Tím zajistíte, aby byly obslužné rutiny registrovány před přijetím jakýchkoli zpráv.

## <a name="error-handling-and-logging"></a>Zpracování chyb a protokolování

Řetězit `catch` metodu ke konci `start` metody pro zpracování chyb na straně klienta. Pro výstup chyb do konzoly prohlížeče použijte `console.error`.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

Nastavení trasování protokolu na straně klienta předáním protokolovacího nástroje a typu události, která se má protokolovat při navázání spojení. Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší. K dispozici jsou následující úrovně protokolu:

* `signalR.LogLevel.Error` &ndash; chybové zprávy. Protokoluje pouze zprávy `Error`.
* `signalR.LogLevel.Warning` &ndash; varovné zprávy o potenciálních chybách. Protokoluje `Warning`a `Error` zprávy.
* `signalR.LogLevel.Information` &ndash; stavových zpráv bez chyb. Protokoluje zprávy `Information`, `Warning`a `Error`.
* `signalR.LogLevel.Trace` &ndash; trasovací zprávy. Zaznamená vše, včetně dat přepravovaných mezi centrem a klientem.

K nakonfigurování úrovně protokolu použijte metodu [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) pro [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) . Zprávy jsou protokolovány do konzoly prohlížeče.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Znovu připojit klienty

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automaticky znovu připojit

Klient jazyka JavaScript pro SignalR lze nakonfigurovat tak, aby se automaticky znovu připojoval pomocí metody `withAutomaticReconnect` na [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Ve výchozím nastavení se automaticky znovu nepřipojí.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Bez parametrů `withAutomaticReconnect()` nakonfiguruje klienta tak, aby počkal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.

Před zahájením jakýchkoli pokusů o opětovné připojení `HubConnection` přechodem do stavu `HubConnectionState.Reconnecting` a spustí `onreconnecting` zpětná volání namísto přechodu do `Disconnected` stavu a aktivuje své `onclose` zpětné volání, jako je `HubConnection`, aniž by bylo nakonfigurováno automatické opětovné připojení. Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, `HubConnection` přejde zpět do stavu `Connected` a aktivuje jeho `onreconnected` zpětná volání. To vám umožní informovat uživatele o tom, že bylo připojení znovu navázáno.

Vzhledem k tomu, že připojení na serveru zcela začíná, bude k dispozici nový `connectionId` ke zpětnému volání `onreconnected`.

> [!WARNING]
> Parametr `connectionId` `onreconnected` zpětného volání bude nedefinován, pokud byl `HubConnection` nakonfigurován pro [přeskočení vyjednávání](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` nenastaví `HubConnection` na opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:

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

Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde do stavu `Disconnected` a aktivuje jeho zpětné volání při jeho [ukončení](/javascript/api/%40aspnet/signalr/hubconnection#onclose) . To vám umožní informovat uživatele o trvalé ztrátě připojení a doporučit aktualizaci stránky:

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování opakovaného připojení, `withAutomaticReconnect` akceptuje pole čísel představujících zpoždění v milisekundách, které budou čekat před zahájením každého pokusu o opětovné připojení.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

V předchozím příkladu se nakonfiguruje `HubConnection`, aby se začaly znovu připojovat hned po ztrátě připojení. To platí také pro výchozí konfiguraci.

Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.

Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.

Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o ukončení pokusu o opětovné připojení v dalších 30 sekundách, jako by to bylo ve výchozí konfiguraci.

Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení, `withAutomaticReconnect` akceptuje objekt implementující rozhraní `IRetryPolicy`, které má jedinou metodu s názvem `nextRetryDelayInMilliseconds`.

`nextRetryDelayInMilliseconds` přijímá jeden argument typu `RetryContext`. `RetryContext` má tři vlastnosti: `previousRetryCount`, `elapsedMilliseconds` a `retryReason`, které jsou `number`, `number` a `Error`. Před prvním pokusem o opětovné připojení budou `previousRetryCount` i `elapsedMilliseconds` nula a `retryReason` dojde k chybě, která způsobila ztrátu připojení. Po každém neúspěšném pokusu o opakování se `previousRetryCount` zvýší o jednu, `elapsedMilliseconds` se aktualizuje tak, aby odrážela dobu trvání opětovného připojení v milisekundách, a `retryReason` bude chyba, která způsobila selhání posledního pokusu o opětovné připojení.

`nextRetryDelayInMilliseconds` musí vracet buď číslo představující počet milisekund, po který se má čekat před dalším pokusem o opětovné připojení, nebo `null`, pokud by se `HubConnection` mělo zastavit opětovné připojení.

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
        })
    .build();
```

Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ručně znovu připojit

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Před 3,0 se klient JavaScript pro SignalR automaticky znovu nepřipojí. Musíte napsat kód, který bude znovu připojit klienta ručně.

::: moniker-end

Následující kód ukazuje typický postup ručního opětovného připojení:

1. Funkce (v tomto případě funkce `start`) je vytvořena pro spuštění připojení.
1. V obslužné rutině události `onclose` připojení volejte funkci `start`.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Implementace reálného světa využije exponenciální přerušení nebo opakuje zadaný počet opakování.

## <a name="additional-resources"></a>Další zdroje

* [Referenční dokumentace k rozhraní API v JavaScriptu](/javascript/api/?view=signalr-js-latest)
* [Kurz JavaScriptu](xref:tutorials/signalr)
* [Kurz pro Webpack a TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
* [Žádosti mezi zdroji (CORS)](xref:security/cors)
* [Dokumentace k serveru služby Azure SignalR bez serveru](/azure/azure-signalr/signalr-concept-serverless-development-config)
