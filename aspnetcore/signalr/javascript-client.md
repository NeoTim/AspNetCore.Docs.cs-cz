---
title: ASP.NET Core SignalR JavaScript klienta
author: bradygaster
description: Přehled ASP.NET Core SignalR JavaScript klienta.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/javascript-client
ms.openlocfilehash: 8b645304b597db0c37fb9cd8814c493ca1c6ee62
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814964"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="36c82-103">ASP.NET Core SignalR JavaScript klienta</span><span class="sxs-lookup"><span data-stu-id="36c82-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="36c82-104">Podle [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="36c82-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="36c82-105">Klientská knihovna ASP.NET Core SignalR JavaScript umožňuje vývojářům volat kód rozbočovače na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="36c82-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="36c82-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36c82-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="36c82-107">Instalace balíčku pro klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="36c82-107">Install the SignalR client package</span></span>

<span data-ttu-id="36c82-108">Klientské knihovny SignalR JavaScript je dodávána jako [npm](https://www.npmjs.com/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="36c82-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="36c82-109">Pokud používáte Visual Studio, spusťte `npm install` z **Konzola správce balíčků** během činnosti v kořenové složce.</span><span class="sxs-lookup"><span data-stu-id="36c82-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="36c82-110">Visual Studio Code, spusťte příkaz z **integrovaný terminál**.</span><span class="sxs-lookup"><span data-stu-id="36c82-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="36c82-111">npm nainstaluje balíček obsahu *node_modules\\@aspnet\signalr\dist\browser* složky.</span><span class="sxs-lookup"><span data-stu-id="36c82-111">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="36c82-112">Vytvořte novou složku s názvem *signalr* pod *wwwroot\\lib* složky.</span><span class="sxs-lookup"><span data-stu-id="36c82-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="36c82-113">Kopírovat *signalr.js* do souboru *wwwroot\lib\signalr* složky.</span><span class="sxs-lookup"><span data-stu-id="36c82-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

## <a name="use-the-signalr-javascript-client"></a><span data-ttu-id="36c82-114">Použití klienta SignalR JavaScript</span><span class="sxs-lookup"><span data-stu-id="36c82-114">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="36c82-115">Odkazovat na klientovi SignalR JavaScript v `<script>` elementu.</span><span class="sxs-lookup"><span data-stu-id="36c82-115">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="36c82-116">Připojení k rozbočovači</span><span class="sxs-lookup"><span data-stu-id="36c82-116">Connect to a hub</span></span>

<span data-ttu-id="36c82-117">Následující kód vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="36c82-117">The following code creates and starts a connection.</span></span> <span data-ttu-id="36c82-118">Název centra se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="36c82-118">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="36c82-119">Nepůvodního zdroje připojení</span><span class="sxs-lookup"><span data-stu-id="36c82-119">Cross-origin connections</span></span>

<span data-ttu-id="36c82-120">Obvykle prohlížeče načíst připojení ve stejné doméně jako požadovanou stránku.</span><span class="sxs-lookup"><span data-stu-id="36c82-120">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="36c82-121">Existují však situace, kdy je vyžadováno připojení k jiné doméně.</span><span class="sxs-lookup"><span data-stu-id="36c82-121">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="36c82-122">Aby se zabránilo škodlivým webům ve čtení citlivých dat z jiné lokality [nepůvodního zdroje připojení](xref:security/cors) jsou ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="36c82-122">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="36c82-123">Žádosti nepůvodního zdroje, povolit jej `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="36c82-123">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="36c82-124">Volání metod rozbočovače na z klienta</span><span class="sxs-lookup"><span data-stu-id="36c82-124">Call hub methods from client</span></span>

<span data-ttu-id="36c82-125">Klientům JavaScript volat veřejné metody rozbočovače prostřednictvím [vyvolat](/javascript/api/%40aspnet/signalr/hubconnection#invoke) metodu [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="36c82-125">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="36c82-126">`invoke` Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="36c82-126">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="36c82-127">Název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="36c82-127">The name of the hub method.</span></span> <span data-ttu-id="36c82-128">V následujícím příkladu je název metody rozbočovače `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="36c82-128">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="36c82-129">Všechny argumenty podle metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="36c82-129">Any arguments defined in the hub method.</span></span> <span data-ttu-id="36c82-130">V následujícím příkladu je název argumentu `message`.</span><span class="sxs-lookup"><span data-stu-id="36c82-130">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="36c82-131">Příklad kódu používá syntaxe funkce šipku, která je podporována v aktuálních verzích všechny hlavní prohlížeče s výjimkou aplikace Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="36c82-131">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="36c82-132">Pokud používáte služby Azure SignalR v *bez serveru režimu*, nelze volat metody rozbočovače klienta.</span><span class="sxs-lookup"><span data-stu-id="36c82-132">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="36c82-133">Další informace najdete v tématu [dokumentace ke službě SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="36c82-133">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="36c82-134">`invoke` Metoda vrátí JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span><span class="sxs-lookup"><span data-stu-id="36c82-134">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="36c82-135">`Promise` Vyřeší s návratovou hodnotou (pokud existuje) při vrácení metody na serveru.</span><span class="sxs-lookup"><span data-stu-id="36c82-135">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="36c82-136">Pokud metoda na serveru vyvolá chybu, `Promise` byl odmítnut s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="36c82-136">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="36c82-137">Použití `then` a `catch` metody `Promise` samotný řešení těchto případů (nebo `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="36c82-137">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="36c82-138">`send` Metoda vrátí JavaScript `Promise`.</span><span class="sxs-lookup"><span data-stu-id="36c82-138">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="36c82-139">`Promise` Vyřeší, když zpráva byla odeslána na server.</span><span class="sxs-lookup"><span data-stu-id="36c82-139">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="36c82-140">Pokud dojde k chybě odesílání zprávy, `Promise` byl odmítnut s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="36c82-140">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="36c82-141">Použití `then` a `catch` metody `Promise` samotný řešení těchto případů (nebo `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="36c82-141">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="36c82-142">Pomocí `send` nečeká server přijal zprávu.</span><span class="sxs-lookup"><span data-stu-id="36c82-142">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="36c82-143">V důsledku toho není možné vrátit data nebo chyby ze serveru.</span><span class="sxs-lookup"><span data-stu-id="36c82-143">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="36c82-144">Volání metody klienta od rozbočovače</span><span class="sxs-lookup"><span data-stu-id="36c82-144">Call client methods from hub</span></span>

<span data-ttu-id="36c82-145">Chcete-li přijímat zprávy z centra, definovat metodu pomocí [na](/javascript/api/%40aspnet/signalr/hubconnection#on) metodu `HubConnection`.</span><span class="sxs-lookup"><span data-stu-id="36c82-145">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="36c82-146">Název metody jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="36c82-146">The name of the JavaScript client method.</span></span> <span data-ttu-id="36c82-147">V následujícím příkladu je název metody `ReceiveMessage`.</span><span class="sxs-lookup"><span data-stu-id="36c82-147">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="36c82-148">Argumenty, které se předá metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="36c82-148">Arguments the hub passes to the method.</span></span> <span data-ttu-id="36c82-149">V následujícím příkladu je hodnota argumentu `message`.</span><span class="sxs-lookup"><span data-stu-id="36c82-149">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="36c82-150">Předchozí kód v `connection.on` spustí, když kód na straně serveru pomocí volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) metoda.</span><span class="sxs-lookup"><span data-stu-id="36c82-150">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="36c82-151">SignalR Určuje, jakou metodu klienta volat to provede spárováním odpovídajících názvu metody a argumenty definovaných v `SendAsync` a `connection.on`.</span><span class="sxs-lookup"><span data-stu-id="36c82-151">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="36c82-152">Jako osvědčený postup volání [start](/javascript/api/%40aspnet/signalr/hubconnection#start) metodu `HubConnection` po `on`.</span><span class="sxs-lookup"><span data-stu-id="36c82-152">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="36c82-153">Tím se zajistí, že vaše obslužné rutiny jsou registrovány předtím, než jsou přijaty žádné zprávy.</span><span class="sxs-lookup"><span data-stu-id="36c82-153">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="36c82-154">Protokolování a zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="36c82-154">Error handling and logging</span></span>

<span data-ttu-id="36c82-155">Řetězce `catch` metoda na konec objektu `start` metodu ke zpracování chyby na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="36c82-155">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="36c82-156">Použití `console.error` chyby výstup do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="36c82-156">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="36c82-157">Nastavení na straně klienta protokolu trasování předáním protokolovací nástroj a typ události do protokolu, když se připojení.</span><span class="sxs-lookup"><span data-stu-id="36c82-157">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="36c82-158">Zprávy jsou zaznamenány na úrovni zadaný protokol a vyšší.</span><span class="sxs-lookup"><span data-stu-id="36c82-158">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="36c82-159">Dostupné úrovně jsou následující:</span><span class="sxs-lookup"><span data-stu-id="36c82-159">Available log levels are as follows:</span></span>

* <span data-ttu-id="36c82-160">`signalR.LogLevel.Error` &ndash; Chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="36c82-160">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="36c82-161">Protokoly `Error` pouze zprávy.</span><span class="sxs-lookup"><span data-stu-id="36c82-161">Logs `Error` messages only.</span></span>
* <span data-ttu-id="36c82-162">`signalR.LogLevel.Warning` &ndash; Zprávy s upozorněním potenciální chyby.</span><span class="sxs-lookup"><span data-stu-id="36c82-162">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="36c82-163">Protokoly `Warning`, a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="36c82-163">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="36c82-164">`signalR.LogLevel.Information` &ndash; Stavové zprávy bez chyb.</span><span class="sxs-lookup"><span data-stu-id="36c82-164">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="36c82-165">Protokoly `Information`, `Warning`, a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="36c82-165">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="36c82-166">`signalR.LogLevel.Trace` &ndash; Trasovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="36c82-166">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="36c82-167">Protokoly vše, včetně dat přenášených mezi centrem a klienta.</span><span class="sxs-lookup"><span data-stu-id="36c82-167">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="36c82-168">Použití [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) metoda [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) nakonfigurovat úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="36c82-168">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="36c82-169">Zprávy jsou protokolovány do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="36c82-169">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="36c82-170">Opětovné připojení klientů</span><span class="sxs-lookup"><span data-stu-id="36c82-170">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="36c82-171">Automaticky znovu připojila</span><span class="sxs-lookup"><span data-stu-id="36c82-171">Automatically reconnect</span></span>

<span data-ttu-id="36c82-172">JavaScript klienta pro funkci SignalR může být nakonfigurován k automaticky znovu připojila, pomocí `withAutomaticReconnect` metoda [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="36c82-172">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="36c82-173">Připojení nebude automaticky ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="36c82-173">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="36c82-174">Bez parametrů `withAutomaticReconnect()` nakonfiguruje klienta na 0, 2, 10 a 30 sekund, resp. před pokusem o každý pokus o volání metody reconnect, zastavování po čtyři neúspěšných pokusech o čekání.</span><span class="sxs-lookup"><span data-stu-id="36c82-174">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="36c82-175">Před zahájením jakékoli pokusy o volání metody reconnect `HubConnection` se přechod na `HubConnectionState.Reconnecting` stav a vyvolat jeho `onreconnecting` zpětná volání místo přechodu do `Disconnected` stavu a aktivuje se jeho `onclose` zpětná volání, jako jsou `HubConnection`bez opětovné připojení automaticky nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="36c82-175">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="36c82-176">To představuje příležitost a upozornit uživatele, že připojení bylo ztraceno zakážete prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="36c82-176">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="36c82-177">Pokud klient úspěšně obnoví v rámci své první čtyři pokusy `HubConnection` přejde zpět `Connected` stav a vyvolat jeho `onreconnected` zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="36c82-177">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="36c82-178">To představuje příležitost k informování uživatelů, které byly znovu navázat připojení.</span><span class="sxs-lookup"><span data-stu-id="36c82-178">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="36c82-179">Vzhledem k tomu, že připojení ověří zcela nový server, nový `connectionId` vám poskytneme `onreconnected` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="36c82-179">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="36c82-180">`onreconnected` Zpětného volání `connectionId` parametr bude definováno, pokud `HubConnection` nastavený tak, aby [přeskočit vyjednávání](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="36c82-180">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="36c82-181">`withAutomaticReconnect()` nenakonfiguruje `HubConnection` pokus o selhání prvního spuštění, budou muset ručně zpracovat selhání spuštění:</span><span class="sxs-lookup"><span data-stu-id="36c82-181">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="36c82-182">Pokud klient nebude znovu připojit úspěšně v rámci své první čtyři pokusy `HubConnection` se přechod na `Disconnected` stavu a aktivuje její [při zavření](/javascript/api/%40aspnet/signalr/hubconnection#onclose) zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="36c82-182">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="36c82-183">To představuje příležitost k informování uživatelů se trvale ztratil připojení a doporučujeme aktualizovat stránku:</span><span class="sxs-lookup"><span data-stu-id="36c82-183">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="36c82-184">Pokud chcete nakonfigurovat vlastní počet pokusů o nové připojení před odpojením nebo změnit časování volání metody reconnect `withAutomaticReconnect` přijímá pole čísel představující zpoždění v milisekundách pro čekání před zahájením každý pokus o volání metody reconnect.</span><span class="sxs-lookup"><span data-stu-id="36c82-184">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="36c82-185">Předchozí příklad nakonfiguruje `HubConnection` spustit okamžitě, jakmile dojde ke ztrátě připojení k pokusu o připojování.</span><span class="sxs-lookup"><span data-stu-id="36c82-185">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="36c82-186">To platí také pro výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="36c82-186">This is also true for the default configuration.</span></span>

<span data-ttu-id="36c82-187">Pokud první pokus o volání metody reconnect se nezdaří, druhý pokus o volání metody reconnect také začne okamžitě nečekáte, než 2 sekundy, jako by tomu bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="36c82-187">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="36c82-188">Pokud se druhý pokus o volání metody reconnect se nezdaří, třetí pokus o volání metody reconnect se spustí za 10 sekund, což je znovu jako výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="36c82-188">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="36c82-189">Vlastní chování pak diverges znovu výchozí chování zastavením po třetí volání metody reconnect pokusu spadne, místo aby zkusit jednu více znovu připojit pokus o jiné 30 sekund, jako by tomu bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="36c82-189">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="36c82-190">Pokud chcete, aby ještě větší kontrolu nad načasování a počet automaticky pokusů `withAutomaticReconnect` přijímá implementaci objektu `IReconnectPolicy` rozhraní, které obsahuje jedinou metodu s názvem `nextRetryDelayInMilliseconds`.</span><span class="sxs-lookup"><span data-stu-id="36c82-190">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IReconnectPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="36c82-191">`nextRetryDelayInMilliseconds` přebírá dva argumenty, `previousRetryCount` a `elapsedMilliseconds`, které jsou obě čísla.</span><span class="sxs-lookup"><span data-stu-id="36c82-191">`nextRetryDelayInMilliseconds` takes two arguments, `previousRetryCount` and `elapsedMilliseconds`, which are both numbers.</span></span> <span data-ttu-id="36c82-192">Před první pokus o volání metody reconnect obě `previousRetryCount` a `elapsedMilliseconds` bude nula.</span><span class="sxs-lookup"><span data-stu-id="36c82-192">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero.</span></span> <span data-ttu-id="36c82-193">Po každý pokus o opakování neúspěšných `previousRetryCount` se zvýší o jedna a `elapsedMilliseconds` bude aktualizován tak, aby odrážely množství času stráveného opětovné připojení, pokud v milisekundách.</span><span class="sxs-lookup"><span data-stu-id="36c82-193">After each failed retry attempt, `previousRetryCount` will be incremented by one and `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds.</span></span>

<span data-ttu-id="36c82-194">`nextRetryDelayInMilliseconds` musí vrátit buď číslo představující počet milisekund čekání před dalším pokusem volání metody reconnect nebo `null` Pokud `HubConnection` by se měla zastavit, opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="36c82-194">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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
        }
    })
    .build();
```

<span data-ttu-id="36c82-195">Alternativně můžete napsat kód, který se znovu připojit klientu ručně, jak je ukázáno v [ručně připojit](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="36c82-195">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="36c82-196">Ručně připojit</span><span class="sxs-lookup"><span data-stu-id="36c82-196">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="36c82-197">Než 3.0 JavaScript klienta pro funkci SignalR nebude automaticky znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="36c82-197">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="36c82-198">Musíte napsat kód, který se znovu připojit klientu ručně.</span><span class="sxs-lookup"><span data-stu-id="36c82-198">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="36c82-199">Následující kód ukazuje typický ručního nastavitelnou přístup:</span><span class="sxs-lookup"><span data-stu-id="36c82-199">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="36c82-200">Funkce (v tomto případě `start` funkce) se vytvoří připojení spustíte.</span><span class="sxs-lookup"><span data-stu-id="36c82-200">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="36c82-201">Volání `start` funkce v rámci připojení `onclose` obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="36c82-201">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="36c82-202">Skutečná implementace by použít exponenciální regrese nebo opakování zadaného počtu opakování, než se ukončí.</span><span class="sxs-lookup"><span data-stu-id="36c82-202">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36c82-203">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="36c82-203">Additional resources</span></span>

* [<span data-ttu-id="36c82-204">Referenční dokumentace k rozhraní API v JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="36c82-204">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="36c82-205">Kurz jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="36c82-205">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="36c82-206">Kurz Webpacku a TypeScript</span><span class="sxs-lookup"><span data-stu-id="36c82-206">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="36c82-207">Centra</span><span class="sxs-lookup"><span data-stu-id="36c82-207">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="36c82-208">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="36c82-208">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="36c82-209">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="36c82-209">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="36c82-210">Požadavky mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="36c82-210">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="36c82-211">Dokumentace Azure bez serveru služby SignalR</span><span class="sxs-lookup"><span data-stu-id="36c82-211">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
