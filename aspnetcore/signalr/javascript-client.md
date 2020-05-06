---
title: Klient SignalR ASP.NET Core JavaScript
author: bradygaster
description: Přehled ASP.NET Core SignalR klienta jazyka JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 966e8e171752edb230f6da82203fd901b0fdeaab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768939"
---
# <a name="aspnet-core-signalr-javascript-client"></a>Klient SignalR ASP.NET Core JavaScript

Od [Rachel Appel](https://twitter.com/rachelappel)

Klientská SignalR knihovna ASP.NET Core JavaScript umožňuje vývojářům volat kód centra na straně serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>Instalace balíčku SignalR klienta

Klientská knihovna SignalR JavaScriptu je dodávána jako balíček [npm](https://www.npmjs.com/) . Následující oddíly popisují různé způsoby instalace klientské knihovny.

### <a name="install-with-npm"></a>Instalace pomocí npm

Pokud používáte Visual Studio, spusťte následující příkazy z **konzoly Správce balíčků** v kořenové složce. Pro Visual Studio Code spusťte následující příkazy z **integrovaného terminálu**.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

NPM nainstaluje obsah balíčku do složky *node_modules\\ * . Vytvořte novou složku s názvem *Signal* ve složce *wwwroot\\lib* . Zkopírujte soubor *Signal. js* do složky *wwwroot\lib\signalr* .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

NPM nainstaluje obsah balíčku do složky *node_modules\\ * . Vytvořte novou složku s názvem *Signal* ve složce *wwwroot\\lib* . Zkopírujte soubor *Signal. js* do složky *wwwroot\lib\signalr* .

::: moniker-end

Odkazování SignalR na klienta JavaScriptu v `<script>` elementu. Příklad:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Použít Content Delivery Network (CDN)

Chcete-li použít klientskou knihovnu bez npm požadavků, proveďte odkaz na kopii klientské knihovny hostované v CDN. Příklad:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

Klientská knihovna je k dispozici na následujících sítě CDN:

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

### <a name="install-with-libman"></a>Instalace pomocí LibMan

[LibMan](xref:client-side/libman/index) se dá použít k instalaci určitých souborů knihovny klienta z klientské knihovny hostované v CDN. Například do projektu přidejte pouze soubor JavaScriptu minifikovaného. Podrobnosti o tomto přístupu najdete v tématu [Přidání SignalR klientské knihovny](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Připojení k centru

Následující kód vytvoří a spustí připojení. V názvu centra se nerozlišují malá a velká písmena.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Připojení mezi zdroji

Prohlížeče obvykle načítají připojení ze stejné domény jako požadovanou stránku. Existují však situace, kdy je vyžadováno připojení k jiné doméně.

Chcete-li zabránit škodlivému webu v čtení citlivých dat z jiné lokality, [připojení mezi zdroji](xref:security/cors) jsou ve výchozím nastavení zakázána. Pokud chcete povolit žádost o více zdrojů, povolte ji ve `Startup` třídě.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Volání metod centra z klienta

Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). `invoke` Metoda přijímá dva argumenty:

* Název metody centra V následujícím příkladu je `SendMessage`název metody v centru.
* Jakékoli argumenty definované v metodě hub. V následujícím příkladu je `message`název argumentu. Vzorový kód používá syntaxi funkce šipky, která je podporována v aktuálních verzích všech hlavních prohlížečů s výjimkou aplikace Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Pokud používáte službu Azure SignalR v režimu bez *serveru*, nemůžete volat metody centra z klienta. Další informace najdete v dokumentaci ke [ SignalR službě](/azure/azure-signalr/signalr-concept-serverless-development-config).

`invoke` Metoda vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu. `Promise` Je vyřešen s návratovou hodnotou (pokud existuje), když metoda na serveru vrátí. Pokud metoda na serveru vyvolá chybu, bude `Promise` zpráva odmítnuta s chybovou zprávou. Použijte metody `then` a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).

`send` Metoda vrátí JavaScript `Promise`. `Promise` Je vyřešena při odeslání zprávy na server. Pokud při odesílání zprávy dojde k chybě, bude `Promise` zpráva odmítnuta s chybovou zprávou. Použijte metody `then` a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).

> [!NOTE]
> Použití `send` nástroje nečeká na přijetí zprávy serverem. V důsledku toho není možné vracet data nebo chyby ze serveru.

## <a name="call-client-methods-from-hub"></a>Volání metod klienta z centra

Chcete-li přijímat zprávy z centra, definujte metodu pomocí metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection`.

* Název metody klienta jazyka JavaScript. V následujícím příkladu je `ReceiveMessage`název metody.
* Argumenty, které rozbočovač předává metodě. V následujícím příkladu je `message`hodnota argumentu.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Předchozí kód v aplikaci `connection.on` se spustí, když kód na straně serveru ho volá pomocí metody [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRUrčuje, která metoda klienta má být volána, a to tak, že odpovídá `SendAsync` názvu `connection.on`metody a argumentům definovaným v a.

> [!NOTE]
> Osvědčeným postupem je zavolat metodu [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) na `HubConnection` za `on`. Tím zajistíte, aby byly obslužné rutiny registrovány před přijetím jakýchkoli zpráv.

## <a name="error-handling-and-logging"></a>Zpracování chyb a protokolování

Řetězení `catch` metody ke konci `start` metody za účelem zpracování chyb na straně klienta. Slouží `console.error` k výstupu chyb do konzoly prohlížeče.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

Nastavte trasování protokolu na straně klienta předáním protokolovacího nástroje a typu události, které se mají protokolovat při navázání spojení. Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší. K dispozici jsou následující úrovně protokolu:

* `signalR.LogLevel.Error`&ndash; Chybové zprávy. Protokoluje `Error` pouze zprávy.
* `signalR.LogLevel.Warning`&ndash; Zprávy upozorňující na potenciální chyby. Protokoly `Warning`a `Error` zprávy.
* `signalR.LogLevel.Information`&ndash; Stavové zprávy bez chyb. Protokoly `Information`, `Warning`a `Error` zprávy.
* `signalR.LogLevel.Trace`&ndash; Sleduje zprávy. Zaznamená vše, včetně dat přepravovaných mezi centrem a klientem.

K nakonfigurování úrovně protokolu použijte metodu [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) pro [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) . Zprávy jsou protokolovány do konzoly prohlížeče.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Znovu připojit klienty

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automaticky znovu připojit

Klient jazyka JavaScript pro SignalR lze nakonfigurovat tak, aby se automaticky znovu připojil `withAutomaticReconnect` pomocí metody v [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Ve výchozím nastavení se automaticky znovu nepřipojí.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Bez parametrů nakonfiguruje `withAutomaticReconnect()` klienta, aby čekal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.

Před zahájením jakýchkoli pokusů o opětovné `HubConnection` připojení přejdete do `HubConnectionState.Reconnecting` stavu a dojde k `onreconnecting` jeho zpětnému volání namísto přechodu do `Disconnected` stavu a aktivaci jeho `onclose` zpětného volání, jako je `HubConnection` třeba automatické opětovné připojení. Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, `HubConnection` převede se zpátky do `Connected` stavu a spustí zpětná `onreconnected` volání. To vám umožní informovat uživatele o tom, že bylo připojení znovu navázáno.

Vzhledem k tomu, že připojení na serveru zcela začíná, bude `connectionId` `onreconnected` zpětnému volání k dispozici nový.

> [!WARNING]
> `connectionId` `HubConnection` Parametr `onreconnected` zpětného volání bude nedefinován, pokud byl nakonfigurován tak, aby [přeskočil vyjednávání](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected(connectionId => {
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

Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde do `Disconnected` stavu a aktivuje jeho zpětné volání při jeho [ukončení](/javascript/api/%40aspnet/signalr/hubconnection#onclose) . To vám umožní informovat uživatele o trvalé ztrátě připojení a doporučit aktualizaci stránky:

```javascript
connection.onclose(error => {
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

`nextRetryDelayInMilliseconds`přijímá jeden argument s typem `RetryContext`. `RetryContext` Má `previousRetryCount`tři vlastnosti:, `elapsedMilliseconds` a `retryReason` `number`, `number` které jsou a v `Error` uvedeném pořadí. Před prvním pokusem o opětovné připojení budou `previousRetryCount` obě `elapsedMilliseconds` i nulové a `retryReason` dojde k chybě, která způsobila ztrátu připojení. Po každém neúspěšném pokusu o `previousRetryCount` opakování se bude aktualizovat o `elapsedMilliseconds` jednu, aby odrážela dobu strávenou opětovným opětovným připojením v milisekundách. výsledkem `retryReason` bude chyba, která způsobila selhání posledního pokusu o opětovné připojení.

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
        }
    })
    .build();
```

Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ručně znovu připojit

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Před 3,0 se klient JavaScriptu pro SignalR neautomaticky znovu nepřipojí. Musíte napsat kód, který bude znovu připojit klienta ručně.

::: moniker-end

Následující kód ukazuje typický postup ručního opětovného připojení:

1. Pro spuštění připojení je vytvořena funkce (v `start` tomto případě funkce).
1. Zavolejte `start` funkci v popisovači `onclose` události připojení.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Implementace reálného světa využije exponenciální přerušení nebo opakuje zadaný počet opakování.

## <a name="additional-resources"></a>Další zdroje

* [Referenční dokumentace k rozhraní API v JavaScriptu](/javascript/api/?view=signalr-js-latest)
* [Kurz JavaScriptu](xref:tutorials/signalr)
* [Kurz pro Webpack a TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Publikování aplikací do Azure](xref:signalr/publish-to-azure-web-app)
* [Žádosti mezi zdroji (CORS)](xref:security/cors)
* [Dokumentace SignalR k serveru se službou Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
