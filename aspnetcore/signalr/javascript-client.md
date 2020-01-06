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
ms.openlocfilehash: eaf737642cdbd7ab2b1b5c16538b47a70cddd332
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354701"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>Klient ASP.NET Core SignalR JavaScript

Podle [Rachel Appel](https://twitter.com/rachelappel)

Klientská knihovna ASP.NET Core SignalR JavaScript umožňuje vývojářům volat kód centra na straně serveru.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>Instalace balíčku klienta SignalR

Klientská knihovna SignalR JavaScript je dodávána jako balíček [npm](https://www.npmjs.com/) . Pokud používáte Visual Studio, spusťte `npm install` z **Konzola správce balíčků** během činnosti v kořenové složce. Visual Studio Code, spusťte příkaz z **integrovaný terminál**.

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

npm nainstaluje balíček obsahu *node_modules\\@microsoft\signalr\dist\browser* složky. Vytvořte novou složku s názvem *signalr* pod *wwwroot\\lib* složky. Kopírovat *signalr.js* do souboru *wwwroot\lib\signalr* složky.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

npm nainstaluje balíček obsahu *node_modules\\@aspnet\signalr\dist\browser* složky. Vytvořte novou složku s názvem *signalr* pod *wwwroot\\lib* složky. Kopírovat *signalr.js* do souboru *wwwroot\lib\signalr* složky.

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a>Použití klienta SignalR JavaScript

Odkaz na SignalR klient JavaScript v elementu `<script>`.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>Připojení k rozbočovači

Následující kód vytvoří a spustí připojení. Název centra se nerozlišují malá a velká písmena.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Nepůvodního zdroje připojení

Obvykle prohlížeče načíst připojení ve stejné doméně jako požadovanou stránku. Existují však situace, kdy je vyžadováno připojení k jiné doméně.

Aby se zabránilo škodlivým webům ve čtení citlivých dat z jiné lokality [nepůvodního zdroje připojení](xref:security/cors) jsou ve výchozím nastavení zakázané. Žádosti nepůvodního zdroje, povolit jej `Startup` třídy.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Volání metod rozbočovače na z klienta

Klientům JavaScript volat veřejné metody rozbočovače prostřednictvím [vyvolat](/javascript/api/%40aspnet/signalr/hubconnection#invoke) metodu [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). `invoke` Metoda přijímá dva argumenty:

* Název metody rozbočovače. V následujícím příkladu je název metody rozbočovače `SendMessage`.
* Všechny argumenty podle metody rozbočovače. V následujícím příkladu je název argumentu `message`. Vzorový kód používá syntaxi funkce šipky, která je podporována v aktuálních verzích všech hlavních prohlížečů s výjimkou aplikace Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Pokud používáte službu Azure SignalR v režimu bez *serveru*, nemůžete volat metody centra z klienta. Další informace najdete v dokumentaci ke [služběSignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).

Metoda `invoke` vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu. `Promise` se vyřeší návratovou hodnotou (pokud existuje), když metoda na serveru vrátí hodnotu. Pokud metoda na serveru vyvolá chybu, `Promise` je odmítnuta s chybovou zprávou. Pro zpracování těchto případů (nebo `await` syntaxe) použijte metody `then` a `catch` na `Promise` sebe sama.

Metoda `send` vrátí `Promise`JavaScriptu. `Promise` se vyřeší, když se do serveru pošle zpráva. Pokud při odesílání zprávy dojde k chybě, `Promise` je odmítnuta s chybovou zprávou. Pro zpracování těchto případů (nebo `await` syntaxe) použijte metody `then` a `catch` na `Promise` sebe sama.

> [!NOTE]
> Použití `send` nečeká na přijetí zprávy serverem. V důsledku toho není možné vracet data nebo chyby ze serveru.

## <a name="call-client-methods-from-hub"></a>Volání metody klienta od rozbočovače

Chcete-li přijímat zprávy z centra, definovat metodu pomocí [na](/javascript/api/%40aspnet/signalr/hubconnection#on) metodu `HubConnection`.

* Název metody jazyka JavaScript. V následujícím příkladu je název metody `ReceiveMessage`.
* Argumenty, které se předá metodě rozbočovače. V následujícím příkladu je hodnota argumentu `message`.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Předchozí kód v `connection.on` spustí, když kód na straně serveru pomocí volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) metoda.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR určuje, která metoda klienta má být volána, porovnáním názvu metody a argumentů definovaných v `SendAsync` a `connection.on`.

> [!NOTE]
> Jako osvědčený postup volání [start](/javascript/api/%40aspnet/signalr/hubconnection#start) metodu `HubConnection` po `on`. Tím se zajistí, že vaše obslužné rutiny jsou registrovány předtím, než jsou přijaty žádné zprávy.

## <a name="error-handling-and-logging"></a>Protokolování a zpracování chyb

Řetězce `catch` metoda na konec objektu `start` metodu ke zpracování chyby na straně klienta. Použití `console.error` chyby výstup do konzoly prohlížeče.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

Nastavení na straně klienta protokolu trasování předáním protokolovací nástroj a typ události do protokolu, když se připojení. Zprávy jsou zaznamenány na úrovni zadaný protokol a vyšší. Dostupné úrovně jsou následující:

* `signalR.LogLevel.Error` &ndash; Chybové zprávy. Protokoly `Error` pouze zprávy.
* `signalR.LogLevel.Warning` &ndash; Zprávy s upozorněním potenciální chyby. Protokoly `Warning`, a `Error` zprávy.
* `signalR.LogLevel.Information` &ndash; Stavové zprávy bez chyb. Protokoly `Information`, `Warning`, a `Error` zprávy.
* `signalR.LogLevel.Trace` &ndash; Trasovací zprávy. Protokoly vše, včetně dat přenášených mezi centrem a klienta.

Použití [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) metoda [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) nakonfigurovat úroveň protokolu. Zprávy jsou protokolovány do konzoly prohlížeče.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Opětovné připojení klientů

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
        }
    })
    .build();
```

Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ručně znovu připojit

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Před 3,0 se klient JavaScript pro SignalR automaticky znovu nepřipojí. Musíte napsat kód, který se znovu připojit klientu ručně.

::: moniker-end

Následující kód ukazuje typický postup ručního opětovného připojení:

1. Funkce (v tomto případě `start` funkce) se vytvoří připojení spustíte.
1. Volání `start` funkce v rámci připojení `onclose` obslužné rutiny události.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Skutečná implementace by použít exponenciální regrese nebo opakování zadaného počtu opakování, než se ukončí.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Referenční dokumentace k rozhraní API v JavaScriptu](/javascript/api/?view=signalr-js-latest)
* [Kurz jazyka JavaScript](xref:tutorials/signalr)
* [Kurz Webpacku a TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
* [Požadavky mezi zdroji (CORS)](xref:security/cors)
* [Dokumentace k serveru služby Azure SignalR bez serveru](/azure/azure-signalr/signalr-concept-serverless-development-config)
