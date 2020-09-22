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
ms.openlocfilehash: 359aa2b9e6b7f826d75f10645b7f2b565ab48b7a
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847686"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="80b75-103">SignalRKlient ASP.NET Core JavaScript</span><span class="sxs-lookup"><span data-stu-id="80b75-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="80b75-104">Od [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="80b75-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="80b75-105">SignalRKlientská knihovna ASP.NET Core JavaScript umožňuje vývojářům volat kód centra na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="80b75-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="80b75-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="80b75-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="80b75-107">Instalace SignalR balíčku klienta</span><span class="sxs-lookup"><span data-stu-id="80b75-107">Install the SignalR client package</span></span>

<span data-ttu-id="80b75-108">SignalRKlientská knihovna JavaScriptu je dodávána jako balíček [npm](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="80b75-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="80b75-109">Následující oddíly popisují různé způsoby instalace klientské knihovny.</span><span class="sxs-lookup"><span data-stu-id="80b75-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="80b75-110">Instalace pomocí npm</span><span class="sxs-lookup"><span data-stu-id="80b75-110">Install with npm</span></span>

<span data-ttu-id="80b75-111">V případě sady Visual Studio spusťte následující příkazy z **konzoly Správce balíčků** v kořenové složce.</span><span class="sxs-lookup"><span data-stu-id="80b75-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="80b75-112">Pro Visual Studio Code spusťte následující příkazy z **integrovaného terminálu**.</span><span class="sxs-lookup"><span data-stu-id="80b75-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="80b75-113">NPM nainstaluje obsah balíčku do složky \*node_modules \\ @microsoft\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="80b75-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="80b75-114">Vytvořte novou složku s názvem *Signal* ve složce *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="80b75-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="80b75-115">Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="80b75-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="80b75-116">Odkazování na SignalR klienta JavaScriptu v `<script>` elementu.</span><span class="sxs-lookup"><span data-stu-id="80b75-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="80b75-117">Příklad:</span><span class="sxs-lookup"><span data-stu-id="80b75-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="80b75-118">Použít Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="80b75-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="80b75-119">Chcete-li použít klientskou knihovnu bez npm požadavků, proveďte odkaz na kopii klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="80b75-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="80b75-120">Příklad:</span><span class="sxs-lookup"><span data-stu-id="80b75-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="80b75-121">Klientská knihovna je k dispozici na následujících sítě CDN:</span><span class="sxs-lookup"><span data-stu-id="80b75-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="80b75-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="80b75-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="80b75-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="80b75-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="80b75-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="80b75-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="80b75-125">Instalace pomocí LibMan</span><span class="sxs-lookup"><span data-stu-id="80b75-125">Install with LibMan</span></span>

<span data-ttu-id="80b75-126">[LibMan](xref:client-side/libman/index) se dá použít k instalaci určitých souborů knihovny klienta z klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="80b75-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="80b75-127">Například do projektu přidejte pouze soubor JavaScriptu minifikovaného.</span><span class="sxs-lookup"><span data-stu-id="80b75-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="80b75-128">Podrobnosti o tomto přístupu najdete v tématu [Přidání SignalR klientské knihovny](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="80b75-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="80b75-129">Připojení k centru</span><span class="sxs-lookup"><span data-stu-id="80b75-129">Connect to a hub</span></span>

<span data-ttu-id="80b75-130">Následující kód vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="80b75-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="80b75-131">V názvu centra se nerozlišují malá a velká písmena:</span><span class="sxs-lookup"><span data-stu-id="80b75-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="80b75-132">Připojení mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="80b75-132">Cross-origin connections</span></span>

<span data-ttu-id="80b75-133">Prohlížeče obvykle načítají připojení ze stejné domény jako požadovanou stránku.</span><span class="sxs-lookup"><span data-stu-id="80b75-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="80b75-134">Existují však situace, kdy je vyžadováno připojení k jiné doméně.</span><span class="sxs-lookup"><span data-stu-id="80b75-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="80b75-135">Chcete-li zabránit škodlivému webu v čtení citlivých dat z jiné lokality, [připojení mezi zdroji](xref:security/cors) jsou ve výchozím nastavení zakázána.</span><span class="sxs-lookup"><span data-stu-id="80b75-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="80b75-136">Pokud chcete povolit žádost o více zdrojů, povolte ji ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="80b75-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="80b75-137">Volání metod centra z klienta</span><span class="sxs-lookup"><span data-stu-id="80b75-137">Call hub methods from the client</span></span>

<span data-ttu-id="80b75-138">Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="80b75-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="80b75-139">`invoke`Metoda akceptuje:</span><span class="sxs-lookup"><span data-stu-id="80b75-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="80b75-140">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="80b75-140">The name of the hub method.</span></span>
* <span data-ttu-id="80b75-141">Jakékoli argumenty definované v metodě hub.</span><span class="sxs-lookup"><span data-stu-id="80b75-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="80b75-142">V následujícím příkladu je název metody v centru `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="80b75-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="80b75-143">Druhý a třetí argument předaný k `invoke` mapování na metody `user` a `message` argumenty centra:</span><span class="sxs-lookup"><span data-stu-id="80b75-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="80b75-144">Volání metod rozbočovače z klienta je podporováno, pouze pokud používáte SignalR službu Azure ve *výchozím* režimu.</span><span class="sxs-lookup"><span data-stu-id="80b75-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="80b75-145">Další informace najdete v tématu [Nejčastější dotazy (úložiště GitHub Azure-Signal)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="80b75-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="80b75-146">`invoke`Metoda vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="80b75-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="80b75-147">`Promise`Je vyřešen s návratovou hodnotou (pokud existuje), když metoda na serveru vrátí.</span><span class="sxs-lookup"><span data-stu-id="80b75-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="80b75-148">Pokud metoda na serveru vyvolá chybu, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="80b75-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="80b75-149">Použijte `async` `await` metody a nebo `Promise` `then` a `catch` ke zpracování těchto případů.</span><span class="sxs-lookup"><span data-stu-id="80b75-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="80b75-150">Klienti JavaScriptu můžou také volat veřejné metody na rozbočovače prostřednictvím metody [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) objektu `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="80b75-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="80b75-151">Na rozdíl od `invoke` metody `send` Metoda nečeká na odpověď ze serveru.</span><span class="sxs-lookup"><span data-stu-id="80b75-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="80b75-152">`send`Metoda vrátí JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="80b75-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="80b75-153">`Promise`Je vyřešena při odeslání zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="80b75-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="80b75-154">Pokud při odesílání zprávy dojde k chybě, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="80b75-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="80b75-155">Použijte `async` `await` metody a nebo `Promise` `then` a `catch` ke zpracování těchto případů.</span><span class="sxs-lookup"><span data-stu-id="80b75-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="80b75-156">Použití nástroje `send` nečeká na přijetí zprávy serverem.</span><span class="sxs-lookup"><span data-stu-id="80b75-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="80b75-157">V důsledku toho není možné vracet data nebo chyby ze serveru.</span><span class="sxs-lookup"><span data-stu-id="80b75-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="80b75-158">Volání metod klienta z centra</span><span class="sxs-lookup"><span data-stu-id="80b75-158">Call client methods from the hub</span></span>

<span data-ttu-id="80b75-159">Chcete-li přijímat zprávy z centra, definujte metodu pomocí metody [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="80b75-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="80b75-160">Název metody klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80b75-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="80b75-161">Argumenty, které rozbočovač předává metodě.</span><span class="sxs-lookup"><span data-stu-id="80b75-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="80b75-162">V následujícím příkladu je název metody `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="80b75-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="80b75-163">Názvy argumentů jsou `user` a `message` :</span><span class="sxs-lookup"><span data-stu-id="80b75-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="80b75-164">Předchozí kód v aplikaci se `connection.on` spustí, když kód na straně serveru volá <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> metodu:</span><span class="sxs-lookup"><span data-stu-id="80b75-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="80b75-165">SignalR Určuje, která metoda klienta má být volána, a to tak, že odpovídá názvu metody a argumentům definovaným v `SendAsync` a `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="80b75-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="80b75-166">Osvědčeným postupem je zavolat metodu [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) na `HubConnection` za `on` .</span><span class="sxs-lookup"><span data-stu-id="80b75-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="80b75-167">Tím zajistíte, aby byly obslužné rutiny registrovány před přijetím jakýchkoli zpráv.</span><span class="sxs-lookup"><span data-stu-id="80b75-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="80b75-168">Zpracování chyb a protokolování</span><span class="sxs-lookup"><span data-stu-id="80b75-168">Error handling and logging</span></span>

<span data-ttu-id="80b75-169">`try` `catch` `async` `await` `Promise` `catch` Pro zpracování chyb na straně klienta použijte metody a a nebo.</span><span class="sxs-lookup"><span data-stu-id="80b75-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="80b75-170">Slouží `console.error` k výstupu chyb do konzoly prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="80b75-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="80b75-171">Nastavte trasování protokolu na straně klienta předáním protokolovacího nástroje a typu události, které se mají protokolovat při navázání spojení.</span><span class="sxs-lookup"><span data-stu-id="80b75-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="80b75-172">Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší.</span><span class="sxs-lookup"><span data-stu-id="80b75-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="80b75-173">K dispozici jsou následující úrovně protokolu:</span><span class="sxs-lookup"><span data-stu-id="80b75-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="80b75-174">`signalR.LogLevel.Error`: Chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="80b75-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="80b75-175">Protokoluje `Error` pouze zprávy.</span><span class="sxs-lookup"><span data-stu-id="80b75-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="80b75-176">`signalR.LogLevel.Warning`: Varovné zprávy o potenciálních chybách.</span><span class="sxs-lookup"><span data-stu-id="80b75-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="80b75-177">Protokoly `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="80b75-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="80b75-178">`signalR.LogLevel.Information`: Stavové zprávy bez chyb.</span><span class="sxs-lookup"><span data-stu-id="80b75-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="80b75-179">Protokoly `Information` , `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="80b75-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="80b75-180">`signalR.LogLevel.Trace`: Trace Messages.</span><span class="sxs-lookup"><span data-stu-id="80b75-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="80b75-181">Zaznamená vše, včetně dat přepravovaných mezi centrem a klientem.</span><span class="sxs-lookup"><span data-stu-id="80b75-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="80b75-182">K nakonfigurování úrovně protokolu použijte metodu [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) pro [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="80b75-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="80b75-183">Zprávy jsou protokolovány do konzoly prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="80b75-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="80b75-184">Znovu připojit klienty</span><span class="sxs-lookup"><span data-stu-id="80b75-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="80b75-185">Automaticky znovu připojit</span><span class="sxs-lookup"><span data-stu-id="80b75-185">Automatically reconnect</span></span>

<span data-ttu-id="80b75-186">Klient jazyka JavaScript pro SignalR lze nakonfigurovat tak, aby se automaticky znovu připojil pomocí `withAutomaticReconnect` metody v [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="80b75-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="80b75-187">Ve výchozím nastavení se automaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="80b75-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="80b75-188">Bez parametrů `withAutomaticReconnect()` nakonfiguruje klienta, aby čekal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.</span><span class="sxs-lookup"><span data-stu-id="80b75-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="80b75-189">Před zahájením jakýchkoli pokusů o opětovné připojení `HubConnection` přejdete do `HubConnectionState.Reconnecting` stavu a dojde k jeho `onreconnecting` zpětnému volání namísto přechodu do `Disconnected` stavu a aktivaci jeho `onclose` zpětného volání, jako je třeba `HubConnection` automatické opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="80b75-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="80b75-190">Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="80b75-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="80b75-191">Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, převede se `HubConnection` zpátky do `Connected` stavu a spustí zpětná `onreconnected` volání.</span><span class="sxs-lookup"><span data-stu-id="80b75-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="80b75-192">To vám umožní informovat uživatele o tom, že bylo připojení znovu navázáno.</span><span class="sxs-lookup"><span data-stu-id="80b75-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="80b75-193">Vzhledem k tomu, že připojení na serveru zcela začíná, `connectionId` bude zpětnému volání k dispozici nový `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="80b75-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="80b75-194">`onreconnected`Parametr zpětného volání `connectionId` bude nedefinován, pokud `HubConnection` byl nakonfigurován tak, aby [přeskočil vyjednávání](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="80b75-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="80b75-195">`withAutomaticReconnect()` neprovede konfiguraci `HubConnection` pro opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:</span><span class="sxs-lookup"><span data-stu-id="80b75-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="80b75-196">Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde do `Disconnected` stavu a aktivuje jeho zpětné volání při jeho [ukončení](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="80b75-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="80b75-197">To vám umožní informovat uživatele o trvalé ztrátě připojení a doporučit aktualizaci stránky:</span><span class="sxs-lookup"><span data-stu-id="80b75-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="80b75-198">Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování pro opětovné připojení, `withAutomaticReconnect` přijme pole čísel představující zpoždění v milisekundách, které se má počkat, než se spustí pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="80b75-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="80b75-199">V předchozím příkladu se nakonfiguruje `HubConnection` tak, aby se při pokusu o opětovné připojení ihned po ztrátě připojení spouštěla znovu.</span><span class="sxs-lookup"><span data-stu-id="80b75-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="80b75-200">To platí také pro výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="80b75-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="80b75-201">Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="80b75-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="80b75-202">Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="80b75-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="80b75-203">Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o ukončení pokusu o opětovné připojení v dalších 30 sekundách, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="80b75-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="80b75-204">Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení, je třeba `withAutomaticReconnect` přijmout objekt implementující `IRetryPolicy` rozhraní, které má jedinou metodu s názvem `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="80b75-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="80b75-205">`nextRetryDelayInMilliseconds` přijímá jeden argument s typem `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="80b75-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="80b75-206">`RetryContext`Má tři vlastnosti: `previousRetryCount` , `elapsedMilliseconds` a `retryReason` , které jsou a v `number` `number` `Error` uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="80b75-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="80b75-207">Před prvním pokusem o opětovné připojení budou obě `previousRetryCount` i `elapsedMilliseconds` nulové a `retryReason` dojde k chybě, která způsobila ztrátu připojení.</span><span class="sxs-lookup"><span data-stu-id="80b75-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="80b75-208">Po každém neúspěšném pokusu o opakování se bude `previousRetryCount` aktualizovat o jednu, `elapsedMilliseconds` aby odrážela dobu strávenou opětovným opětovným připojením v milisekundách `retryReason` . výsledkem bude chyba, která způsobila selhání posledního pokusu o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="80b75-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="80b75-209">`nextRetryDelayInMilliseconds` musí vracet buď číslo představující počet milisekund, po které se má čekat před dalším pokusem o opětovné připojení, nebo `null` jestli se `HubConnection` má zastavit opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="80b75-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="80b75-210">Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="80b75-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="80b75-211">Ručně znovu připojit</span><span class="sxs-lookup"><span data-stu-id="80b75-211">Manually reconnect</span></span>

<span data-ttu-id="80b75-212">Následující kód ukazuje typický postup ručního opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="80b75-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="80b75-213">`start`Pro spuštění připojení je vytvořena funkce (v tomto případě funkce).</span><span class="sxs-lookup"><span data-stu-id="80b75-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="80b75-214">Zavolejte `start` funkci v `onclose` popisovači události připojení.</span><span class="sxs-lookup"><span data-stu-id="80b75-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="80b75-215">Implementace reálného světa využije exponenciální přerušení nebo opakuje zadaný počet opakování.</span><span class="sxs-lookup"><span data-stu-id="80b75-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="80b75-216">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="80b75-216">Additional resources</span></span>

* [<span data-ttu-id="80b75-217">Referenční dokumentace k rozhraní API v JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="80b75-217">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="80b75-218">Kurz JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="80b75-218">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="80b75-219">Kurz pro Webpack a TypeScript</span><span class="sxs-lookup"><span data-stu-id="80b75-219">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="80b75-220">Centra</span><span class="sxs-lookup"><span data-stu-id="80b75-220">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="80b75-221">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="80b75-221">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="80b75-222">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="80b75-222">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="80b75-223">Žádosti mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="80b75-223">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="80b75-224">SignalRDokumentace k serveru se službou Azure</span><span class="sxs-lookup"><span data-stu-id="80b75-224">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="80b75-225">Od [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="80b75-225">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="80b75-226">SignalRKlientská knihovna ASP.NET Core JavaScript umožňuje vývojářům volat kód centra na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="80b75-226">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="80b75-227">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="80b75-227">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="80b75-228">Instalace SignalR balíčku klienta</span><span class="sxs-lookup"><span data-stu-id="80b75-228">Install the SignalR client package</span></span>

<span data-ttu-id="80b75-229">SignalRKlientská knihovna JavaScriptu je dodávána jako balíček [npm](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="80b75-229">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="80b75-230">Následující oddíly popisují různé způsoby instalace klientské knihovny.</span><span class="sxs-lookup"><span data-stu-id="80b75-230">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="80b75-231">Instalace pomocí npm</span><span class="sxs-lookup"><span data-stu-id="80b75-231">Install with npm</span></span>

<span data-ttu-id="80b75-232">Pokud používáte Visual Studio, spusťte následující příkazy z **konzoly Správce balíčků** v kořenové složce.</span><span class="sxs-lookup"><span data-stu-id="80b75-232">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="80b75-233">Pro Visual Studio Code spusťte následující příkazy z **integrovaného terminálu**.</span><span class="sxs-lookup"><span data-stu-id="80b75-233">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="80b75-234">NPM nainstaluje obsah balíčku do složky \*node_modules \\ @aspnet\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="80b75-234">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="80b75-235">Vytvořte novou složku s názvem *Signal* ve složce *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="80b75-235">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="80b75-236">Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="80b75-236">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="80b75-237">Odkazování na SignalR klienta JavaScriptu v `<script>` elementu.</span><span class="sxs-lookup"><span data-stu-id="80b75-237">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="80b75-238">Příklad:</span><span class="sxs-lookup"><span data-stu-id="80b75-238">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="80b75-239">Použít Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="80b75-239">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="80b75-240">Chcete-li použít klientskou knihovnu bez npm požadavků, proveďte odkaz na kopii klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="80b75-240">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="80b75-241">Příklad:</span><span class="sxs-lookup"><span data-stu-id="80b75-241">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="80b75-242">Klientská knihovna je k dispozici na následujících sítě CDN:</span><span class="sxs-lookup"><span data-stu-id="80b75-242">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="80b75-243">cdnjs</span><span class="sxs-lookup"><span data-stu-id="80b75-243">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="80b75-244">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="80b75-244">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="80b75-245">unpkg</span><span class="sxs-lookup"><span data-stu-id="80b75-245">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="80b75-246">Instalace pomocí LibMan</span><span class="sxs-lookup"><span data-stu-id="80b75-246">Install with LibMan</span></span>

<span data-ttu-id="80b75-247">[LibMan](xref:client-side/libman/index) se dá použít k instalaci určitých souborů knihovny klienta z klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="80b75-247">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="80b75-248">Například do projektu přidejte pouze soubor JavaScriptu minifikovaného.</span><span class="sxs-lookup"><span data-stu-id="80b75-248">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="80b75-249">Podrobnosti o tomto přístupu najdete v tématu [Přidání SignalR klientské knihovny](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="80b75-249">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="80b75-250">Připojení k centru</span><span class="sxs-lookup"><span data-stu-id="80b75-250">Connect to a hub</span></span>

<span data-ttu-id="80b75-251">Následující kód vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="80b75-251">The following code creates and starts a connection.</span></span> <span data-ttu-id="80b75-252">V názvu centra se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="80b75-252">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="80b75-253">Připojení mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="80b75-253">Cross-origin connections</span></span>

<span data-ttu-id="80b75-254">Prohlížeče obvykle načítají připojení ze stejné domény jako požadovanou stránku.</span><span class="sxs-lookup"><span data-stu-id="80b75-254">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="80b75-255">Existují však situace, kdy je vyžadováno připojení k jiné doméně.</span><span class="sxs-lookup"><span data-stu-id="80b75-255">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="80b75-256">Chcete-li zabránit škodlivému webu v čtení citlivých dat z jiné lokality, [připojení mezi zdroji](xref:security/cors) jsou ve výchozím nastavení zakázána.</span><span class="sxs-lookup"><span data-stu-id="80b75-256">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="80b75-257">Pokud chcete povolit žádost o více zdrojů, povolte ji ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="80b75-257">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="80b75-258">Volání metod centra z klienta</span><span class="sxs-lookup"><span data-stu-id="80b75-258">Call hub methods from client</span></span>

<span data-ttu-id="80b75-259">Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="80b75-259">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="80b75-260">`invoke`Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="80b75-260">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="80b75-261">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="80b75-261">The name of the hub method.</span></span> <span data-ttu-id="80b75-262">V následujícím příkladu je název metody v centru `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="80b75-262">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="80b75-263">Jakékoli argumenty definované v metodě hub.</span><span class="sxs-lookup"><span data-stu-id="80b75-263">Any arguments defined in the hub method.</span></span> <span data-ttu-id="80b75-264">V následujícím příkladu je název argumentu `message` .</span><span class="sxs-lookup"><span data-stu-id="80b75-264">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="80b75-265">Vzorový kód používá syntaxi funkce šipky, která je podporována v aktuálních verzích všech hlavních prohlížečů s výjimkou aplikace Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="80b75-265">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="80b75-266">Volání metod rozbočovače z klienta je podporováno, pouze pokud používáte SignalR službu Azure ve *výchozím* režimu.</span><span class="sxs-lookup"><span data-stu-id="80b75-266">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="80b75-267">Další informace najdete v tématu [Nejčastější dotazy (úložiště GitHub Azure-Signal)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="80b75-267">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="80b75-268">`invoke`Metoda vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="80b75-268">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="80b75-269">`Promise`Je vyřešen s návratovou hodnotou (pokud existuje), když metoda na serveru vrátí.</span><span class="sxs-lookup"><span data-stu-id="80b75-269">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="80b75-270">Pokud metoda na serveru vyvolá chybu, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="80b75-270">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="80b75-271">Použijte `then` metody a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="80b75-271">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="80b75-272">`send`Metoda vrátí JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="80b75-272">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="80b75-273">`Promise`Je vyřešena při odeslání zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="80b75-273">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="80b75-274">Pokud při odesílání zprávy dojde k chybě, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="80b75-274">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="80b75-275">Použijte `then` metody a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="80b75-275">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="80b75-276">Použití nástroje `send` nečeká na přijetí zprávy serverem.</span><span class="sxs-lookup"><span data-stu-id="80b75-276">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="80b75-277">V důsledku toho není možné vracet data nebo chyby ze serveru.</span><span class="sxs-lookup"><span data-stu-id="80b75-277">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="80b75-278">Volání metod klienta z centra</span><span class="sxs-lookup"><span data-stu-id="80b75-278">Call client methods from hub</span></span>

<span data-ttu-id="80b75-279">Chcete-li přijímat zprávy z centra, definujte metodu pomocí metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="80b75-279">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="80b75-280">Název metody klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80b75-280">The name of the JavaScript client method.</span></span> <span data-ttu-id="80b75-281">V následujícím příkladu je název metody `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="80b75-281">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="80b75-282">Argumenty, které rozbočovač předává metodě.</span><span class="sxs-lookup"><span data-stu-id="80b75-282">Arguments the hub passes to the method.</span></span> <span data-ttu-id="80b75-283">V následujícím příkladu je hodnota argumentu `message` .</span><span class="sxs-lookup"><span data-stu-id="80b75-283">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="80b75-284">Předchozí kód v aplikaci se `connection.on` spustí, když kód na straně serveru volá <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> metodu.</span><span class="sxs-lookup"><span data-stu-id="80b75-284">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="80b75-285">SignalR Určuje, která metoda klienta má být volána, a to tak, že odpovídá názvu metody a argumentům definovaným v `SendAsync` a `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="80b75-285">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="80b75-286">Osvědčeným postupem je zavolat metodu [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) na `HubConnection` za `on` .</span><span class="sxs-lookup"><span data-stu-id="80b75-286">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="80b75-287">Tím zajistíte, aby byly obslužné rutiny registrovány před přijetím jakýchkoli zpráv.</span><span class="sxs-lookup"><span data-stu-id="80b75-287">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="80b75-288">Zpracování chyb a protokolování</span><span class="sxs-lookup"><span data-stu-id="80b75-288">Error handling and logging</span></span>

<span data-ttu-id="80b75-289">Řetězení `catch` metody ke konci `start` metody za účelem zpracování chyb na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="80b75-289">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="80b75-290">Slouží `console.error` k výstupu chyb do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="80b75-290">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="80b75-291">Nastavte trasování protokolu na straně klienta předáním protokolovacího nástroje a typu události, které se mají protokolovat při navázání spojení.</span><span class="sxs-lookup"><span data-stu-id="80b75-291">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="80b75-292">Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší.</span><span class="sxs-lookup"><span data-stu-id="80b75-292">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="80b75-293">K dispozici jsou následující úrovně protokolu:</span><span class="sxs-lookup"><span data-stu-id="80b75-293">Available log levels are as follows:</span></span>

* <span data-ttu-id="80b75-294">`signalR.LogLevel.Error`: Chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="80b75-294">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="80b75-295">Protokoluje `Error` pouze zprávy.</span><span class="sxs-lookup"><span data-stu-id="80b75-295">Logs `Error` messages only.</span></span>
* <span data-ttu-id="80b75-296">`signalR.LogLevel.Warning`: Varovné zprávy o potenciálních chybách.</span><span class="sxs-lookup"><span data-stu-id="80b75-296">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="80b75-297">Protokoly `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="80b75-297">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="80b75-298">`signalR.LogLevel.Information`: Stavové zprávy bez chyb.</span><span class="sxs-lookup"><span data-stu-id="80b75-298">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="80b75-299">Protokoly `Information` , `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="80b75-299">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="80b75-300">`signalR.LogLevel.Trace`: Trace Messages.</span><span class="sxs-lookup"><span data-stu-id="80b75-300">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="80b75-301">Zaznamená vše, včetně dat přepravovaných mezi centrem a klientem.</span><span class="sxs-lookup"><span data-stu-id="80b75-301">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="80b75-302">K nakonfigurování úrovně protokolu použijte metodu [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) pro [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="80b75-302">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="80b75-303">Zprávy jsou protokolovány do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="80b75-303">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="80b75-304">Znovu připojit klienty</span><span class="sxs-lookup"><span data-stu-id="80b75-304">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="80b75-305">Ručně znovu připojit</span><span class="sxs-lookup"><span data-stu-id="80b75-305">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="80b75-306">Před 3,0 se klient JavaScriptu pro SignalR neautomaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="80b75-306">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="80b75-307">Musíte napsat kód, který bude znovu připojit klienta ručně.</span><span class="sxs-lookup"><span data-stu-id="80b75-307">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="80b75-308">Následující kód ukazuje typický postup ručního opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="80b75-308">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="80b75-309">`start`Pro spuštění připojení je vytvořena funkce (v tomto případě funkce).</span><span class="sxs-lookup"><span data-stu-id="80b75-309">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="80b75-310">Zavolejte `start` funkci v `onclose` popisovači události připojení.</span><span class="sxs-lookup"><span data-stu-id="80b75-310">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="80b75-311">Implementace reálného světa využije exponenciální přerušení nebo opakuje zadaný počet opakování.</span><span class="sxs-lookup"><span data-stu-id="80b75-311">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="80b75-312">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="80b75-312">Additional resources</span></span>

* [<span data-ttu-id="80b75-313">Referenční dokumentace k rozhraní API v JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="80b75-313">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="80b75-314">Kurz JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="80b75-314">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="80b75-315">Kurz pro Webpack a TypeScript</span><span class="sxs-lookup"><span data-stu-id="80b75-315">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="80b75-316">Centra</span><span class="sxs-lookup"><span data-stu-id="80b75-316">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="80b75-317">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="80b75-317">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="80b75-318">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="80b75-318">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="80b75-319">Žádosti mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="80b75-319">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="80b75-320">SignalRDokumentace k serveru se službou Azure</span><span class="sxs-lookup"><span data-stu-id="80b75-320">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
