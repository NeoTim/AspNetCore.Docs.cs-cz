---
title: SignalRKlient ASP.NET Core JavaScript
author: bradygaster
description: Přehled ASP.NET Core SignalR klienta jazyka JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
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
ms.openlocfilehash: e6feeb0009034d9ea92f09c44ed0ca882d80fe1b
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504759"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="52295-103">SignalRKlient ASP.NET Core JavaScript</span><span class="sxs-lookup"><span data-stu-id="52295-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="52295-104">Od [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="52295-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="52295-105">SignalRKlientská knihovna ASP.NET Core JavaScript umožňuje vývojářům volat kód centra na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="52295-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="52295-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="52295-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="52295-107">Instalace SignalR balíčku klienta</span><span class="sxs-lookup"><span data-stu-id="52295-107">Install the SignalR client package</span></span>

<span data-ttu-id="52295-108">SignalRKlientská knihovna JavaScriptu je dodávána jako balíček [npm](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="52295-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="52295-109">Následující oddíly popisují různé způsoby instalace klientské knihovny.</span><span class="sxs-lookup"><span data-stu-id="52295-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="52295-110">Instalace pomocí npm</span><span class="sxs-lookup"><span data-stu-id="52295-110">Install with npm</span></span>

<span data-ttu-id="52295-111">Pokud používáte Visual Studio, spusťte následující příkazy z **konzoly Správce balíčků** v kořenové složce.</span><span class="sxs-lookup"><span data-stu-id="52295-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="52295-112">Pro Visual Studio Code spusťte následující příkazy z **integrovaného terminálu**.</span><span class="sxs-lookup"><span data-stu-id="52295-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="52295-113">NPM nainstaluje obsah balíčku do složky \*node_modules \\ @microsoft\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="52295-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="52295-114">Vytvořte novou složku s názvem *Signal* ve složce *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="52295-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="52295-115">Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="52295-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="52295-116">NPM nainstaluje obsah balíčku do složky \*node_modules \\ @aspnet\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="52295-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="52295-117">Vytvořte novou složku s názvem *Signal* ve složce *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="52295-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="52295-118">Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="52295-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="52295-119">Odkazování na SignalR klienta JavaScriptu v `<script>` elementu.</span><span class="sxs-lookup"><span data-stu-id="52295-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="52295-120">Příklad:</span><span class="sxs-lookup"><span data-stu-id="52295-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="52295-121">Použít Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="52295-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="52295-122">Chcete-li použít klientskou knihovnu bez npm požadavků, proveďte odkaz na kopii klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="52295-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="52295-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="52295-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="52295-124">Klientská knihovna je k dispozici na následujících sítě CDN:</span><span class="sxs-lookup"><span data-stu-id="52295-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="52295-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="52295-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="52295-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="52295-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="52295-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="52295-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="52295-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="52295-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="52295-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="52295-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="52295-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="52295-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="52295-131">Instalace pomocí LibMan</span><span class="sxs-lookup"><span data-stu-id="52295-131">Install with LibMan</span></span>

<span data-ttu-id="52295-132">[LibMan](xref:client-side/libman/index) se dá použít k instalaci určitých souborů knihovny klienta z klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="52295-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="52295-133">Například do projektu přidejte pouze soubor JavaScriptu minifikovaného.</span><span class="sxs-lookup"><span data-stu-id="52295-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="52295-134">Podrobnosti o tomto přístupu najdete v tématu [Přidání SignalR klientské knihovny](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="52295-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="52295-135">Připojení k centru</span><span class="sxs-lookup"><span data-stu-id="52295-135">Connect to a hub</span></span>

<span data-ttu-id="52295-136">Následující kód vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="52295-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="52295-137">V názvu centra se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="52295-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="52295-138">Připojení mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="52295-138">Cross-origin connections</span></span>

<span data-ttu-id="52295-139">Prohlížeče obvykle načítají připojení ze stejné domény jako požadovanou stránku.</span><span class="sxs-lookup"><span data-stu-id="52295-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="52295-140">Existují však situace, kdy je vyžadováno připojení k jiné doméně.</span><span class="sxs-lookup"><span data-stu-id="52295-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="52295-141">Chcete-li zabránit škodlivému webu v čtení citlivých dat z jiné lokality, [připojení mezi zdroji](xref:security/cors) jsou ve výchozím nastavení zakázána.</span><span class="sxs-lookup"><span data-stu-id="52295-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="52295-142">Pokud chcete povolit žádost o více zdrojů, povolte ji ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="52295-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="52295-143">Volání metod centra z klienta</span><span class="sxs-lookup"><span data-stu-id="52295-143">Call hub methods from client</span></span>

<span data-ttu-id="52295-144">Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="52295-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="52295-145">`invoke`Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="52295-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="52295-146">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="52295-146">The name of the hub method.</span></span> <span data-ttu-id="52295-147">V následujícím příkladu je název metody v centru `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="52295-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="52295-148">Jakékoli argumenty definované v metodě hub.</span><span class="sxs-lookup"><span data-stu-id="52295-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="52295-149">V následujícím příkladu je název argumentu `message` .</span><span class="sxs-lookup"><span data-stu-id="52295-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="52295-150">Vzorový kód používá syntaxi funkce šipky, která je podporována v aktuálních verzích všech hlavních prohlížečů s výjimkou aplikace Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="52295-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="52295-151">Volání metod rozbočovače z klienta je podporováno, pouze pokud používáte SignalR službu Azure ve *výchozím* režimu.</span><span class="sxs-lookup"><span data-stu-id="52295-151">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="52295-152">Další informace najdete v tématu [Nejčastější dotazy (úložiště GitHub Azure-Signal)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="52295-152">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="52295-153">`invoke`Metoda vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="52295-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="52295-154">`Promise`Je vyřešen s návratovou hodnotou (pokud existuje), když metoda na serveru vrátí.</span><span class="sxs-lookup"><span data-stu-id="52295-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="52295-155">Pokud metoda na serveru vyvolá chybu, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="52295-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="52295-156">Použijte `then` metody a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="52295-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="52295-157">`send`Metoda vrátí JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="52295-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="52295-158">`Promise`Je vyřešena při odeslání zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="52295-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="52295-159">Pokud při odesílání zprávy dojde k chybě, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="52295-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="52295-160">Použijte `then` metody a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="52295-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="52295-161">Použití nástroje `send` nečeká na přijetí zprávy serverem.</span><span class="sxs-lookup"><span data-stu-id="52295-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="52295-162">V důsledku toho není možné vracet data nebo chyby ze serveru.</span><span class="sxs-lookup"><span data-stu-id="52295-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="52295-163">Volání metod klienta z centra</span><span class="sxs-lookup"><span data-stu-id="52295-163">Call client methods from hub</span></span>

<span data-ttu-id="52295-164">Chcete-li přijímat zprávy z centra, definujte metodu pomocí metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="52295-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="52295-165">Název metody klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="52295-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="52295-166">V následujícím příkladu je název metody `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="52295-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="52295-167">Argumenty, které rozbočovač předává metodě.</span><span class="sxs-lookup"><span data-stu-id="52295-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="52295-168">V následujícím příkladu je hodnota argumentu `message` .</span><span class="sxs-lookup"><span data-stu-id="52295-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="52295-169">Předchozí kód v aplikaci se `connection.on` spustí, když kód na straně serveru ho volá pomocí metody [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .</span><span class="sxs-lookup"><span data-stu-id="52295-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="52295-170">SignalR Určuje, která metoda klienta má být volána, a to tak, že odpovídá názvu metody a argumentům definovaným v `SendAsync` a `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="52295-170">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="52295-171">Osvědčeným postupem je zavolat metodu [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) na `HubConnection` za `on` .</span><span class="sxs-lookup"><span data-stu-id="52295-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="52295-172">Tím zajistíte, aby byly obslužné rutiny registrovány před přijetím jakýchkoli zpráv.</span><span class="sxs-lookup"><span data-stu-id="52295-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="52295-173">Zpracování chyb a protokolování</span><span class="sxs-lookup"><span data-stu-id="52295-173">Error handling and logging</span></span>

<span data-ttu-id="52295-174">Řetězení `catch` metody ke konci `start` metody za účelem zpracování chyb na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="52295-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="52295-175">Slouží `console.error` k výstupu chyb do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="52295-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="52295-176">Nastavte trasování protokolu na straně klienta předáním protokolovacího nástroje a typu události, které se mají protokolovat při navázání spojení.</span><span class="sxs-lookup"><span data-stu-id="52295-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="52295-177">Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší.</span><span class="sxs-lookup"><span data-stu-id="52295-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="52295-178">K dispozici jsou následující úrovně protokolu:</span><span class="sxs-lookup"><span data-stu-id="52295-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="52295-179">`signalR.LogLevel.Error`: Chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="52295-179">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="52295-180">Protokoluje `Error` pouze zprávy.</span><span class="sxs-lookup"><span data-stu-id="52295-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="52295-181">`signalR.LogLevel.Warning`: Varovné zprávy o potenciálních chybách.</span><span class="sxs-lookup"><span data-stu-id="52295-181">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="52295-182">Protokoly `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="52295-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="52295-183">`signalR.LogLevel.Information`: Stavové zprávy bez chyb.</span><span class="sxs-lookup"><span data-stu-id="52295-183">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="52295-184">Protokoly `Information` , `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="52295-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="52295-185">`signalR.LogLevel.Trace`: Trace Messages.</span><span class="sxs-lookup"><span data-stu-id="52295-185">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="52295-186">Zaznamená vše, včetně dat přepravovaných mezi centrem a klientem.</span><span class="sxs-lookup"><span data-stu-id="52295-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="52295-187">K nakonfigurování úrovně protokolu použijte metodu [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) pro [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="52295-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="52295-188">Zprávy jsou protokolovány do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="52295-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="52295-189">Znovu připojit klienty</span><span class="sxs-lookup"><span data-stu-id="52295-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="52295-190">Automaticky znovu připojit</span><span class="sxs-lookup"><span data-stu-id="52295-190">Automatically reconnect</span></span>

<span data-ttu-id="52295-191">Klient jazyka JavaScript pro SignalR lze nakonfigurovat tak, aby se automaticky znovu připojil pomocí `withAutomaticReconnect` metody v [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="52295-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="52295-192">Ve výchozím nastavení se automaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="52295-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="52295-193">Bez parametrů `withAutomaticReconnect()` nakonfiguruje klienta, aby čekal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.</span><span class="sxs-lookup"><span data-stu-id="52295-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="52295-194">Před zahájením jakýchkoli pokusů o opětovné připojení `HubConnection` přejdete do `HubConnectionState.Reconnecting` stavu a dojde k jeho `onreconnecting` zpětnému volání namísto přechodu do `Disconnected` stavu a aktivaci jeho `onclose` zpětného volání, jako je třeba `HubConnection` automatické opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="52295-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="52295-195">Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="52295-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="52295-196">Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, převede se `HubConnection` zpátky do `Connected` stavu a spustí zpětná `onreconnected` volání.</span><span class="sxs-lookup"><span data-stu-id="52295-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="52295-197">To vám umožní informovat uživatele o tom, že bylo připojení znovu navázáno.</span><span class="sxs-lookup"><span data-stu-id="52295-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="52295-198">Vzhledem k tomu, že připojení na serveru zcela začíná, `connectionId` bude zpětnému volání k dispozici nový `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="52295-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="52295-199">`onreconnected`Parametr zpětného volání `connectionId` bude nedefinován, pokud `HubConnection` byl nakonfigurován tak, aby [přeskočil vyjednávání](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="52295-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="52295-200">`withAutomaticReconnect()` neprovede konfiguraci `HubConnection` pro opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:</span><span class="sxs-lookup"><span data-stu-id="52295-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="52295-201">Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde do `Disconnected` stavu a aktivuje jeho zpětné volání při jeho [ukončení](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="52295-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="52295-202">To vám umožní informovat uživatele o trvalé ztrátě připojení a doporučit aktualizaci stránky:</span><span class="sxs-lookup"><span data-stu-id="52295-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="52295-203">Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování pro opětovné připojení, `withAutomaticReconnect` přijme pole čísel představující zpoždění v milisekundách, které se má počkat, než se spustí pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="52295-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="52295-204">V předchozím příkladu se nakonfiguruje `HubConnection` tak, aby se při pokusu o opětovné připojení ihned po ztrátě připojení spouštěla znovu.</span><span class="sxs-lookup"><span data-stu-id="52295-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="52295-205">To platí také pro výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="52295-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="52295-206">Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="52295-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="52295-207">Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="52295-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="52295-208">Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o ukončení pokusu o opětovné připojení v dalších 30 sekundách, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="52295-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="52295-209">Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení, je třeba `withAutomaticReconnect` přijmout objekt implementující `IRetryPolicy` rozhraní, které má jedinou metodu s názvem `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="52295-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="52295-210">`nextRetryDelayInMilliseconds` přijímá jeden argument s typem `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="52295-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="52295-211">`RetryContext`Má tři vlastnosti: `previousRetryCount` , `elapsedMilliseconds` a `retryReason` , které jsou a v `number` `number` `Error` uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="52295-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="52295-212">Před prvním pokusem o opětovné připojení budou obě `previousRetryCount` i `elapsedMilliseconds` nulové a `retryReason` dojde k chybě, která způsobila ztrátu připojení.</span><span class="sxs-lookup"><span data-stu-id="52295-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="52295-213">Po každém neúspěšném pokusu o opakování se bude `previousRetryCount` aktualizovat o jednu, `elapsedMilliseconds` aby odrážela dobu strávenou opětovným opětovným připojením v milisekundách `retryReason` . výsledkem bude chyba, která způsobila selhání posledního pokusu o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="52295-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="52295-214">`nextRetryDelayInMilliseconds` musí vracet buď číslo představující počet milisekund, po které se má čekat před dalším pokusem o opětovné připojení, nebo `null` jestli se `HubConnection` má zastavit opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="52295-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="52295-215">Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="52295-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="52295-216">Ručně znovu připojit</span><span class="sxs-lookup"><span data-stu-id="52295-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="52295-217">Před 3,0 se klient JavaScriptu pro SignalR neautomaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="52295-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="52295-218">Musíte napsat kód, který bude znovu připojit klienta ručně.</span><span class="sxs-lookup"><span data-stu-id="52295-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="52295-219">Následující kód ukazuje typický postup ručního opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="52295-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="52295-220">`start`Pro spuštění připojení je vytvořena funkce (v tomto případě funkce).</span><span class="sxs-lookup"><span data-stu-id="52295-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="52295-221">Zavolejte `start` funkci v `onclose` popisovači události připojení.</span><span class="sxs-lookup"><span data-stu-id="52295-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="52295-222">Implementace reálného světa využije exponenciální přerušení nebo opakuje zadaný počet opakování.</span><span class="sxs-lookup"><span data-stu-id="52295-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52295-223">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="52295-223">Additional resources</span></span>

* [<span data-ttu-id="52295-224">Referenční dokumentace k rozhraní API v JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="52295-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="52295-225">Kurz JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="52295-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="52295-226">Kurz pro Webpack a TypeScript</span><span class="sxs-lookup"><span data-stu-id="52295-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="52295-227">Centra</span><span class="sxs-lookup"><span data-stu-id="52295-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="52295-228">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="52295-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="52295-229">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="52295-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="52295-230">Žádosti mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="52295-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="52295-231">SignalRDokumentace k serveru se službou Azure</span><span class="sxs-lookup"><span data-stu-id="52295-231">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
