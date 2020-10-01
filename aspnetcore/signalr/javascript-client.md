---
title: SignalRKlient ASP.NET Core JavaScript
author: bradygaster
description: Přehled ASP.NET Core SignalR klienta jazyka JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: 6fc586d144547585ef75d653bf54193def5c8b7f
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606686"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a>SignalRKlient ASP.NET Core JavaScript

::: moniker range=">= aspnetcore-3.0"

Od [Rachel Appel](https://twitter.com/rachelappel)

SignalRKlientská knihovna ASP.NET Core JavaScript umožňuje vývojářům volat kód centra na straně serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="install-the-no-locsignalr-client-package"></a>Instalace SignalR balíčku klienta

SignalRKlientská knihovna JavaScriptu je dodávána jako balíček [npm](https://www.npmjs.com/) . Následující oddíly popisují různé způsoby instalace klientské knihovny.

### <a name="install-with-npm"></a>Instalace pomocí npm

V případě sady Visual Studio spusťte následující příkazy z **konzoly Správce balíčků** v kořenové složce. Pro Visual Studio Code spusťte následující příkazy z **integrovaného terminálu**.

```bash
npm init -y
npm install @microsoft/signalr
```

NPM nainstaluje obsah balíčku do složky *node_modules \\ @microsoft\signalr\dist\browser * . Vytvořte novou složku s názvem *Signal* ve složce *wwwroot \\ lib* . Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr* .

Odkazování na SignalR klienta JavaScriptu v `<script>` elementu. Například:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Použít Content Delivery Network (CDN)

Chcete-li použít klientskou knihovnu bez npm požadavků, proveďte odkaz na kopii klientské knihovny hostované v CDN. Například:

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

Klientská knihovna je k dispozici na následujících sítě CDN:

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a>Instalace pomocí LibMan

[LibMan](xref:client-side/libman/index) se dá použít k instalaci určitých souborů knihovny klienta z klientské knihovny hostované v CDN. Například do projektu přidejte pouze soubor JavaScriptu minifikovaného. Podrobnosti o tomto přístupu najdete v tématu [Přidání SignalR klientské knihovny](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Připojení k centru

Následující kód vytvoří a spustí připojení. V názvu centra se nerozlišují malá a velká písmena:

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a>Připojení mezi zdroji

Prohlížeče obvykle načítají připojení ze stejné domény jako požadovanou stránku. Existují však situace, kdy je vyžadováno připojení k jiné doméně.

Chcete-li zabránit škodlivému webu v čtení citlivých dat z jiné lokality, [připojení mezi zdroji](xref:security/cors) jsou ve výchozím nastavení zakázána. Pokud chcete povolit žádost o více zdrojů, povolte ji ve `Startup` třídě:

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a>Volání metod centra z klienta

Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection). `invoke`Metoda akceptuje:

* Název metody centra
* Jakékoli argumenty definované v metodě hub.

V následujícím příkladu je název metody v centru `SendMessage` . Druhý a třetí argument předaný k `invoke` mapování na metody `user` a `message` argumenty centra:

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> Volání metod rozbočovače z klienta je podporováno, pouze pokud používáte SignalR službu Azure ve *výchozím* režimu. Další informace najdete v tématu [Nejčastější dotazy (úložiště GitHub Azure-Signal)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

`invoke`Metoda vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu. `Promise`Je vyřešen s návratovou hodnotou (pokud existuje), když metoda na serveru vrátí. Pokud metoda na serveru vyvolá chybu, bude `Promise` zpráva odmítnuta s chybovou zprávou. Použijte `async` `await` metody a nebo `Promise` `then` a `catch` ke zpracování těchto případů.

Klienti JavaScriptu můžou také volat veřejné metody na rozbočovače prostřednictvím metody [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) objektu `HubConnection` . Na rozdíl od `invoke` metody `send` Metoda nečeká na odpověď ze serveru. `send`Metoda vrátí JavaScript `Promise` . `Promise`Je vyřešena při odeslání zprávy na server. Pokud při odesílání zprávy dojde k chybě, bude `Promise` zpráva odmítnuta s chybovou zprávou. Použijte `async` `await` metody a nebo `Promise` `then` a `catch` ke zpracování těchto případů.

> [!NOTE]
> Použití nástroje `send` nečeká na přijetí zprávy serverem. V důsledku toho není možné vracet data nebo chyby ze serveru.

## <a name="call-client-methods-from-the-hub"></a>Volání metod klienta z centra

Chcete-li přijímat zprávy z centra, definujte metodu pomocí metody [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) `HubConnection` .

* Název metody klienta jazyka JavaScript.
* Argumenty, které rozbočovač předává metodě.

V následujícím příkladu je název metody `ReceiveMessage` . Názvy argumentů jsou `user` a `message` :

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

Předchozí kód v aplikaci se `connection.on` spustí, když kód na straně serveru volá <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> metodu:

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

SignalR Určuje, která metoda klienta má být volána, a to tak, že odpovídá názvu metody a argumentům definovaným v `SendAsync` a `connection.on` .

> [!NOTE]
> Osvědčeným postupem je zavolat metodu [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) na `HubConnection` za `on` . Tím zajistíte, aby byly obslužné rutiny registrovány před přijetím jakýchkoli zpráv.

## <a name="error-handling-and-logging"></a>Zpracování chyb a protokolování

`try` `catch` `async` `await` `Promise` `catch` Pro zpracování chyb na straně klienta použijte metody a a nebo. Slouží `console.error` k výstupu chyb do konzoly prohlížeče:

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

Nastavte trasování protokolu na straně klienta předáním protokolovacího nástroje a typu události, které se mají protokolovat při navázání spojení. Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší. K dispozici jsou následující úrovně protokolu:

* `signalR.LogLevel.Error`: Chybové zprávy. Protokoluje `Error` pouze zprávy.
* `signalR.LogLevel.Warning`: Varovné zprávy o potenciálních chybách. Protokoly `Warning` a `Error` zprávy.
* `signalR.LogLevel.Information`: Stavové zprávy bez chyb. Protokoly `Information` , `Warning` a `Error` zprávy.
* `signalR.LogLevel.Trace`: Trace Messages. Zaznamená vše, včetně dat přepravovaných mezi centrem a klientem.

K nakonfigurování úrovně protokolu použijte metodu [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) pro [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) . Zprávy jsou protokolovány do konzoly prohlížeče:

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a>Znovu připojit klienty

### <a name="automatically-reconnect"></a>Automaticky znovu připojit

Klient jazyka JavaScript pro SignalR lze nakonfigurovat tak, aby se automaticky znovu připojil pomocí `withAutomaticReconnect` metody v [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Ve výchozím nastavení se automaticky znovu nepřipojí.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

Bez parametrů `withAutomaticReconnect()` nakonfiguruje klienta, aby čekal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.

Před zahájením jakýchkoli pokusů o opětovné připojení `HubConnection` přejdete do `HubConnectionState.Reconnecting` stavu a dojde k jeho `onreconnecting` zpětnému volání namísto přechodu do `Disconnected` stavu a aktivaci jeho `onclose` zpětného volání, jako je třeba `HubConnection` automatické opětovné připojení. Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, převede se `HubConnection` zpátky do `Connected` stavu a spustí zpětná `onreconnected` volání. To vám umožní informovat uživatele o tom, že bylo připojení znovu navázáno.

Vzhledem k tomu, že připojení na serveru zcela začíná, `connectionId` bude zpětnému volání k dispozici nový `onreconnected` .

> [!WARNING]
> `onreconnected`Parametr zpětného volání `connectionId` bude nedefinován, pokud `HubConnection` byl nakonfigurován tak, aby [přeskočil vyjednávání](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` neprovede konfiguraci `HubConnection` pro opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
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

Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování pro opětovné připojení, `withAutomaticReconnect` přijme pole čísel představující zpoždění v milisekundách, které se má počkat, než se spustí pokus o opětovné připojení.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

V předchozím příkladu se nakonfiguruje `HubConnection` tak, aby se při pokusu o opětovné připojení ihned po ztrátě připojení spouštěla znovu. To platí také pro výchozí konfiguraci.

Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.

Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.

Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o ukončení pokusu o opětovné připojení v dalších 30 sekundách, jako by to bylo ve výchozí konfiguraci.

Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení, je třeba `withAutomaticReconnect` přijmout objekt implementující `IRetryPolicy` rozhraní, které má jedinou metodu s názvem `nextRetryDelayInMilliseconds` .

`nextRetryDelayInMilliseconds` přijímá jeden argument s typem `RetryContext` . `RetryContext`Má tři vlastnosti: `previousRetryCount` , `elapsedMilliseconds` a `retryReason` , které jsou a v `number` `number` `Error` uvedeném pořadí. Před prvním pokusem o opětovné připojení budou obě `previousRetryCount` i `elapsedMilliseconds` nulové a `retryReason` dojde k chybě, která způsobila ztrátu připojení. Po každém neúspěšném pokusu o opakování se bude `previousRetryCount` aktualizovat o jednu, `elapsedMilliseconds` aby odrážela dobu strávenou opětovným opětovným připojením v milisekundách `retryReason` . výsledkem bude chyba, která způsobila selhání posledního pokusu o opětovné připojení.

`nextRetryDelayInMilliseconds` musí vracet buď číslo představující počet milisekund, po které se má čekat před dalším pokusem o opětovné připojení, nebo `null` jestli se `HubConnection` má zastavit opětovné připojení.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
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

### <a name="manually-reconnect"></a>Ručně znovu připojit

Následující kód ukazuje typický postup ručního opětovného připojení:

1. `start`Pro spuštění připojení je vytvořena funkce (v tomto případě funkce).
1. Zavolejte `start` funkci v `onclose` popisovači události připojení.

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

Implementace reálného světa využije exponenciální přerušení nebo opakuje zadaný počet opakování.

## <a name="troubleshoot-websocket-handshake-errors"></a>Řešení chyb handshake s protokolem WebSocket

Tato část poskytuje při pokusu o navázání připojení k rozbočovači ASP.NET Core k chybě při výjimce *metody handshake typu WebSocket handshake* SignalR .

### <a name="response-code-400-or-503"></a>Kód odpovědi 400 nebo 503

Pro následující chybu:

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

K této chybě obvykle dochází pouze v případě, že klient používá přenos pomocí protokolu WebSockets, ale protokol WebSockets není na serveru povolen.

### <a name="response-code-307"></a>Kód odpovědi 307

```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

K tomu často dochází, když SignalR hub Server:

* Naslouchá a reaguje na protokol HTTP i HTTPS.
* Je nakonfigurován tak, aby vynutil protokol HTTPS voláním `UseHttpsRedirection` v `Startup` nebo vynutila https prostřednictvím pravidla přepsání adresy URL.

Tato chyba se může vypříčinit zadáním adresy URL HTTP na straně klienta pomocí `.withUrl("http://xxx/HubName")` . Oprava pro tento případ upravuje kód tak, aby používal adresu URL protokolu HTTPS.

### <a name="response-code-404"></a>Kód odpovědi 404

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

Pokud aplikace funguje na místním hostiteli, ale po publikování na server služby IIS vrátí tuto chybu:

* Ověřte, že SignalR je aplikace ASP.NET Core hostovaná jako dílčí aplikace služby IIS.
* Nenastavujte adresu URL s pathbase dílčí aplikace na SignalR straně klienta JavaScript `.withUrl("/SubAppName/HubName")` .

## <a name="additional-resources"></a>Další zdroje informací

* [Referenční dokumentace k rozhraní API v JavaScriptu](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [Kurz JavaScriptu](xref:tutorials/signalr)
* [Kurz pro Webpack a TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
* [Žádosti mezi zdroji (CORS)](xref:security/cors)
* [SignalRDokumentace k serveru se službou Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Od [Rachel Appel](https://twitter.com/rachelappel)

SignalRKlientská knihovna ASP.NET Core JavaScript umožňuje vývojářům volat kód centra na straně serveru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="install-the-no-locsignalr-client-package"></a>Instalace SignalR balíčku klienta

SignalRKlientská knihovna JavaScriptu je dodávána jako balíček [npm](https://www.npmjs.com/) . Následující oddíly popisují různé způsoby instalace klientské knihovny.

### <a name="install-with-npm"></a>Instalace pomocí npm

Pokud používáte Visual Studio, spusťte následující příkazy z **konzoly Správce balíčků** v kořenové složce. Pro Visual Studio Code spusťte následující příkazy z **integrovaného terminálu**.

```bash
npm init -y
npm install @aspnet/signalr
```

NPM nainstaluje obsah balíčku do složky *node_modules \\ @aspnet\signalr\dist\browser * . Vytvořte novou složku s názvem *Signal* ve složce *wwwroot \\ lib* . Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr* .

Odkazování na SignalR klienta JavaScriptu v `<script>` elementu. Například:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Použít Content Delivery Network (CDN)

Chcete-li použít klientskou knihovnu bez npm požadavků, proveďte odkaz na kopii klientské knihovny hostované v CDN. Například:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

Klientská knihovna je k dispozici na následujících sítě CDN:

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a>Instalace pomocí LibMan

[LibMan](xref:client-side/libman/index) se dá použít k instalaci určitých souborů knihovny klienta z klientské knihovny hostované v CDN. Například do projektu přidejte pouze soubor JavaScriptu minifikovaného. Podrobnosti o tomto přístupu najdete v tématu [Přidání SignalR klientské knihovny](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Připojení k centru

Následující kód vytvoří a spustí připojení. V názvu centra se nerozlišují malá a velká písmena.

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a>Připojení mezi zdroji

Prohlížeče obvykle načítají připojení ze stejné domény jako požadovanou stránku. Existují však situace, kdy je vyžadováno připojení k jiné doméně.

Chcete-li zabránit škodlivému webu v čtení citlivých dat z jiné lokality, [připojení mezi zdroji](xref:security/cors) jsou ve výchozím nastavení zakázána. Pokud chcete povolit žádost o více zdrojů, povolte ji ve `Startup` třídě.

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Volání metod centra z klienta

Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). `invoke`Metoda přijímá dva argumenty:

* Název metody centra V následujícím příkladu je název metody v centru `SendMessage` .
* Jakékoli argumenty definované v metodě hub. V následujícím příkladu je název argumentu `message` . Vzorový kód používá syntaxi funkce šipky, která je podporována v aktuálních verzích všech hlavních prohlížečů s výjimkou aplikace Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Volání metod rozbočovače z klienta je podporováno, pouze pokud používáte SignalR službu Azure ve *výchozím* režimu. Další informace najdete v tématu [Nejčastější dotazy (úložiště GitHub Azure-Signal)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

`invoke`Metoda vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu. `Promise`Je vyřešen s návratovou hodnotou (pokud existuje), když metoda na serveru vrátí. Pokud metoda na serveru vyvolá chybu, bude `Promise` zpráva odmítnuta s chybovou zprávou. Použijte `then` metody a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).

`send`Metoda vrátí JavaScript `Promise` . `Promise`Je vyřešena při odeslání zprávy na server. Pokud při odesílání zprávy dojde k chybě, bude `Promise` zpráva odmítnuta s chybovou zprávou. Použijte `then` metody a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).

> [!NOTE]
> Použití nástroje `send` nečeká na přijetí zprávy serverem. V důsledku toho není možné vracet data nebo chyby ze serveru.

## <a name="call-client-methods-from-hub"></a>Volání metod klienta z centra

Chcete-li přijímat zprávy z centra, definujte metodu pomocí metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection` .

* Název metody klienta jazyka JavaScript. V následujícím příkladu je název metody `ReceiveMessage` .
* Argumenty, které rozbočovač předává metodě. V následujícím příkladu je hodnota argumentu `message` .

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

Předchozí kód v aplikaci se `connection.on` spustí, když kód na straně serveru volá <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> metodu.

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

SignalR Určuje, která metoda klienta má být volána, a to tak, že odpovídá názvu metody a argumentům definovaným v `SendAsync` a `connection.on` .

> [!NOTE]
> Osvědčeným postupem je zavolat metodu [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) na `HubConnection` za `on` . Tím zajistíte, aby byly obslužné rutiny registrovány před přijetím jakýchkoli zpráv.

## <a name="error-handling-and-logging"></a>Zpracování chyb a protokolování

Řetězení `catch` metody ke konci `start` metody za účelem zpracování chyb na straně klienta. Slouží `console.error` k výstupu chyb do konzoly prohlížeče.

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

Nastavte trasování protokolu na straně klienta předáním protokolovacího nástroje a typu události, které se mají protokolovat při navázání spojení. Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší. K dispozici jsou následující úrovně protokolu:

* `signalR.LogLevel.Error`: Chybové zprávy. Protokoluje `Error` pouze zprávy.
* `signalR.LogLevel.Warning`: Varovné zprávy o potenciálních chybách. Protokoly `Warning` a `Error` zprávy.
* `signalR.LogLevel.Information`: Stavové zprávy bez chyb. Protokoly `Information` , `Warning` a `Error` zprávy.
* `signalR.LogLevel.Trace`: Trace Messages. Zaznamená vše, včetně dat přepravovaných mezi centrem a klientem.

K nakonfigurování úrovně protokolu použijte metodu [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) pro [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) . Zprávy jsou protokolovány do konzoly prohlížeče.

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Znovu připojit klienty

### <a name="manually-reconnect"></a>Ručně znovu připojit

> [!WARNING]
> Před 3,0 se klient JavaScriptu pro SignalR neautomaticky znovu nepřipojí. Musíte napsat kód, který bude znovu připojit klienta ručně.

Následující kód ukazuje typický postup ručního opětovného připojení:

1. `start`Pro spuštění připojení je vytvořena funkce (v tomto případě funkce).
1. Zavolejte `start` funkci v `onclose` popisovači události připojení.

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

Implementace reálného světa využije exponenciální přerušení nebo opakuje zadaný počet opakování.

## <a name="additional-resources"></a>Další zdroje informací

* [Referenční dokumentace k rozhraní API v JavaScriptu](/javascript/api/?view=signalr-js-latest)
* [Kurz JavaScriptu](xref:tutorials/signalr)
* [Kurz pro Webpack a TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
* [Žádosti mezi zdroji (CORS)](xref:security/cors)
* [SignalRDokumentace k serveru se službou Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
