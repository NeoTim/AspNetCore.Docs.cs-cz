---
title: ASP.NET Core SignalR JavaScript klienta
author: bradygaster
description: Přehled ASP.NET Core SignalR JavaScript klienta.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/javascript-client
ms.openlocfilehash: f1f072e63928502fa1bad62e808ff035e57f2fd3
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59983010"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR JavaScript klienta

Podle [Rachel Appel](http://twitter.com/rachelappel)

Klientská knihovna ASP.NET Core SignalR JavaScript umožňuje vývojářům volat kód rozbočovače na straně serveru.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>Instalace balíčku pro klienta SignalR

Klientské knihovny SignalR JavaScript je dodávána jako [npm](https://www.npmjs.com/) balíčku. Pokud používáte Visual Studio, spusťte `npm install` z **Konzola správce balíčků** během činnosti v kořenové složce. Visual Studio Code, spusťte příkaz z **integrovaný terminál**.

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

npm nainstaluje balíček obsahu *node_modules\\@aspnet\signalr\dist\browser* složky. Vytvořte novou složku s názvem *signalr* pod *wwwroot\\lib* složky. Kopírovat *signalr.js* do souboru *wwwroot\lib\signalr* složky.

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
* Všechny argumenty podle metody rozbočovače. V následujícím příkladu je název argumentu `message`. Příklad kódu používá syntaxe funkce šipku, která je podporována v aktuálních verzích všechny hlavní prohlížeče s výjimkou aplikace Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Pokud používáte služby Azure SignalR v *bez serveru režimu*, nelze volat metody rozbočovače klienta. Další informace najdete v tématu [dokumentace ke službě SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).

`invoke` Metoda vrátí JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise). `Promise` Vyřeší s návratovou hodnotou (pokud existuje) při vrácení metody na serveru. Pokud metoda na serveru vyvolá chybu, `Promise` byl odmítnut s chybovou zprávou. Použití `then` a `catch` metody `Promise` samotný řešení těchto případů (nebo `await` syntaxe).

`send` Metoda vrátí JavaScript `Promise`. `Promise` Vyřeší, když zpráva byla odeslána na server. Pokud dojde k chybě odesílání zprávy, `Promise` byl odmítnut s chybovou zprávou. Použití `then` a `catch` metody `Promise` samotný řešení těchto případů (nebo `await` syntaxe).

> [!NOTE]
> Pomocí `send` nečeká server přijal zprávu. V důsledku toho není možné vrátit data nebo chyby ze serveru.

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

### <a name="automatically-reconnect"></a>Automaticky znovu připojila

JavaScript klienta pro funkci SignalR může být nakonfigurován k automaticky znovu připojila, pomocí `withAutomaticReconnect` metoda [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Připojení nebude automaticky ve výchozím nastavení.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Bez parametrů `withAutomaticReconnect()` nakonfiguruje klienta na 0, 2, 10 a 30 sekund, resp. před pokusem o každý pokus o volání metody reconnect, zastavování po čtyři neúspěšných pokusech o čekání.

Před zahájením jakékoli pokusy o volání metody reconnect `HubConnection` se přechod na `HubConnectionState.Reconnecting` stav a vyvolat jeho `onreconnecting` zpětná volání místo přechodu do `Disconnected` stavu a aktivuje se jeho `onclose` zpětná volání, jako jsou `HubConnection`bez opětovné připojení automaticky nakonfigurované. To představuje příležitost a upozornit uživatele, že připojení bylo ztraceno zakážete prvky uživatelského rozhraní.

```javascript
connection.onreconnecting((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
  document.getElementById("messagesList").appendChild(li);
});
```

Pokud klient úspěšně obnoví v rámci své první čtyři pokusy `HubConnection` přejde zpět `Connected` stav a vyvolat jeho `onreconnected` zpětná volání. To představuje příležitost k informování uživatelů, které byly znovu navázat připojení.

Vzhledem k tomu, že připojení ověří zcela nový server, nový `connectionId` vám poskytneme `onreconnected` zpětného volání.

> [!WARNING]
> `onreconnected` Zpětného volání `connectionId` parametr bude definováno, pokud `HubConnection` nastavený tak, aby [přeskočit vyjednávání](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected((connectionId) => {
  console.assert(connection.state === signalR.HubConnectionState.Connected);

  document.getElementById("messageInput").disabled = false;

  const li = document.createElement("li");
  li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
  document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` nenakonfiguruje `HubConnection` pokus o selhání prvního spuštění, budou muset ručně zpracovat selhání spuštění:

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

Pokud klient nebude znovu připojit úspěšně v rámci své první čtyři pokusy `HubConnection` se přechod na `Disconnected` stavu a aktivuje její [při zavření](/javascript/api/%40aspnet/signalr/hubconnection#onclose) zpětná volání. To představuje příležitost k informování uživatelů se trvale ztratil připojení a doporučujeme aktualizovat stránku:

```javascript
connection.onclose((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Disconnected);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
  document.getElementById("messagesList").appendChild(li);
})
```

Pokud chcete nakonfigurovat vlastní počet pokusů o nové připojení před odpojením nebo změnit časování volání metody reconnect `withAutomaticReconnect` přijímá pole čísel představující zpoždění v milisekundách pro čekání před zahájením každý pokus o volání metody reconnect.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Předchozí příklad nakonfiguruje `HubConnection` spustit okamžitě, jakmile dojde ke ztrátě připojení k pokusu o připojování. To platí také pro výchozí konfiguraci.

Pokud první pokus o volání metody reconnect se nezdaří, druhý pokus o volání metody reconnect také začne okamžitě nečekáte, než 2 sekundy, jako by tomu bylo ve výchozí konfiguraci.

Pokud se druhý pokus o volání metody reconnect se nezdaří, třetí pokus o volání metody reconnect se spustí za 10 sekund, což je znovu jako výchozí konfiguraci.

Vlastní chování pak diverges znovu výchozí chování zastavením po třetí volání metody reconnect pokusu spadne, místo aby zkusit jednu více znovu připojit pokus o jiné 30 sekund, jako by tomu bylo ve výchozí konfiguraci.

Pokud chcete, aby ještě větší kontrolu nad načasování a počet automaticky pokusů `withAutomaticReconnect` přijímá implementaci objektu `IReconnectPolicy` rozhraní, které obsahuje jedinou metodu s názvem `nextRetryDelayInMilliseconds`.

`nextRetryDelayInMilliseconds` přebírá dva argumenty, `previousRetryCount` a `elapsedMilliseconds`, které jsou obě čísla. Před první pokus o volání metody reconnect obě `previousRetryCount` a `elapsedMilliseconds` bude nula. Po každý pokus o opakování neúspěšných `previousRetryCount` se zvýší o jedna a `elapsedMilliseconds` bude aktualizován tak, aby odrážely množství času stráveného opětovné připojení, pokud v milisekundách.

`nextRetryDelayInMilliseconds` musí vrátit buď číslo představující počet milisekund čekání před dalším pokusem volání metody reconnect nebo `null` Pokud `HubConnection` by se měla zastavit, opětovné připojení.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: (previousRetryCount, elapsedMilliseconds) => {
          if (elapsedMilliseconds < 60000) {
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

Alternativně můžete napsat kód, který se znovu připojit klientu ručně, jak je ukázáno v [ručně připojit](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ručně připojit

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Než 3.0 JavaScript klienta pro funkci SignalR nebude automaticky znovu připojit. Musíte napsat kód, který se znovu připojit klientu ručně.

::: moniker-end

Následující kód ukazuje typický ručního nastavitelnou přístup:

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
* [Dokumentace Azure bez serveru služby SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)
