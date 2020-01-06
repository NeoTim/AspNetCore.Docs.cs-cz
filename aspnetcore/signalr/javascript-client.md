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
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="08730-103">Klient ASP.NET Core SignalR JavaScript</span><span class="sxs-lookup"><span data-stu-id="08730-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="08730-104">Podle [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="08730-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="08730-105">Klientská knihovna ASP.NET Core SignalR JavaScript umožňuje vývojářům volat kód centra na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="08730-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="08730-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="08730-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="08730-107">Instalace balíčku klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="08730-107">Install the SignalR client package</span></span>

<span data-ttu-id="08730-108">Klientská knihovna SignalR JavaScript je dodávána jako balíček [npm](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="08730-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="08730-109">Pokud používáte Visual Studio, spusťte `npm install` z **Konzola správce balíčků** během činnosti v kořenové složce.</span><span class="sxs-lookup"><span data-stu-id="08730-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="08730-110">Visual Studio Code, spusťte příkaz z **integrovaný terminál**.</span><span class="sxs-lookup"><span data-stu-id="08730-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

<span data-ttu-id="08730-111">npm nainstaluje balíček obsahu *node_modules\\@microsoft\signalr\dist\browser* složky.</span><span class="sxs-lookup"><span data-stu-id="08730-111">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="08730-112">Vytvořte novou složku s názvem *signalr* pod *wwwroot\\lib* složky.</span><span class="sxs-lookup"><span data-stu-id="08730-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="08730-113">Kopírovat *signalr.js* do souboru *wwwroot\lib\signalr* složky.</span><span class="sxs-lookup"><span data-stu-id="08730-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="08730-114">npm nainstaluje balíček obsahu *node_modules\\@aspnet\signalr\dist\browser* složky.</span><span class="sxs-lookup"><span data-stu-id="08730-114">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="08730-115">Vytvořte novou složku s názvem *signalr* pod *wwwroot\\lib* složky.</span><span class="sxs-lookup"><span data-stu-id="08730-115">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="08730-116">Kopírovat *signalr.js* do souboru *wwwroot\lib\signalr* složky.</span><span class="sxs-lookup"><span data-stu-id="08730-116">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a><span data-ttu-id="08730-117">Použití klienta SignalR JavaScript</span><span class="sxs-lookup"><span data-stu-id="08730-117">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="08730-118">Odkaz na SignalR klient JavaScript v elementu `<script>`.</span><span class="sxs-lookup"><span data-stu-id="08730-118">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="08730-119">Připojení k rozbočovači</span><span class="sxs-lookup"><span data-stu-id="08730-119">Connect to a hub</span></span>

<span data-ttu-id="08730-120">Následující kód vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="08730-120">The following code creates and starts a connection.</span></span> <span data-ttu-id="08730-121">Název centra se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="08730-121">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="08730-122">Nepůvodního zdroje připojení</span><span class="sxs-lookup"><span data-stu-id="08730-122">Cross-origin connections</span></span>

<span data-ttu-id="08730-123">Obvykle prohlížeče načíst připojení ve stejné doméně jako požadovanou stránku.</span><span class="sxs-lookup"><span data-stu-id="08730-123">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="08730-124">Existují však situace, kdy je vyžadováno připojení k jiné doméně.</span><span class="sxs-lookup"><span data-stu-id="08730-124">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="08730-125">Aby se zabránilo škodlivým webům ve čtení citlivých dat z jiné lokality [nepůvodního zdroje připojení](xref:security/cors) jsou ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="08730-125">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="08730-126">Žádosti nepůvodního zdroje, povolit jej `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="08730-126">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="08730-127">Volání metod rozbočovače na z klienta</span><span class="sxs-lookup"><span data-stu-id="08730-127">Call hub methods from client</span></span>

<span data-ttu-id="08730-128">Klientům JavaScript volat veřejné metody rozbočovače prostřednictvím [vyvolat](/javascript/api/%40aspnet/signalr/hubconnection#invoke) metodu [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="08730-128">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="08730-129">`invoke` Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="08730-129">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="08730-130">Název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="08730-130">The name of the hub method.</span></span> <span data-ttu-id="08730-131">V následujícím příkladu je název metody rozbočovače `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="08730-131">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="08730-132">Všechny argumenty podle metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="08730-132">Any arguments defined in the hub method.</span></span> <span data-ttu-id="08730-133">V následujícím příkladu je název argumentu `message`.</span><span class="sxs-lookup"><span data-stu-id="08730-133">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="08730-134">Vzorový kód používá syntaxi funkce šipky, která je podporována v aktuálních verzích všech hlavních prohlížečů s výjimkou aplikace Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="08730-134">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="08730-135">Pokud používáte službu Azure SignalR v režimu bez *serveru*, nemůžete volat metody centra z klienta.</span><span class="sxs-lookup"><span data-stu-id="08730-135">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="08730-136">Další informace najdete v dokumentaci ke [služběSignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="08730-136">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="08730-137">Metoda `invoke` vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="08730-137">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="08730-138">`Promise` se vyřeší návratovou hodnotou (pokud existuje), když metoda na serveru vrátí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="08730-138">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="08730-139">Pokud metoda na serveru vyvolá chybu, `Promise` je odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="08730-139">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="08730-140">Pro zpracování těchto případů (nebo `await` syntaxe) použijte metody `then` a `catch` na `Promise` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="08730-140">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="08730-141">Metoda `send` vrátí `Promise`JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="08730-141">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="08730-142">`Promise` se vyřeší, když se do serveru pošle zpráva.</span><span class="sxs-lookup"><span data-stu-id="08730-142">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="08730-143">Pokud při odesílání zprávy dojde k chybě, `Promise` je odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="08730-143">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="08730-144">Pro zpracování těchto případů (nebo `await` syntaxe) použijte metody `then` a `catch` na `Promise` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="08730-144">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="08730-145">Použití `send` nečeká na přijetí zprávy serverem.</span><span class="sxs-lookup"><span data-stu-id="08730-145">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="08730-146">V důsledku toho není možné vracet data nebo chyby ze serveru.</span><span class="sxs-lookup"><span data-stu-id="08730-146">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="08730-147">Volání metody klienta od rozbočovače</span><span class="sxs-lookup"><span data-stu-id="08730-147">Call client methods from hub</span></span>

<span data-ttu-id="08730-148">Chcete-li přijímat zprávy z centra, definovat metodu pomocí [na](/javascript/api/%40aspnet/signalr/hubconnection#on) metodu `HubConnection`.</span><span class="sxs-lookup"><span data-stu-id="08730-148">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="08730-149">Název metody jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="08730-149">The name of the JavaScript client method.</span></span> <span data-ttu-id="08730-150">V následujícím příkladu je název metody `ReceiveMessage`.</span><span class="sxs-lookup"><span data-stu-id="08730-150">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="08730-151">Argumenty, které se předá metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="08730-151">Arguments the hub passes to the method.</span></span> <span data-ttu-id="08730-152">V následujícím příkladu je hodnota argumentu `message`.</span><span class="sxs-lookup"><span data-stu-id="08730-152">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="08730-153">Předchozí kód v `connection.on` spustí, když kód na straně serveru pomocí volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) metoda.</span><span class="sxs-lookup"><span data-stu-id="08730-153">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="08730-154"> určuje, která metoda klienta má být volána, porovnáním názvu metody a argumentů definovaných v `SendAsync` a `connection.on`.</span><span class="sxs-lookup"><span data-stu-id="08730-154"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="08730-155">Jako osvědčený postup volání [start](/javascript/api/%40aspnet/signalr/hubconnection#start) metodu `HubConnection` po `on`.</span><span class="sxs-lookup"><span data-stu-id="08730-155">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="08730-156">Tím se zajistí, že vaše obslužné rutiny jsou registrovány předtím, než jsou přijaty žádné zprávy.</span><span class="sxs-lookup"><span data-stu-id="08730-156">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="08730-157">Protokolování a zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="08730-157">Error handling and logging</span></span>

<span data-ttu-id="08730-158">Řetězce `catch` metoda na konec objektu `start` metodu ke zpracování chyby na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="08730-158">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="08730-159">Použití `console.error` chyby výstup do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="08730-159">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="08730-160">Nastavení na straně klienta protokolu trasování předáním protokolovací nástroj a typ události do protokolu, když se připojení.</span><span class="sxs-lookup"><span data-stu-id="08730-160">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="08730-161">Zprávy jsou zaznamenány na úrovni zadaný protokol a vyšší.</span><span class="sxs-lookup"><span data-stu-id="08730-161">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="08730-162">Dostupné úrovně jsou následující:</span><span class="sxs-lookup"><span data-stu-id="08730-162">Available log levels are as follows:</span></span>

* <span data-ttu-id="08730-163">`signalR.LogLevel.Error` &ndash; Chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="08730-163">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="08730-164">Protokoly `Error` pouze zprávy.</span><span class="sxs-lookup"><span data-stu-id="08730-164">Logs `Error` messages only.</span></span>
* <span data-ttu-id="08730-165">`signalR.LogLevel.Warning` &ndash; Zprávy s upozorněním potenciální chyby.</span><span class="sxs-lookup"><span data-stu-id="08730-165">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="08730-166">Protokoly `Warning`, a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="08730-166">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="08730-167">`signalR.LogLevel.Information` &ndash; Stavové zprávy bez chyb.</span><span class="sxs-lookup"><span data-stu-id="08730-167">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="08730-168">Protokoly `Information`, `Warning`, a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="08730-168">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="08730-169">`signalR.LogLevel.Trace` &ndash; Trasovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="08730-169">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="08730-170">Protokoly vše, včetně dat přenášených mezi centrem a klienta.</span><span class="sxs-lookup"><span data-stu-id="08730-170">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="08730-171">Použití [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) metoda [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) nakonfigurovat úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="08730-171">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="08730-172">Zprávy jsou protokolovány do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="08730-172">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="08730-173">Opětovné připojení klientů</span><span class="sxs-lookup"><span data-stu-id="08730-173">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="08730-174">Automaticky znovu připojit</span><span class="sxs-lookup"><span data-stu-id="08730-174">Automatically reconnect</span></span>

<span data-ttu-id="08730-175">Klient jazyka JavaScript pro SignalR lze nakonfigurovat tak, aby se automaticky znovu připojoval pomocí metody `withAutomaticReconnect` na [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="08730-175">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="08730-176">Ve výchozím nastavení se automaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="08730-176">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="08730-177">Bez parametrů `withAutomaticReconnect()` nakonfiguruje klienta tak, aby počkal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.</span><span class="sxs-lookup"><span data-stu-id="08730-177">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="08730-178">Před zahájením jakýchkoli pokusů o opětovné připojení `HubConnection` přechodem do stavu `HubConnectionState.Reconnecting` a spustí `onreconnecting` zpětná volání namísto přechodu do `Disconnected` stavu a aktivuje své `onclose` zpětné volání, jako je `HubConnection`, aniž by bylo nakonfigurováno automatické opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="08730-178">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="08730-179">Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="08730-179">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="08730-180">Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, `HubConnection` přejde zpět do stavu `Connected` a aktivuje jeho `onreconnected` zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="08730-180">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="08730-181">To vám umožní informovat uživatele o tom, že bylo připojení znovu navázáno.</span><span class="sxs-lookup"><span data-stu-id="08730-181">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="08730-182">Vzhledem k tomu, že připojení na serveru zcela začíná, bude k dispozici nový `connectionId` ke zpětnému volání `onreconnected`.</span><span class="sxs-lookup"><span data-stu-id="08730-182">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="08730-183">Parametr `connectionId` `onreconnected` zpětného volání bude nedefinován, pokud byl `HubConnection` nakonfigurován pro [přeskočení vyjednávání](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="08730-183">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="08730-184">`withAutomaticReconnect()` nenastaví `HubConnection` na opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:</span><span class="sxs-lookup"><span data-stu-id="08730-184">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="08730-185">Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde do stavu `Disconnected` a aktivuje jeho zpětné volání při jeho [ukončení](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="08730-185">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="08730-186">To vám umožní informovat uživatele o trvalé ztrátě připojení a doporučit aktualizaci stránky:</span><span class="sxs-lookup"><span data-stu-id="08730-186">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="08730-187">Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování opakovaného připojení, `withAutomaticReconnect` akceptuje pole čísel představujících zpoždění v milisekundách, které budou čekat před zahájením každého pokusu o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="08730-187">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="08730-188">V předchozím příkladu se nakonfiguruje `HubConnection`, aby se začaly znovu připojovat hned po ztrátě připojení.</span><span class="sxs-lookup"><span data-stu-id="08730-188">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="08730-189">To platí také pro výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="08730-189">This is also true for the default configuration.</span></span>

<span data-ttu-id="08730-190">Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="08730-190">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="08730-191">Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="08730-191">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="08730-192">Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o ukončení pokusu o opětovné připojení v dalších 30 sekundách, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="08730-192">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="08730-193">Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení, `withAutomaticReconnect` akceptuje objekt implementující rozhraní `IRetryPolicy`, které má jedinou metodu s názvem `nextRetryDelayInMilliseconds`.</span><span class="sxs-lookup"><span data-stu-id="08730-193">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="08730-194">`nextRetryDelayInMilliseconds` přijímá jeden argument typu `RetryContext`.</span><span class="sxs-lookup"><span data-stu-id="08730-194">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="08730-195">`RetryContext` má tři vlastnosti: `previousRetryCount`, `elapsedMilliseconds` a `retryReason`, které jsou `number`, `number` a `Error`.</span><span class="sxs-lookup"><span data-stu-id="08730-195">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="08730-196">Před prvním pokusem o opětovné připojení budou `previousRetryCount` i `elapsedMilliseconds` nula a `retryReason` dojde k chybě, která způsobila ztrátu připojení.</span><span class="sxs-lookup"><span data-stu-id="08730-196">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="08730-197">Po každém neúspěšném pokusu o opakování se `previousRetryCount` zvýší o jednu, `elapsedMilliseconds` se aktualizuje tak, aby odrážela dobu trvání opětovného připojení v milisekundách, a `retryReason` bude chyba, která způsobila selhání posledního pokusu o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="08730-197">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="08730-198">`nextRetryDelayInMilliseconds` musí vracet buď číslo představující počet milisekund, po který se má čekat před dalším pokusem o opětovné připojení, nebo `null`, pokud by se `HubConnection` mělo zastavit opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="08730-198">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="08730-199">Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="08730-199">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="08730-200">Ručně znovu připojit</span><span class="sxs-lookup"><span data-stu-id="08730-200">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="08730-201">Před 3,0 se klient JavaScript pro SignalR automaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="08730-201">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="08730-202">Musíte napsat kód, který se znovu připojit klientu ručně.</span><span class="sxs-lookup"><span data-stu-id="08730-202">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="08730-203">Následující kód ukazuje typický postup ručního opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="08730-203">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="08730-204">Funkce (v tomto případě `start` funkce) se vytvoří připojení spustíte.</span><span class="sxs-lookup"><span data-stu-id="08730-204">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="08730-205">Volání `start` funkce v rámci připojení `onclose` obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="08730-205">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="08730-206">Skutečná implementace by použít exponenciální regrese nebo opakování zadaného počtu opakování, než se ukončí.</span><span class="sxs-lookup"><span data-stu-id="08730-206">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08730-207">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="08730-207">Additional resources</span></span>

* [<span data-ttu-id="08730-208">Referenční dokumentace k rozhraní API v JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="08730-208">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="08730-209">Kurz jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="08730-209">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="08730-210">Kurz Webpacku a TypeScript</span><span class="sxs-lookup"><span data-stu-id="08730-210">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="08730-211">Centra</span><span class="sxs-lookup"><span data-stu-id="08730-211">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="08730-212">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="08730-212">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="08730-213">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="08730-213">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="08730-214">Požadavky mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="08730-214">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="08730-215">[Dokumentace k serveru služby Azure SignalR bez serveru](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="08730-215">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
