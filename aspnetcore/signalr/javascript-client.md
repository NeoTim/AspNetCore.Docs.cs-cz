---
title: ASP.NET SignalR klient javascriptu Core
author: bradygaster
description: Přehled ASP.NET SignalR javascriptového klienta.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 43b2cacf9f415ec422a00b28246f30c8ad74de29
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440854"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="47182-103">ASP.NET SignalR klient javascriptu Core</span><span class="sxs-lookup"><span data-stu-id="47182-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="47182-104">Podle [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="47182-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="47182-105">Klientská SignalR knihovna ASP.NET JavaScript umožňuje vývojářům volat kód rozbočovače na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="47182-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="47182-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="47182-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="47182-107">Instalace SignalR klientského balíčku</span><span class="sxs-lookup"><span data-stu-id="47182-107">Install the SignalR client package</span></span>

<span data-ttu-id="47182-108">Klientská knihovna SignalR JavaScriptu je dodávána jako balíček [npm.](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="47182-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="47182-109">V následujících částech jsou popsány různé způsoby instalace klientské knihovny.</span><span class="sxs-lookup"><span data-stu-id="47182-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="47182-110">Instalace s npm</span><span class="sxs-lookup"><span data-stu-id="47182-110">Install with npm</span></span>

<span data-ttu-id="47182-111">Pokud používáte Visual Studio, spusťte následující příkazy z **konzoly Správce balíčků** v kořenové složce.</span><span class="sxs-lookup"><span data-stu-id="47182-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="47182-112">Kód sady Visual Studio spusťte následující příkazy z **integrovaného terminálu**.</span><span class="sxs-lookup"><span data-stu-id="47182-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="47182-113">npm nainstaluje obsah balíčku do \*node_modules\\ \* složky.</span><span class="sxs-lookup"><span data-stu-id="47182-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="47182-114">Vytvořte novou složku s názvem *signalr* pod stolitou *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="47182-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="47182-115">Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr.*</span><span class="sxs-lookup"><span data-stu-id="47182-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="47182-116">npm nainstaluje obsah balíčku do \*node_modules\\ \* složky.</span><span class="sxs-lookup"><span data-stu-id="47182-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="47182-117">Vytvořte novou složku s názvem *signalr* pod stolitou *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="47182-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="47182-118">Zkopírujte soubor *signalr.js* do složky *wwwroot\lib\signalr.*</span><span class="sxs-lookup"><span data-stu-id="47182-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="47182-119">Odkazna SignalR klienta JavaScript u `<script>` prvku.</span><span class="sxs-lookup"><span data-stu-id="47182-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="47182-120">Příklad:</span><span class="sxs-lookup"><span data-stu-id="47182-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="47182-121">Použití sítě pro doručování obsahu (CDN)</span><span class="sxs-lookup"><span data-stu-id="47182-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="47182-122">Chcete-li použít klientskou knihovnu bez předpokladů npm, odkazujte na kopii klientské knihovny hostovodou cdn.</span><span class="sxs-lookup"><span data-stu-id="47182-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="47182-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="47182-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="47182-124">Klientská knihovna je k dispozici na následujících nedostupných seznamech CDN:</span><span class="sxs-lookup"><span data-stu-id="47182-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="47182-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="47182-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="47182-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="47182-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="47182-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="47182-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="47182-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="47182-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="47182-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="47182-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="47182-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="47182-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="47182-131">Instalace s LibManem</span><span class="sxs-lookup"><span data-stu-id="47182-131">Install with LibMan</span></span>

<span data-ttu-id="47182-132">[LibMan](xref:client-side/libman/index) lze použít k instalaci konkrétních souborů klientské knihovny z klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="47182-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="47182-133">Například přidejte do projektu pouze minifikovaný soubor JavaScript.</span><span class="sxs-lookup"><span data-stu-id="47182-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="47182-134">Podrobnosti o tomto [přístupu SignalR naleznete v tématu Přidání klientské knihovny](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="47182-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="47182-135">Připojení k rozbočovači</span><span class="sxs-lookup"><span data-stu-id="47182-135">Connect to a hub</span></span>

<span data-ttu-id="47182-136">Následující kód vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="47182-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="47182-137">Název rozbočovače je malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="47182-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="47182-138">Spojení s křížovým původem</span><span class="sxs-lookup"><span data-stu-id="47182-138">Cross-origin connections</span></span>

<span data-ttu-id="47182-139">Prohlížeče obvykle načítají připojení ze stejné domény jako požadovaná stránka.</span><span class="sxs-lookup"><span data-stu-id="47182-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="47182-140">Existují však případy, kdy je vyžadováno připojení k jiné doméně.</span><span class="sxs-lookup"><span data-stu-id="47182-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="47182-141">Chcete-li zabránit škodlivému webu ve čtení citlivých dat z jiné lokality, jsou [připojení mezi počátky](xref:security/cors) ve výchozím nastavení zakázána.</span><span class="sxs-lookup"><span data-stu-id="47182-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="47182-142">Chcete-li povolit požadavek na `Startup` příčný původ, povolte jej ve třídě.</span><span class="sxs-lookup"><span data-stu-id="47182-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="47182-143">Metody centra volání z klienta</span><span class="sxs-lookup"><span data-stu-id="47182-143">Call hub methods from client</span></span>

<span data-ttu-id="47182-144">Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [hubconnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="47182-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="47182-145">Metoda `invoke` přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="47182-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="47182-146">Název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="47182-146">The name of the hub method.</span></span> <span data-ttu-id="47182-147">V následujícím příkladu je `SendMessage`název metody v rozbočovači .</span><span class="sxs-lookup"><span data-stu-id="47182-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="47182-148">Všechny argumenty definované v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="47182-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="47182-149">V následujícím příkladu je `message`název argumentu .</span><span class="sxs-lookup"><span data-stu-id="47182-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="47182-150">Ukázkový kód používá syntaxi funkce šipky, která je podporována v aktuálních verzích všech hlavních prohlížečů s výjimkou aplikace Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="47182-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="47182-151">Pokud používáte službu Azure SignalR service v *režimu bez serveru*, nelze volat metody rozbočovače z klienta.</span><span class="sxs-lookup"><span data-stu-id="47182-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="47182-152">Další informace naleznete v [ SignalR dokumentaci ke službě](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="47182-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="47182-153">Metoda `invoke` vrátí JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span><span class="sxs-lookup"><span data-stu-id="47182-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="47182-154">Je `Promise` vyřešen s vrácenou hodnotou (pokud existuje) při metoda na serveru vrátí.</span><span class="sxs-lookup"><span data-stu-id="47182-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="47182-155">Pokud metoda na serveru vyvolá chybu, `Promise` je odmítnut s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="47182-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="47182-156">Použijte `then` metody `catch` a `Promise` na sobě ke zpracování `await` těchto případů (nebo syntaxe).</span><span class="sxs-lookup"><span data-stu-id="47182-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="47182-157">Metoda `send` vrátí JavaScript `Promise`.</span><span class="sxs-lookup"><span data-stu-id="47182-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="47182-158">Je `Promise` vyřešen, když byla zpráva odeslána na server.</span><span class="sxs-lookup"><span data-stu-id="47182-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="47182-159">Pokud dojde k chybě při `Promise` odesílání zprávy, je odmítnut a chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="47182-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="47182-160">Použijte `then` metody `catch` a `Promise` na sobě ke zpracování `await` těchto případů (nebo syntaxe).</span><span class="sxs-lookup"><span data-stu-id="47182-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="47182-161">Použití `send` nečeká, dokud server zprávu neobdrží.</span><span class="sxs-lookup"><span data-stu-id="47182-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="47182-162">V důsledku toho není možné vrátit data nebo chyby ze serveru.</span><span class="sxs-lookup"><span data-stu-id="47182-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="47182-163">Volání klientských metod z centra</span><span class="sxs-lookup"><span data-stu-id="47182-163">Call client methods from hub</span></span>

<span data-ttu-id="47182-164">Chcete-li přijímat zprávy z centra, definujte `HubConnection`metodu pomocí metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) .</span><span class="sxs-lookup"><span data-stu-id="47182-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="47182-165">Název metody klienta JavaScript.</span><span class="sxs-lookup"><span data-stu-id="47182-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="47182-166">V následujícím příkladu je `ReceiveMessage`název metody .</span><span class="sxs-lookup"><span data-stu-id="47182-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="47182-167">Argumenty rozbočovač předá metodu.</span><span class="sxs-lookup"><span data-stu-id="47182-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="47182-168">V následujícím příkladu je `message`hodnota argumentu .</span><span class="sxs-lookup"><span data-stu-id="47182-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="47182-169">Předchozí kód v `connection.on` systému se spustí, když kód na straně serveru volá pomocí metody [SendAsync.](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)</span><span class="sxs-lookup"><span data-stu-id="47182-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="47182-170">určuje, kterou metodu klienta má volat porovnáním `SendAsync` `connection.on`názvu metody a argumentů definovaných v písmenech a) a.</span><span class="sxs-lookup"><span data-stu-id="47182-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="47182-171">Jako osvědčený postup volejte [start](/javascript/api/%40aspnet/signalr/hubconnection#start) metodu `HubConnection` start `on`na after .</span><span class="sxs-lookup"><span data-stu-id="47182-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="47182-172">Tím zajistíte, že vaše obslužné rutiny jsou registrovány před přijetím všech zpráv.</span><span class="sxs-lookup"><span data-stu-id="47182-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="47182-173">Zpracování chyb a protokolování</span><span class="sxs-lookup"><span data-stu-id="47182-173">Error handling and logging</span></span>

<span data-ttu-id="47182-174">Zřetězení `catch` metody na `start` konec metody pro zpracování chyb na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="47182-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="47182-175">Slouží `console.error` k výstupu chyb do konzole prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="47182-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="47182-176">Nastavte trasování protokolu na straně klienta předáním protokolovacího nástroje a typu události, která se přihlásí při navádění připojení.</span><span class="sxs-lookup"><span data-stu-id="47182-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="47182-177">Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší.</span><span class="sxs-lookup"><span data-stu-id="47182-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="47182-178">Dostupné úrovně protokolu jsou následující:</span><span class="sxs-lookup"><span data-stu-id="47182-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="47182-179">`signalR.LogLevel.Error`&ndash; Chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="47182-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="47182-180">Zaznamenává `Error` pouze zprávy.</span><span class="sxs-lookup"><span data-stu-id="47182-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="47182-181">`signalR.LogLevel.Warning`&ndash; Varovné zprávy o možných chybách.</span><span class="sxs-lookup"><span data-stu-id="47182-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="47182-182">Protokoly `Warning`a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="47182-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="47182-183">`signalR.LogLevel.Information`&ndash; Stavové zprávy bez chyb.</span><span class="sxs-lookup"><span data-stu-id="47182-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="47182-184">Protokoly `Information` `Warning`a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="47182-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="47182-185">`signalR.LogLevel.Trace`&ndash; Vysledovat zprávy.</span><span class="sxs-lookup"><span data-stu-id="47182-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="47182-186">Protokoluje vše, včetně dat přenášených mezi rozbočovačem a klientem.</span><span class="sxs-lookup"><span data-stu-id="47182-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="47182-187">Ke [konfiguraci](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) úrovně protokolu použijte metodu configureLogging v [HubConnectionBuilder.](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)</span><span class="sxs-lookup"><span data-stu-id="47182-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="47182-188">Zprávy jsou zaznamenány do konzole prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="47182-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="47182-189">Opětovné připojení klientů</span><span class="sxs-lookup"><span data-stu-id="47182-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="47182-190">Automatické opětovné připojení</span><span class="sxs-lookup"><span data-stu-id="47182-190">Automatically reconnect</span></span>

<span data-ttu-id="47182-191">Klient javascriptu SignalR lze nakonfigurovat tak, `withAutomaticReconnect` aby se automaticky znovu připojoval pomocí metody na [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="47182-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="47182-192">Ve výchozím nastavení se automaticky nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="47182-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="47182-193">Bez jakýchkoli parametrů `withAutomaticReconnect()` nakonfiguruje klienta tak, aby před pokusem o každý pokus o opětovné připojení čekal 0, 2, 10 a 30 sekund, a to po čtyřech neúspěšných pokusech.</span><span class="sxs-lookup"><span data-stu-id="47182-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="47182-194">Před zahájením jakékoli pokusy `HubConnection` o opětovné `HubConnectionState.Reconnecting` připojení bude `onreconnecting` přechod do stavu a požární `Disconnected` jeho zpětná `onclose` volání namísto `HubConnection` přechodu do stavu a aktivaci jeho zpětná volání jako bez automatického opětovného připojení nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="47182-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="47182-195">To poskytuje příležitost upozornit uživatele, že připojení bylo ztraceno a zakázat prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="47182-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="47182-196">Pokud klient úspěšně znovu připojí v rámci své `HubConnection` první čtyři pokusy, bude přechod zpět do `Connected` stavu a požární jeho `onreconnected` zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="47182-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="47182-197">To poskytuje příležitost informovat uživatele, že připojení bylo obnoveno.</span><span class="sxs-lookup"><span data-stu-id="47182-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="47182-198">Vzhledem k tomu, že připojení vypadá `connectionId` zcela nové na `onreconnected` server, bude k dispozici nový zpětné volání.</span><span class="sxs-lookup"><span data-stu-id="47182-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="47182-199">Parametr `onreconnected` zpětného `connectionId` volání nebude definován, `HubConnection` pokud byl nakonfigurován tak, aby [přeskočoval vyjednávání](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="47182-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="47182-200">`withAutomaticReconnect()`nenakonfiguruje opakování selhání počátečního `HubConnection` spuštění, takže selhání spuštění je nutné zpracovat ručně:</span><span class="sxs-lookup"><span data-stu-id="47182-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="47182-201">Pokud klient není úspěšně znovu připojit v rámci své `HubConnection` první čtyři `Disconnected` pokusy, bude přechod do stavu a požární jeho připojení [k uzavření](/javascript/api/%40aspnet/signalr/hubconnection#onclose) zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="47182-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="47182-202">To poskytuje příležitost informovat uživatele, že připojení bylo trvale ztraceno, a doporučujeme aktualizovat stránku:</span><span class="sxs-lookup"><span data-stu-id="47182-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="47182-203">Chcete-li nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování opětovného připojení, `withAutomaticReconnect` přijme pole čísel představující zpoždění v milisekundách, které čeká před spuštěním každého pokusu o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="47182-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="47182-204">Předchozí příklad nakonfiguruje `HubConnection` zahájení pokusu o opětovné připojení ihned po ztrátě připojení.</span><span class="sxs-lookup"><span data-stu-id="47182-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="47182-205">To platí také pro výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="47182-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="47182-206">Pokud se první pokus o opětovné připojení nezdaří, druhý pokus o opětovné připojení se také spustí okamžitě namísto čekání 2 sekundy, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="47182-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="47182-207">Pokud se druhý pokus o opětovné připojení nezdaří, třetí pokus o opětovné připojení se spustí za 10 sekund, což je opět jako výchozí konfigurace.</span><span class="sxs-lookup"><span data-stu-id="47182-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="47182-208">Vlastní chování se pak znovu odchyluje od výchozího chování zastavením po selhání třetího pokusu o opětovné připojení namísto pokusu o další pokus o opětovné připojení v jiné 30 sekundě, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="47182-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="47182-209">Pokud chcete ještě větší kontrolu nad časování a počet `withAutomaticReconnect` pokusů o automatické `IRetryPolicy` opětovné připojení, přijme objekt `nextRetryDelayInMilliseconds`implementující rozhraní, který má jednu metodu s názvem .</span><span class="sxs-lookup"><span data-stu-id="47182-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="47182-210">`nextRetryDelayInMilliseconds`trvá jeden argument s `RetryContext`typem .</span><span class="sxs-lookup"><span data-stu-id="47182-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="47182-211">Má `RetryContext` tři vlastnosti: `previousRetryCount`, `elapsedMilliseconds` a `retryReason` `number` které `Error` jsou `number`, a a příslušně.</span><span class="sxs-lookup"><span data-stu-id="47182-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="47182-212">Před prvním pokusem o `previousRetryCount` `elapsedMilliseconds` opětovné připojení, a `retryReason` to jak a bude nula a bude chyba, která způsobila ztrátu připojení.</span><span class="sxs-lookup"><span data-stu-id="47182-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="47182-213">Po každém neúspěšném `previousRetryCount` pokusu o opakování, bude `elapsedMilliseconds` zvýší o jeden, bude aktualizován tak, aby odrážely množství `retryReason` času stráveného opětovné připojení tak daleko v milisekundách a bude chyba, která způsobila poslední pokus o opětovné připojení k selhání.</span><span class="sxs-lookup"><span data-stu-id="47182-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="47182-214">`nextRetryDelayInMilliseconds`musí vrátit číslo představující počet milisekund čekat před dalším pokusem `null` o `HubConnection` opětovné připojení nebo pokud by měl přestat znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="47182-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="47182-215">Případně můžete napsat kód, který bude znovu klienta ručně, jak je znázorněno v [ručně znovu připojit](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="47182-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="47182-216">Ruční opětovné připojení</span><span class="sxs-lookup"><span data-stu-id="47182-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="47182-217">Před 3.0 se klient SignalR JavaScriptu automaticky nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="47182-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="47182-218">Musíte napsat kód, který bude znovu klienta ručně.</span><span class="sxs-lookup"><span data-stu-id="47182-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="47182-219">Následující kód ukazuje typický přístup ručního opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="47182-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="47182-220">Funkce (v tomto případě `start` funkce) je vytvořen pro spuštění připojení.</span><span class="sxs-lookup"><span data-stu-id="47182-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="47182-221">Volání `start` funkce v obslužné `onclose` rutině události připojení.</span><span class="sxs-lookup"><span data-stu-id="47182-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="47182-222">Implementace v reálném světě by použít exponenciální back-off nebo opakovat zadaný počet opakování před vzdání.</span><span class="sxs-lookup"><span data-stu-id="47182-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="47182-223">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="47182-223">Additional resources</span></span>

* [<span data-ttu-id="47182-224">Referenční dokumentace k rozhraní API v JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="47182-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="47182-225">Výukový program javascriptu</span><span class="sxs-lookup"><span data-stu-id="47182-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="47182-226">Kurz webpacku a jazyka TypeScript</span><span class="sxs-lookup"><span data-stu-id="47182-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="47182-227">Rozbočovače</span><span class="sxs-lookup"><span data-stu-id="47182-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="47182-228">Klient rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="47182-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="47182-229">Publikování aplikací do Azure</span><span class="sxs-lookup"><span data-stu-id="47182-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="47182-230">Žádosti o křížový původ (CORS)</span><span class="sxs-lookup"><span data-stu-id="47182-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="47182-231">[Dokumentace SignalR azure service bez serveru](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="47182-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
