---
title: ASP.NET Core SignalR JavaScript klienta
author: bradygaster
description: Přehled ASP.NET Core SignalR JavaScript klienta.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/28/2019
uid: signalr/javascript-client
ms.openlocfilehash: f314e1fe0ef0ea73a28b332404a09f2956524132
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412377"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR JavaScript klienta

Podle [Rachel Appel](https://twitter.com/rachelappel)

Klientská knihovna ASP.NET Core SignalR JavaScript umožňuje vývojářům volat kód rozbočovače na straně serveru.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>Instalace balíčku pro klienta SignalR

Klientské knihovny SignalR JavaScript je dodávána jako [npm](https://www.npmjs.com/) balíčku. Pokud používáte Visual Studio, spusťte `npm install` z **Konzola správce balíčků** během činnosti v kořenové složce. Visual Studio Code, spusťte příkaz z **integrovaný terminál**.

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

## <a name="use-the-signalr-javascript-client"></a>Použití klienta SignalR JavaScript

Odkazovat na klientovi SignalR JavaScript v `<script>` elementu.

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
> Pokud používáte službu Azure Signaler v režimu bez *serveru*, nemůžete volat metody centra z klienta. Další informace najdete v dokumentaci ke [službě signaler](/azure/azure-signalr/signalr-concept-serverless-development-config).

Metoda vrací příslib JavaScriptu. [](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) `invoke` `Promise` Je vyřešen s návratovou hodnotou (pokud existuje), když metoda na serveru vrátí. Pokud metoda na serveru vyvolá chybu, bude `Promise` zpráva odmítnuta s chybovou zprávou. Použijte metody `then` a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).

Metoda vrátí JavaScript `Promise`. `send` `Promise` Je vyřešena při odeslání zprávy na server. Pokud při odesílání zprávy dojde k chybě, bude `Promise` zpráva odmítnuta s chybovou zprávou. Použijte metody `then` a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).

> [!NOTE]
> Použití `send` nástroje nečeká na přijetí zprávy serverem. V důsledku toho není možné vracet data nebo chyby ze serveru.

## <a name="call-client-methods-from-hub"></a>Volání metody klienta od rozbočovače

Chcete-li přijímat zprávy z centra, definovat metodu pomocí [na](/javascript/api/%40aspnet/signalr/hubconnection#on) metodu `HubConnection`.

* Název metody jazyka JavaScript. V následujícím příkladu je název metody `ReceiveMessage`.
* Argumenty, které se předá metodě rozbočovače. V následujícím příkladu je hodnota argumentu `message`.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Předchozí kód v `connection.on` spustí, když kód na straně serveru pomocí volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) metoda.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR Určuje, jakou metodu klienta volat to provede spárováním odpovídajících názvu metody a argumenty definovaných v `SendAsync` a `connection.on`.

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

Klient JavaScriptu pro Signal se dá nakonfigurovat tak, aby se automaticky znovu připojil pomocí `withAutomaticReconnect` metody v [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Ve výchozím nastavení se automaticky znovu nepřipojí.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Bez parametrů nakonfiguruje `withAutomaticReconnect()` klienta, aby čekal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.

Před zahájením jakýchkoli pokusů o `HubConnection` opětovné připojení se přechod `HubConnectionState.Reconnecting` do stavu a aktivuje `onreconnecting` jeho `Disconnected` zpětná volání namísto přechodu do stavu a aktivace jeho `onclose` zpětného volání jako `HubConnection`bez nakonfigurovaného automatického opětovného připojení. Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, `HubConnection` převede se zpátky `Connected` do stavu a spustí `onreconnected` zpětná volání. To vám umožní informovat uživatele o tom, že bylo připojení znovu navázáno.

Vzhledem k tomu, že připojení na serveru zcela začíná, bude `connectionId` `onreconnected` zpětnému volání k dispozici nový.

> [!WARNING]
> Parametr zpětného volání bude nedefinován, pokud `HubConnection` byl nakonfigurován tak, aby přeskočil [vyjednávání](xref:signalr/configuration#configure-client-options). `onreconnected` `connectionId`

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`neprovede konfiguraci `HubConnection` pro opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:

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

Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde `Disconnected` do stavu a aktivuje jeho zpětné volání při jeho [ukončení](/javascript/api/%40aspnet/signalr/hubconnection#onclose) . To vám umožní informovat uživatele o trvalé ztrátě připojení a doporučit aktualizaci stránky:

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování pro opětovné připojení, přijme `withAutomaticReconnect` pole čísel představující zpoždění v milisekundách, které se má počkat, než se spustí pokus o opětovné připojení.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

V předchozím příkladu se nakonfiguruje tak, `HubConnection` aby se při pokusu o opětovné připojení ihned po ztrátě připojení spouštěla znovu. To platí také pro výchozí konfiguraci.

Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.

Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.

Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o ukončení pokusu o opětovné připojení v dalších 30 sekundách, jako by to bylo ve výchozí konfiguraci.

Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení `withAutomaticReconnect` , je třeba přijmout objekt `IRetryPolicy` implementující rozhraní, které má jedinou metodu `nextRetryDelayInMilliseconds`s názvem.

`nextRetryDelayInMilliseconds`přijímá jeden argument s typem `RetryContext`. `elapsedMilliseconds` Mátři`Error` vlastnosti: `previousRetryCount` ,`number` a `retryReason` , kteréjsouavuvedenémpořadí.`number` `RetryContext` Před prvním pokusem o opětovné připojení budou `previousRetryCount` obě `elapsedMilliseconds` i nulové a `retryReason` dojde k chybě, která způsobila ztrátu připojení. Po každém neúspěšném pokusu o `previousRetryCount` opakování se bude aktualizovat o jednu, `elapsedMilliseconds` aby odrážela dobu strávenou opětovným opětovným připojením `retryReason` v milisekundách. výsledkem bude chyba, která způsobila pokus o poslední opětovné připojení. proběhne.

`nextRetryDelayInMilliseconds`musí vracet buď číslo představující počet milisekund, po které se má čekat před dalším pokusem o opětovné `null` připojení, `HubConnection` nebo jestli se má zastavit opětovné připojení.

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
> Před 3,0 se klient JavaScript for Signal automaticky znovu nepřipojí. Musíte napsat kód, který se znovu připojit klientu ručně.

::: moniker-end

Následující kód ukazuje typický postup ručního opětovného připojení:

1. Funkce (v tomto případě `start` funkce) se vytvoří připojení spustíte.
1. Volání `start` funkce v rámci připojení `onclose` obslužné rutiny události.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Skutečná implementace by použít exponenciální regrese nebo opakování zadaného počtu opakování, než se ukončí.

## <a name="additional-resources"></a>Další zdroje

* [Referenční dokumentace k rozhraní API v JavaScriptu](/javascript/api/?view=signalr-js-latest)
* [Kurz jazyka JavaScript](xref:tutorials/signalr)
* [Kurz Webpacku a TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
* [Požadavky mezi zdroji (CORS)](xref:security/cors)
* [Dokumentace ke službě Azure Signaler bez serveru](/azure/azure-signalr/signalr-concept-serverless-development-config)
