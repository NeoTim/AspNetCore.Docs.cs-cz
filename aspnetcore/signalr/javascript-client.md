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
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="82e94-103">SignalRKlient ASP.NET Core JavaScript</span><span class="sxs-lookup"><span data-stu-id="82e94-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="82e94-104">Od [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="82e94-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="82e94-105">SignalRKlientská knihovna ASP.NET Core JavaScript umožňuje vývojářům volat kód centra na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="82e94-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="82e94-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="82e94-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="82e94-107">Instalace SignalR balíčku klienta</span><span class="sxs-lookup"><span data-stu-id="82e94-107">Install the SignalR client package</span></span>

<span data-ttu-id="82e94-108">SignalRKlientská knihovna JavaScriptu je dodávána jako balíček [npm](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="82e94-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="82e94-109">Následující oddíly popisují různé způsoby instalace klientské knihovny.</span><span class="sxs-lookup"><span data-stu-id="82e94-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="82e94-110">Instalace pomocí npm</span><span class="sxs-lookup"><span data-stu-id="82e94-110">Install with npm</span></span>

<span data-ttu-id="82e94-111">V případě sady Visual Studio spusťte následující příkazy z **konzoly Správce balíčků** v kořenové složce.</span><span class="sxs-lookup"><span data-stu-id="82e94-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="82e94-112">Pro Visual Studio Code spusťte následující příkazy z **integrovaného terminálu**.</span><span class="sxs-lookup"><span data-stu-id="82e94-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="82e94-113">NPM nainstaluje obsah balíčku do složky \*node_modules \\ @microsoft\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="82e94-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="82e94-114">Vytvořte novou složku s názvem *Signal* ve složce *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="82e94-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="82e94-115">Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="82e94-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="82e94-116">Odkazování na SignalR klienta JavaScriptu v `<script>` elementu.</span><span class="sxs-lookup"><span data-stu-id="82e94-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="82e94-117">Například:</span><span class="sxs-lookup"><span data-stu-id="82e94-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="82e94-118">Použít Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="82e94-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="82e94-119">Chcete-li použít klientskou knihovnu bez npm požadavků, proveďte odkaz na kopii klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="82e94-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="82e94-120">Například:</span><span class="sxs-lookup"><span data-stu-id="82e94-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="82e94-121">Klientská knihovna je k dispozici na následujících sítě CDN:</span><span class="sxs-lookup"><span data-stu-id="82e94-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="82e94-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="82e94-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="82e94-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="82e94-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="82e94-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="82e94-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="82e94-125">Instalace pomocí LibMan</span><span class="sxs-lookup"><span data-stu-id="82e94-125">Install with LibMan</span></span>

<span data-ttu-id="82e94-126">[LibMan](xref:client-side/libman/index) se dá použít k instalaci určitých souborů knihovny klienta z klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="82e94-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="82e94-127">Například do projektu přidejte pouze soubor JavaScriptu minifikovaného.</span><span class="sxs-lookup"><span data-stu-id="82e94-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="82e94-128">Podrobnosti o tomto přístupu najdete v tématu [Přidání SignalR klientské knihovny](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="82e94-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="82e94-129">Připojení k centru</span><span class="sxs-lookup"><span data-stu-id="82e94-129">Connect to a hub</span></span>

<span data-ttu-id="82e94-130">Následující kód vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="82e94-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="82e94-131">V názvu centra se nerozlišují malá a velká písmena:</span><span class="sxs-lookup"><span data-stu-id="82e94-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="82e94-132">Připojení mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="82e94-132">Cross-origin connections</span></span>

<span data-ttu-id="82e94-133">Prohlížeče obvykle načítají připojení ze stejné domény jako požadovanou stránku.</span><span class="sxs-lookup"><span data-stu-id="82e94-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="82e94-134">Existují však situace, kdy je vyžadováno připojení k jiné doméně.</span><span class="sxs-lookup"><span data-stu-id="82e94-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="82e94-135">Chcete-li zabránit škodlivému webu v čtení citlivých dat z jiné lokality, [připojení mezi zdroji](xref:security/cors) jsou ve výchozím nastavení zakázána.</span><span class="sxs-lookup"><span data-stu-id="82e94-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="82e94-136">Pokud chcete povolit žádost o více zdrojů, povolte ji ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="82e94-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="82e94-137">Volání metod centra z klienta</span><span class="sxs-lookup"><span data-stu-id="82e94-137">Call hub methods from the client</span></span>

<span data-ttu-id="82e94-138">Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="82e94-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="82e94-139">`invoke`Metoda akceptuje:</span><span class="sxs-lookup"><span data-stu-id="82e94-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="82e94-140">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="82e94-140">The name of the hub method.</span></span>
* <span data-ttu-id="82e94-141">Jakékoli argumenty definované v metodě hub.</span><span class="sxs-lookup"><span data-stu-id="82e94-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="82e94-142">V následujícím příkladu je název metody v centru `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="82e94-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="82e94-143">Druhý a třetí argument předaný k `invoke` mapování na metody `user` a `message` argumenty centra:</span><span class="sxs-lookup"><span data-stu-id="82e94-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="82e94-144">Volání metod rozbočovače z klienta je podporováno, pouze pokud používáte SignalR službu Azure ve *výchozím* režimu.</span><span class="sxs-lookup"><span data-stu-id="82e94-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="82e94-145">Další informace najdete v tématu [Nejčastější dotazy (úložiště GitHub Azure-Signal)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="82e94-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="82e94-146">`invoke`Metoda vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="82e94-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="82e94-147">`Promise`Je vyřešen s návratovou hodnotou (pokud existuje), když metoda na serveru vrátí.</span><span class="sxs-lookup"><span data-stu-id="82e94-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="82e94-148">Pokud metoda na serveru vyvolá chybu, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="82e94-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="82e94-149">Použijte `async` `await` metody a nebo `Promise` `then` a `catch` ke zpracování těchto případů.</span><span class="sxs-lookup"><span data-stu-id="82e94-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="82e94-150">Klienti JavaScriptu můžou také volat veřejné metody na rozbočovače prostřednictvím metody [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) objektu `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="82e94-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="82e94-151">Na rozdíl od `invoke` metody `send` Metoda nečeká na odpověď ze serveru.</span><span class="sxs-lookup"><span data-stu-id="82e94-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="82e94-152">`send`Metoda vrátí JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="82e94-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="82e94-153">`Promise`Je vyřešena při odeslání zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="82e94-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="82e94-154">Pokud při odesílání zprávy dojde k chybě, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="82e94-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="82e94-155">Použijte `async` `await` metody a nebo `Promise` `then` a `catch` ke zpracování těchto případů.</span><span class="sxs-lookup"><span data-stu-id="82e94-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="82e94-156">Použití nástroje `send` nečeká na přijetí zprávy serverem.</span><span class="sxs-lookup"><span data-stu-id="82e94-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="82e94-157">V důsledku toho není možné vracet data nebo chyby ze serveru.</span><span class="sxs-lookup"><span data-stu-id="82e94-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="82e94-158">Volání metod klienta z centra</span><span class="sxs-lookup"><span data-stu-id="82e94-158">Call client methods from the hub</span></span>

<span data-ttu-id="82e94-159">Chcete-li přijímat zprávy z centra, definujte metodu pomocí metody [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="82e94-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="82e94-160">Název metody klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="82e94-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="82e94-161">Argumenty, které rozbočovač předává metodě.</span><span class="sxs-lookup"><span data-stu-id="82e94-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="82e94-162">V následujícím příkladu je název metody `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="82e94-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="82e94-163">Názvy argumentů jsou `user` a `message` :</span><span class="sxs-lookup"><span data-stu-id="82e94-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="82e94-164">Předchozí kód v aplikaci se `connection.on` spustí, když kód na straně serveru volá <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> metodu:</span><span class="sxs-lookup"><span data-stu-id="82e94-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="82e94-165">SignalR Určuje, která metoda klienta má být volána, a to tak, že odpovídá názvu metody a argumentům definovaným v `SendAsync` a `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="82e94-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="82e94-166">Osvědčeným postupem je zavolat metodu [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) na `HubConnection` za `on` .</span><span class="sxs-lookup"><span data-stu-id="82e94-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="82e94-167">Tím zajistíte, aby byly obslužné rutiny registrovány před přijetím jakýchkoli zpráv.</span><span class="sxs-lookup"><span data-stu-id="82e94-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="82e94-168">Zpracování chyb a protokolování</span><span class="sxs-lookup"><span data-stu-id="82e94-168">Error handling and logging</span></span>

<span data-ttu-id="82e94-169">`try` `catch` `async` `await` `Promise` `catch` Pro zpracování chyb na straně klienta použijte metody a a nebo.</span><span class="sxs-lookup"><span data-stu-id="82e94-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="82e94-170">Slouží `console.error` k výstupu chyb do konzoly prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="82e94-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="82e94-171">Nastavte trasování protokolu na straně klienta předáním protokolovacího nástroje a typu události, které se mají protokolovat při navázání spojení.</span><span class="sxs-lookup"><span data-stu-id="82e94-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="82e94-172">Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší.</span><span class="sxs-lookup"><span data-stu-id="82e94-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="82e94-173">K dispozici jsou následující úrovně protokolu:</span><span class="sxs-lookup"><span data-stu-id="82e94-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="82e94-174">`signalR.LogLevel.Error`: Chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="82e94-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="82e94-175">Protokoluje `Error` pouze zprávy.</span><span class="sxs-lookup"><span data-stu-id="82e94-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="82e94-176">`signalR.LogLevel.Warning`: Varovné zprávy o potenciálních chybách.</span><span class="sxs-lookup"><span data-stu-id="82e94-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="82e94-177">Protokoly `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="82e94-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="82e94-178">`signalR.LogLevel.Information`: Stavové zprávy bez chyb.</span><span class="sxs-lookup"><span data-stu-id="82e94-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="82e94-179">Protokoly `Information` , `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="82e94-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="82e94-180">`signalR.LogLevel.Trace`: Trace Messages.</span><span class="sxs-lookup"><span data-stu-id="82e94-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="82e94-181">Zaznamená vše, včetně dat přepravovaných mezi centrem a klientem.</span><span class="sxs-lookup"><span data-stu-id="82e94-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="82e94-182">K nakonfigurování úrovně protokolu použijte metodu [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) pro [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="82e94-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="82e94-183">Zprávy jsou protokolovány do konzoly prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="82e94-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="82e94-184">Znovu připojit klienty</span><span class="sxs-lookup"><span data-stu-id="82e94-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="82e94-185">Automaticky znovu připojit</span><span class="sxs-lookup"><span data-stu-id="82e94-185">Automatically reconnect</span></span>

<span data-ttu-id="82e94-186">Klient jazyka JavaScript pro SignalR lze nakonfigurovat tak, aby se automaticky znovu připojil pomocí `withAutomaticReconnect` metody v [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="82e94-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="82e94-187">Ve výchozím nastavení se automaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="82e94-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="82e94-188">Bez parametrů `withAutomaticReconnect()` nakonfiguruje klienta, aby čekal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.</span><span class="sxs-lookup"><span data-stu-id="82e94-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="82e94-189">Před zahájením jakýchkoli pokusů o opětovné připojení `HubConnection` přejdete do `HubConnectionState.Reconnecting` stavu a dojde k jeho `onreconnecting` zpětnému volání namísto přechodu do `Disconnected` stavu a aktivaci jeho `onclose` zpětného volání, jako je třeba `HubConnection` automatické opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="82e94-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="82e94-190">Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="82e94-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="82e94-191">Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, převede se `HubConnection` zpátky do `Connected` stavu a spustí zpětná `onreconnected` volání.</span><span class="sxs-lookup"><span data-stu-id="82e94-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="82e94-192">To vám umožní informovat uživatele o tom, že bylo připojení znovu navázáno.</span><span class="sxs-lookup"><span data-stu-id="82e94-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="82e94-193">Vzhledem k tomu, že připojení na serveru zcela začíná, `connectionId` bude zpětnému volání k dispozici nový `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="82e94-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="82e94-194">`onreconnected`Parametr zpětného volání `connectionId` bude nedefinován, pokud `HubConnection` byl nakonfigurován tak, aby [přeskočil vyjednávání](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="82e94-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="82e94-195">`withAutomaticReconnect()` neprovede konfiguraci `HubConnection` pro opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:</span><span class="sxs-lookup"><span data-stu-id="82e94-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="82e94-196">Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde do `Disconnected` stavu a aktivuje jeho zpětné volání při jeho [ukončení](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="82e94-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="82e94-197">To vám umožní informovat uživatele o trvalé ztrátě připojení a doporučit aktualizaci stránky:</span><span class="sxs-lookup"><span data-stu-id="82e94-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="82e94-198">Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování pro opětovné připojení, `withAutomaticReconnect` přijme pole čísel představující zpoždění v milisekundách, které se má počkat, než se spustí pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="82e94-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="82e94-199">V předchozím příkladu se nakonfiguruje `HubConnection` tak, aby se při pokusu o opětovné připojení ihned po ztrátě připojení spouštěla znovu.</span><span class="sxs-lookup"><span data-stu-id="82e94-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="82e94-200">To platí také pro výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="82e94-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="82e94-201">Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="82e94-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="82e94-202">Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="82e94-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="82e94-203">Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o ukončení pokusu o opětovné připojení v dalších 30 sekundách, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="82e94-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="82e94-204">Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení, je třeba `withAutomaticReconnect` přijmout objekt implementující `IRetryPolicy` rozhraní, které má jedinou metodu s názvem `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="82e94-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="82e94-205">`nextRetryDelayInMilliseconds` přijímá jeden argument s typem `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="82e94-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="82e94-206">`RetryContext`Má tři vlastnosti: `previousRetryCount` , `elapsedMilliseconds` a `retryReason` , které jsou a v `number` `number` `Error` uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="82e94-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="82e94-207">Před prvním pokusem o opětovné připojení budou obě `previousRetryCount` i `elapsedMilliseconds` nulové a `retryReason` dojde k chybě, která způsobila ztrátu připojení.</span><span class="sxs-lookup"><span data-stu-id="82e94-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="82e94-208">Po každém neúspěšném pokusu o opakování se bude `previousRetryCount` aktualizovat o jednu, `elapsedMilliseconds` aby odrážela dobu strávenou opětovným opětovným připojením v milisekundách `retryReason` . výsledkem bude chyba, která způsobila selhání posledního pokusu o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="82e94-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="82e94-209">`nextRetryDelayInMilliseconds` musí vracet buď číslo představující počet milisekund, po které se má čekat před dalším pokusem o opětovné připojení, nebo `null` jestli se `HubConnection` má zastavit opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="82e94-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="82e94-210">Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="82e94-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="82e94-211">Ručně znovu připojit</span><span class="sxs-lookup"><span data-stu-id="82e94-211">Manually reconnect</span></span>

<span data-ttu-id="82e94-212">Následující kód ukazuje typický postup ručního opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="82e94-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="82e94-213">`start`Pro spuštění připojení je vytvořena funkce (v tomto případě funkce).</span><span class="sxs-lookup"><span data-stu-id="82e94-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="82e94-214">Zavolejte `start` funkci v `onclose` popisovači události připojení.</span><span class="sxs-lookup"><span data-stu-id="82e94-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="82e94-215">Implementace reálného světa využije exponenciální přerušení nebo opakuje zadaný počet opakování.</span><span class="sxs-lookup"><span data-stu-id="82e94-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="troubleshoot-websocket-handshake-errors"></a><span data-ttu-id="82e94-216">Řešení chyb handshake s protokolem WebSocket</span><span class="sxs-lookup"><span data-stu-id="82e94-216">Troubleshoot WebSocket handshake errors</span></span>

<span data-ttu-id="82e94-217">Tato část poskytuje při pokusu o navázání připojení k rozbočovači ASP.NET Core k chybě při výjimce *metody handshake typu WebSocket handshake* SignalR .</span><span class="sxs-lookup"><span data-stu-id="82e94-217">This section provides help on the *"Error during WebSocket handshake"* exception that occurs when trying to establish connection to ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="82e94-218">Kód odpovědi 400 nebo 503</span><span class="sxs-lookup"><span data-stu-id="82e94-218">Response code 400 or 503</span></span>

<span data-ttu-id="82e94-219">Pro následující chybu:</span><span class="sxs-lookup"><span data-stu-id="82e94-219">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="82e94-220">K této chybě obvykle dochází pouze v případě, že klient používá přenos pomocí protokolu WebSockets, ale protokol WebSockets není na serveru povolen.</span><span class="sxs-lookup"><span data-stu-id="82e94-220">The error is usually caused by client only use WebSockets transport but WebSockets protocol is not enabled on server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="82e94-221">Kód odpovědi 307</span><span class="sxs-lookup"><span data-stu-id="82e94-221">Response code 307</span></span>

```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="82e94-222">K tomu často dochází, když SignalR hub Server:</span><span class="sxs-lookup"><span data-stu-id="82e94-222">Frequently this occurs when the SignalR hub server:</span></span>

* <span data-ttu-id="82e94-223">Naslouchá a reaguje na protokol HTTP i HTTPS.</span><span class="sxs-lookup"><span data-stu-id="82e94-223">Listens to and responds over both HTTP and HTTPS.</span></span>
* <span data-ttu-id="82e94-224">Je nakonfigurován tak, aby vynutil protokol HTTPS voláním `UseHttpsRedirection` v `Startup` nebo vynutila https prostřednictvím pravidla přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="82e94-224">Is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="82e94-225">Tato chyba se může vypříčinit zadáním adresy URL HTTP na straně klienta pomocí `.withUrl("http://xxx/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="82e94-225">This error can be caused by specifying the HTTP URL on client side using `.withUrl("http://xxx/HubName")`.</span></span> <span data-ttu-id="82e94-226">Oprava pro tento případ upravuje kód tak, aby používal adresu URL protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="82e94-226">The fix for this case is modifying the code to use an HTTPS URL.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="82e94-227">Kód odpovědi 404</span><span class="sxs-lookup"><span data-stu-id="82e94-227">Response code 404</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

<span data-ttu-id="82e94-228">Pokud aplikace funguje na místním hostiteli, ale po publikování na server služby IIS vrátí tuto chybu:</span><span class="sxs-lookup"><span data-stu-id="82e94-228">If the app works on localhost, but returns this error after publishing to IIS server:</span></span>

* <span data-ttu-id="82e94-229">Ověřte, že SignalR je aplikace ASP.NET Core hostovaná jako dílčí aplikace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="82e94-229">Verify the ASP.NET Core SignalR app is hosted as an IIS sub-application.</span></span>
* <span data-ttu-id="82e94-230">Nenastavujte adresu URL s pathbase dílčí aplikace na SignalR straně klienta JavaScript `.withUrl("/SubAppName/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="82e94-230">Don't set URL with the sub-app's pathbase on SignalR JavaScript client side `.withUrl("/SubAppName/HubName")`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82e94-231">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="82e94-231">Additional resources</span></span>

* [<span data-ttu-id="82e94-232">Referenční dokumentace k rozhraní API v JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="82e94-232">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="82e94-233">Kurz JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="82e94-233">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="82e94-234">Kurz pro Webpack a TypeScript</span><span class="sxs-lookup"><span data-stu-id="82e94-234">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="82e94-235">Centra</span><span class="sxs-lookup"><span data-stu-id="82e94-235">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="82e94-236">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="82e94-236">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="82e94-237">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="82e94-237">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="82e94-238">Žádosti mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="82e94-238">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="82e94-239">SignalRDokumentace k serveru se službou Azure</span><span class="sxs-lookup"><span data-stu-id="82e94-239">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="82e94-240">Od [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="82e94-240">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="82e94-241">SignalRKlientská knihovna ASP.NET Core JavaScript umožňuje vývojářům volat kód centra na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="82e94-241">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="82e94-242">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="82e94-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="82e94-243">Instalace SignalR balíčku klienta</span><span class="sxs-lookup"><span data-stu-id="82e94-243">Install the SignalR client package</span></span>

<span data-ttu-id="82e94-244">SignalRKlientská knihovna JavaScriptu je dodávána jako balíček [npm](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="82e94-244">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="82e94-245">Následující oddíly popisují různé způsoby instalace klientské knihovny.</span><span class="sxs-lookup"><span data-stu-id="82e94-245">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="82e94-246">Instalace pomocí npm</span><span class="sxs-lookup"><span data-stu-id="82e94-246">Install with npm</span></span>

<span data-ttu-id="82e94-247">Pokud používáte Visual Studio, spusťte následující příkazy z **konzoly Správce balíčků** v kořenové složce.</span><span class="sxs-lookup"><span data-stu-id="82e94-247">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="82e94-248">Pro Visual Studio Code spusťte následující příkazy z **integrovaného terminálu**.</span><span class="sxs-lookup"><span data-stu-id="82e94-248">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="82e94-249">NPM nainstaluje obsah balíčku do složky \*node_modules \\ @aspnet\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="82e94-249">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="82e94-250">Vytvořte novou složku s názvem *Signal* ve složce *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="82e94-250">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="82e94-251">Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="82e94-251">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="82e94-252">Odkazování na SignalR klienta JavaScriptu v `<script>` elementu.</span><span class="sxs-lookup"><span data-stu-id="82e94-252">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="82e94-253">Například:</span><span class="sxs-lookup"><span data-stu-id="82e94-253">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="82e94-254">Použít Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="82e94-254">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="82e94-255">Chcete-li použít klientskou knihovnu bez npm požadavků, proveďte odkaz na kopii klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="82e94-255">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="82e94-256">Například:</span><span class="sxs-lookup"><span data-stu-id="82e94-256">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="82e94-257">Klientská knihovna je k dispozici na následujících sítě CDN:</span><span class="sxs-lookup"><span data-stu-id="82e94-257">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="82e94-258">cdnjs</span><span class="sxs-lookup"><span data-stu-id="82e94-258">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="82e94-259">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="82e94-259">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="82e94-260">unpkg</span><span class="sxs-lookup"><span data-stu-id="82e94-260">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="82e94-261">Instalace pomocí LibMan</span><span class="sxs-lookup"><span data-stu-id="82e94-261">Install with LibMan</span></span>

<span data-ttu-id="82e94-262">[LibMan](xref:client-side/libman/index) se dá použít k instalaci určitých souborů knihovny klienta z klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="82e94-262">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="82e94-263">Například do projektu přidejte pouze soubor JavaScriptu minifikovaného.</span><span class="sxs-lookup"><span data-stu-id="82e94-263">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="82e94-264">Podrobnosti o tomto přístupu najdete v tématu [Přidání SignalR klientské knihovny](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="82e94-264">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="82e94-265">Připojení k centru</span><span class="sxs-lookup"><span data-stu-id="82e94-265">Connect to a hub</span></span>

<span data-ttu-id="82e94-266">Následující kód vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="82e94-266">The following code creates and starts a connection.</span></span> <span data-ttu-id="82e94-267">V názvu centra se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="82e94-267">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="82e94-268">Připojení mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="82e94-268">Cross-origin connections</span></span>

<span data-ttu-id="82e94-269">Prohlížeče obvykle načítají připojení ze stejné domény jako požadovanou stránku.</span><span class="sxs-lookup"><span data-stu-id="82e94-269">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="82e94-270">Existují však situace, kdy je vyžadováno připojení k jiné doméně.</span><span class="sxs-lookup"><span data-stu-id="82e94-270">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="82e94-271">Chcete-li zabránit škodlivému webu v čtení citlivých dat z jiné lokality, [připojení mezi zdroji](xref:security/cors) jsou ve výchozím nastavení zakázána.</span><span class="sxs-lookup"><span data-stu-id="82e94-271">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="82e94-272">Pokud chcete povolit žádost o více zdrojů, povolte ji ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="82e94-272">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="82e94-273">Volání metod centra z klienta</span><span class="sxs-lookup"><span data-stu-id="82e94-273">Call hub methods from client</span></span>

<span data-ttu-id="82e94-274">Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="82e94-274">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="82e94-275">`invoke`Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="82e94-275">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="82e94-276">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="82e94-276">The name of the hub method.</span></span> <span data-ttu-id="82e94-277">V následujícím příkladu je název metody v centru `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="82e94-277">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="82e94-278">Jakékoli argumenty definované v metodě hub.</span><span class="sxs-lookup"><span data-stu-id="82e94-278">Any arguments defined in the hub method.</span></span> <span data-ttu-id="82e94-279">V následujícím příkladu je název argumentu `message` .</span><span class="sxs-lookup"><span data-stu-id="82e94-279">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="82e94-280">Vzorový kód používá syntaxi funkce šipky, která je podporována v aktuálních verzích všech hlavních prohlížečů s výjimkou aplikace Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="82e94-280">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="82e94-281">Volání metod rozbočovače z klienta je podporováno, pouze pokud používáte SignalR službu Azure ve *výchozím* režimu.</span><span class="sxs-lookup"><span data-stu-id="82e94-281">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="82e94-282">Další informace najdete v tématu [Nejčastější dotazy (úložiště GitHub Azure-Signal)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="82e94-282">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="82e94-283">`invoke`Metoda vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="82e94-283">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="82e94-284">`Promise`Je vyřešen s návratovou hodnotou (pokud existuje), když metoda na serveru vrátí.</span><span class="sxs-lookup"><span data-stu-id="82e94-284">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="82e94-285">Pokud metoda na serveru vyvolá chybu, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="82e94-285">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="82e94-286">Použijte `then` metody a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="82e94-286">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="82e94-287">`send`Metoda vrátí JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="82e94-287">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="82e94-288">`Promise`Je vyřešena při odeslání zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="82e94-288">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="82e94-289">Pokud při odesílání zprávy dojde k chybě, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="82e94-289">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="82e94-290">Použijte `then` metody a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="82e94-290">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="82e94-291">Použití nástroje `send` nečeká na přijetí zprávy serverem.</span><span class="sxs-lookup"><span data-stu-id="82e94-291">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="82e94-292">V důsledku toho není možné vracet data nebo chyby ze serveru.</span><span class="sxs-lookup"><span data-stu-id="82e94-292">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="82e94-293">Volání metod klienta z centra</span><span class="sxs-lookup"><span data-stu-id="82e94-293">Call client methods from hub</span></span>

<span data-ttu-id="82e94-294">Chcete-li přijímat zprávy z centra, definujte metodu pomocí metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="82e94-294">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="82e94-295">Název metody klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="82e94-295">The name of the JavaScript client method.</span></span> <span data-ttu-id="82e94-296">V následujícím příkladu je název metody `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="82e94-296">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="82e94-297">Argumenty, které rozbočovač předává metodě.</span><span class="sxs-lookup"><span data-stu-id="82e94-297">Arguments the hub passes to the method.</span></span> <span data-ttu-id="82e94-298">V následujícím příkladu je hodnota argumentu `message` .</span><span class="sxs-lookup"><span data-stu-id="82e94-298">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="82e94-299">Předchozí kód v aplikaci se `connection.on` spustí, když kód na straně serveru volá <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> metodu.</span><span class="sxs-lookup"><span data-stu-id="82e94-299">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="82e94-300">SignalR Určuje, která metoda klienta má být volána, a to tak, že odpovídá názvu metody a argumentům definovaným v `SendAsync` a `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="82e94-300">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="82e94-301">Osvědčeným postupem je zavolat metodu [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) na `HubConnection` za `on` .</span><span class="sxs-lookup"><span data-stu-id="82e94-301">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="82e94-302">Tím zajistíte, aby byly obslužné rutiny registrovány před přijetím jakýchkoli zpráv.</span><span class="sxs-lookup"><span data-stu-id="82e94-302">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="82e94-303">Zpracování chyb a protokolování</span><span class="sxs-lookup"><span data-stu-id="82e94-303">Error handling and logging</span></span>

<span data-ttu-id="82e94-304">Řetězení `catch` metody ke konci `start` metody za účelem zpracování chyb na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="82e94-304">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="82e94-305">Slouží `console.error` k výstupu chyb do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="82e94-305">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="82e94-306">Nastavte trasování protokolu na straně klienta předáním protokolovacího nástroje a typu události, které se mají protokolovat při navázání spojení.</span><span class="sxs-lookup"><span data-stu-id="82e94-306">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="82e94-307">Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší.</span><span class="sxs-lookup"><span data-stu-id="82e94-307">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="82e94-308">K dispozici jsou následující úrovně protokolu:</span><span class="sxs-lookup"><span data-stu-id="82e94-308">Available log levels are as follows:</span></span>

* <span data-ttu-id="82e94-309">`signalR.LogLevel.Error`: Chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="82e94-309">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="82e94-310">Protokoluje `Error` pouze zprávy.</span><span class="sxs-lookup"><span data-stu-id="82e94-310">Logs `Error` messages only.</span></span>
* <span data-ttu-id="82e94-311">`signalR.LogLevel.Warning`: Varovné zprávy o potenciálních chybách.</span><span class="sxs-lookup"><span data-stu-id="82e94-311">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="82e94-312">Protokoly `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="82e94-312">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="82e94-313">`signalR.LogLevel.Information`: Stavové zprávy bez chyb.</span><span class="sxs-lookup"><span data-stu-id="82e94-313">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="82e94-314">Protokoly `Information` , `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="82e94-314">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="82e94-315">`signalR.LogLevel.Trace`: Trace Messages.</span><span class="sxs-lookup"><span data-stu-id="82e94-315">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="82e94-316">Zaznamená vše, včetně dat přepravovaných mezi centrem a klientem.</span><span class="sxs-lookup"><span data-stu-id="82e94-316">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="82e94-317">K nakonfigurování úrovně protokolu použijte metodu [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) pro [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="82e94-317">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="82e94-318">Zprávy jsou protokolovány do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="82e94-318">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="82e94-319">Znovu připojit klienty</span><span class="sxs-lookup"><span data-stu-id="82e94-319">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="82e94-320">Ručně znovu připojit</span><span class="sxs-lookup"><span data-stu-id="82e94-320">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="82e94-321">Před 3,0 se klient JavaScriptu pro SignalR neautomaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="82e94-321">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="82e94-322">Musíte napsat kód, který bude znovu připojit klienta ručně.</span><span class="sxs-lookup"><span data-stu-id="82e94-322">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="82e94-323">Následující kód ukazuje typický postup ručního opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="82e94-323">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="82e94-324">`start`Pro spuštění připojení je vytvořena funkce (v tomto případě funkce).</span><span class="sxs-lookup"><span data-stu-id="82e94-324">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="82e94-325">Zavolejte `start` funkci v `onclose` popisovači události připojení.</span><span class="sxs-lookup"><span data-stu-id="82e94-325">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="82e94-326">Implementace reálného světa využije exponenciální přerušení nebo opakuje zadaný počet opakování.</span><span class="sxs-lookup"><span data-stu-id="82e94-326">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82e94-327">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="82e94-327">Additional resources</span></span>

* [<span data-ttu-id="82e94-328">Referenční dokumentace k rozhraní API v JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="82e94-328">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="82e94-329">Kurz JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="82e94-329">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="82e94-330">Kurz pro Webpack a TypeScript</span><span class="sxs-lookup"><span data-stu-id="82e94-330">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="82e94-331">Centra</span><span class="sxs-lookup"><span data-stu-id="82e94-331">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="82e94-332">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="82e94-332">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="82e94-333">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="82e94-333">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="82e94-334">Žádosti mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="82e94-334">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="82e94-335">SignalRDokumentace k serveru se službou Azure</span><span class="sxs-lookup"><span data-stu-id="82e94-335">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
