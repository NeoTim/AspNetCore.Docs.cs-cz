---
<span data-ttu-id="f3d8a-101">title: ' ASP.NET Core SignalR klienta JavaScriptu ' Autor: Description: ' přehled ASP.NET Core SignalR JavaScript Client. '</span><span class="sxs-lookup"><span data-stu-id="f3d8a-101">title: 'ASP.NET Core SignalR JavaScript client' author: description: 'Overview of ASP.NET Core SignalR JavaScript client.'</span></span>
<span data-ttu-id="f3d8a-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f3d8a-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f3d8a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f3d8a-103">'Blazor'</span></span>
- <span data-ttu-id="f3d8a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f3d8a-104">'Identity'</span></span>
- <span data-ttu-id="f3d8a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f3d8a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="f3d8a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f3d8a-106">'Razor'</span></span>
- <span data-ttu-id="f3d8a-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f3d8a-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="f3d8a-108">SignalRKlient ASP.NET Core JavaScript</span><span class="sxs-lookup"><span data-stu-id="f3d8a-108">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="f3d8a-109">Od [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="f3d8a-109">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="f3d8a-110">SignalRKlientská knihovna ASP.NET Core JavaScript umožňuje vývojářům volat kód centra na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-110">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="f3d8a-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f3d8a-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="f3d8a-112">Instalace SignalR balíčku klienta</span><span class="sxs-lookup"><span data-stu-id="f3d8a-112">Install the SignalR client package</span></span>

<span data-ttu-id="f3d8a-113">SignalRKlientská knihovna JavaScriptu je dodávána jako balíček [npm](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-113">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="f3d8a-114">Následující oddíly popisují různé způsoby instalace klientské knihovny.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-114">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="f3d8a-115">Instalace pomocí npm</span><span class="sxs-lookup"><span data-stu-id="f3d8a-115">Install with npm</span></span>

<span data-ttu-id="f3d8a-116">Pokud používáte Visual Studio, spusťte následující příkazy z **konzoly Správce balíčků** v kořenové složce.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-116">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="f3d8a-117">Pro Visual Studio Code spusťte následující příkazy z **integrovaného terminálu**.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-117">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="f3d8a-118">NPM nainstaluje obsah balíčku do složky \*node_modules \\ @microsoft\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-118">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="f3d8a-119">Vytvořte novou složku s názvem *Signal* ve složce *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-119">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="f3d8a-120">Zkopírujte soubor *Signal. js* do složky *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-120">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="f3d8a-121">NPM nainstaluje obsah balíčku do složky \*node_modules \\ @aspnet\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-121">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="f3d8a-122">Vytvořte novou složku s názvem *Signal* ve složce *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-122">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="f3d8a-123">Zkopírujte soubor *Signal. js* do složky *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-123">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="f3d8a-124">Odkazování na SignalR klienta JavaScriptu v `<script>` elementu.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-124">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="f3d8a-125">Například:</span><span class="sxs-lookup"><span data-stu-id="f3d8a-125">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="f3d8a-126">Použít Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="f3d8a-126">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="f3d8a-127">Chcete-li použít klientskou knihovnu bez npm požadavků, proveďte odkaz na kopii klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-127">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="f3d8a-128">Například:</span><span class="sxs-lookup"><span data-stu-id="f3d8a-128">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="f3d8a-129">Klientská knihovna je k dispozici na následujících sítě CDN:</span><span class="sxs-lookup"><span data-stu-id="f3d8a-129">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="f3d8a-130">cdnjs</span><span class="sxs-lookup"><span data-stu-id="f3d8a-130">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="f3d8a-131">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="f3d8a-131">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="f3d8a-132">unpkg</span><span class="sxs-lookup"><span data-stu-id="f3d8a-132">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="f3d8a-133">cdnjs</span><span class="sxs-lookup"><span data-stu-id="f3d8a-133">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="f3d8a-134">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="f3d8a-134">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="f3d8a-135">unpkg</span><span class="sxs-lookup"><span data-stu-id="f3d8a-135">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="f3d8a-136">Instalace pomocí LibMan</span><span class="sxs-lookup"><span data-stu-id="f3d8a-136">Install with LibMan</span></span>

<span data-ttu-id="f3d8a-137">[LibMan](xref:client-side/libman/index) se dá použít k instalaci určitých souborů knihovny klienta z klientské knihovny hostované v CDN.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-137">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="f3d8a-138">Například do projektu přidejte pouze soubor JavaScriptu minifikovaného.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-138">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="f3d8a-139">Podrobnosti o tomto přístupu najdete v tématu [Přidání SignalR klientské knihovny](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="f3d8a-139">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="f3d8a-140">Připojení k centru</span><span class="sxs-lookup"><span data-stu-id="f3d8a-140">Connect to a hub</span></span>

<span data-ttu-id="f3d8a-141">Následující kód vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-141">The following code creates and starts a connection.</span></span> <span data-ttu-id="f3d8a-142">V názvu centra se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-142">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="f3d8a-143">Připojení mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="f3d8a-143">Cross-origin connections</span></span>

<span data-ttu-id="f3d8a-144">Prohlížeče obvykle načítají připojení ze stejné domény jako požadovanou stránku.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-144">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="f3d8a-145">Existují však situace, kdy je vyžadováno připojení k jiné doméně.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-145">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="f3d8a-146">Chcete-li zabránit škodlivému webu v čtení citlivých dat z jiné lokality, [připojení mezi zdroji](xref:security/cors) jsou ve výchozím nastavení zakázána.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-146">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="f3d8a-147">Pokud chcete povolit žádost o více zdrojů, povolte ji ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-147">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="f3d8a-148">Volání metod centra z klienta</span><span class="sxs-lookup"><span data-stu-id="f3d8a-148">Call hub methods from client</span></span>

<span data-ttu-id="f3d8a-149">Klienti JavaScriptu volají veřejné metody na rozbočovačích prostřednictvím metody [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="f3d8a-149">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="f3d8a-150">`invoke`Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="f3d8a-150">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="f3d8a-151">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="f3d8a-151">The name of the hub method.</span></span> <span data-ttu-id="f3d8a-152">V následujícím příkladu je název metody v centru `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-152">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="f3d8a-153">Jakékoli argumenty definované v metodě hub.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-153">Any arguments defined in the hub method.</span></span> <span data-ttu-id="f3d8a-154">V následujícím příkladu je název argumentu `message` .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-154">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="f3d8a-155">Vzorový kód používá syntaxi funkce šipky, která je podporována v aktuálních verzích všech hlavních prohlížečů s výjimkou aplikace Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-155">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="f3d8a-156">Pokud používáte SignalR službu Azure v režimu bez *serveru*, nemůžete volat metody centra z klienta.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-156">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="f3d8a-157">Další informace najdete v dokumentaci ke [ SignalR službě](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="f3d8a-157">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="f3d8a-158">`invoke`Metoda vrací [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-158">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="f3d8a-159">`Promise`Je vyřešen s návratovou hodnotou (pokud existuje), když metoda na serveru vrátí.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-159">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="f3d8a-160">Pokud metoda na serveru vyvolá chybu, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-160">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="f3d8a-161">Použijte `then` metody a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="f3d8a-161">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="f3d8a-162">`send`Metoda vrátí JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-162">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="f3d8a-163">`Promise`Je vyřešena při odeslání zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-163">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="f3d8a-164">Pokud při odesílání zprávy dojde k chybě, bude `Promise` zpráva odmítnuta s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-164">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="f3d8a-165">Použijte `then` metody a `catch` na `Promise` sebe samé ke zpracování těchto případů (nebo `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="f3d8a-165">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="f3d8a-166">Použití nástroje `send` nečeká na přijetí zprávy serverem.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-166">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="f3d8a-167">V důsledku toho není možné vracet data nebo chyby ze serveru.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-167">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="f3d8a-168">Volání metod klienta z centra</span><span class="sxs-lookup"><span data-stu-id="f3d8a-168">Call client methods from hub</span></span>

<span data-ttu-id="f3d8a-169">Chcete-li přijímat zprávy z centra, definujte metodu pomocí metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-169">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="f3d8a-170">Název metody klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-170">The name of the JavaScript client method.</span></span> <span data-ttu-id="f3d8a-171">V následujícím příkladu je název metody `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-171">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="f3d8a-172">Argumenty, které rozbočovač předává metodě.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-172">Arguments the hub passes to the method.</span></span> <span data-ttu-id="f3d8a-173">V následujícím příkladu je hodnota argumentu `message` .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-173">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="f3d8a-174">Předchozí kód v aplikaci se `connection.on` spustí, když kód na straně serveru ho volá pomocí metody [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-174">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="f3d8a-175">Určuje, která metoda klienta má být volána, a to tak, že odpovídá názvu metody a argumentům definovaným v `SendAsync` a `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-175"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="f3d8a-176">Osvědčeným postupem je zavolat metodu [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) na `HubConnection` za `on` .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-176">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="f3d8a-177">Tím zajistíte, aby byly obslužné rutiny registrovány před přijetím jakýchkoli zpráv.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-177">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="f3d8a-178">Zpracování chyb a protokolování</span><span class="sxs-lookup"><span data-stu-id="f3d8a-178">Error handling and logging</span></span>

<span data-ttu-id="f3d8a-179">Řetězení `catch` metody ke konci `start` metody za účelem zpracování chyb na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-179">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="f3d8a-180">Slouží `console.error` k výstupu chyb do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-180">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="f3d8a-181">Nastavte trasování protokolu na straně klienta předáním protokolovacího nástroje a typu události, které se mají protokolovat při navázání spojení.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-181">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="f3d8a-182">Zprávy jsou protokolovány se zadanou úrovní protokolu a vyšší.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-182">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="f3d8a-183">K dispozici jsou následující úrovně protokolu:</span><span class="sxs-lookup"><span data-stu-id="f3d8a-183">Available log levels are as follows:</span></span>

* <span data-ttu-id="f3d8a-184">`signalR.LogLevel.Error`&ndash;Chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-184">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="f3d8a-185">Protokoluje `Error` pouze zprávy.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-185">Logs `Error` messages only.</span></span>
* <span data-ttu-id="f3d8a-186">`signalR.LogLevel.Warning`&ndash;Zprávy upozorňující na potenciální chyby.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-186">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="f3d8a-187">Protokoly `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-187">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="f3d8a-188">`signalR.LogLevel.Information`&ndash;Stavové zprávy bez chyb.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-188">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="f3d8a-189">Protokoly `Information` , `Warning` a `Error` zprávy.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-189">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="f3d8a-190">`signalR.LogLevel.Trace`&ndash;Sleduje zprávy.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-190">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="f3d8a-191">Zaznamená vše, včetně dat přepravovaných mezi centrem a klientem.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-191">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="f3d8a-192">K nakonfigurování úrovně protokolu použijte metodu [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) pro [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-192">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="f3d8a-193">Zprávy jsou protokolovány do konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-193">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="f3d8a-194">Znovu připojit klienty</span><span class="sxs-lookup"><span data-stu-id="f3d8a-194">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="f3d8a-195">Automaticky znovu připojit</span><span class="sxs-lookup"><span data-stu-id="f3d8a-195">Automatically reconnect</span></span>

<span data-ttu-id="f3d8a-196">Klient jazyka JavaScript pro SignalR lze nakonfigurovat tak, aby se automaticky znovu připojil pomocí `withAutomaticReconnect` metody v [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="f3d8a-196">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="f3d8a-197">Ve výchozím nastavení se automaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-197">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="f3d8a-198">Bez parametrů `withAutomaticReconnect()` nakonfiguruje klienta, aby čekal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-198">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="f3d8a-199">Před zahájením jakýchkoli pokusů o opětovné připojení `HubConnection` přejdete do `HubConnectionState.Reconnecting` stavu a dojde k jeho `onreconnecting` zpětnému volání namísto přechodu do `Disconnected` stavu a aktivaci jeho `onclose` zpětného volání, jako je třeba `HubConnection` automatické opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-199">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="f3d8a-200">Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-200">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="f3d8a-201">Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, převede se `HubConnection` zpátky do `Connected` stavu a spustí zpětná `onreconnected` volání.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-201">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="f3d8a-202">To vám umožní informovat uživatele o tom, že bylo připojení znovu navázáno.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-202">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="f3d8a-203">Vzhledem k tomu, že připojení na serveru zcela začíná, `connectionId` bude zpětnému volání k dispozici nový `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-203">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="f3d8a-204">`onreconnected`Parametr zpětného volání `connectionId` bude nedefinován, pokud `HubConnection` byl nakonfigurován tak, aby [přeskočil vyjednávání](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="f3d8a-204">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="f3d8a-205">`withAutomaticReconnect()`neprovede konfiguraci `HubConnection` pro opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:</span><span class="sxs-lookup"><span data-stu-id="f3d8a-205">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="f3d8a-206">Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde do `Disconnected` stavu a aktivuje jeho zpětné volání při jeho [ukončení](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-206">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="f3d8a-207">To vám umožní informovat uživatele o trvalé ztrátě připojení a doporučit aktualizaci stránky:</span><span class="sxs-lookup"><span data-stu-id="f3d8a-207">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="f3d8a-208">Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování pro opětovné připojení, `withAutomaticReconnect` přijme pole čísel představující zpoždění v milisekundách, které se má počkat, než se spustí pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-208">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="f3d8a-209">V předchozím příkladu se nakonfiguruje `HubConnection` tak, aby se při pokusu o opětovné připojení ihned po ztrátě připojení spouštěla znovu.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-209">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="f3d8a-210">To platí také pro výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-210">This is also true for the default configuration.</span></span>

<span data-ttu-id="f3d8a-211">Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-211">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="f3d8a-212">Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-212">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="f3d8a-213">Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o ukončení pokusu o opětovné připojení v dalších 30 sekundách, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-213">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="f3d8a-214">Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení, je třeba `withAutomaticReconnect` přijmout objekt implementující `IRetryPolicy` rozhraní, které má jedinou metodu s názvem `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-214">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="f3d8a-215">`nextRetryDelayInMilliseconds`přijímá jeden argument s typem `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="f3d8a-215">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="f3d8a-216">`RetryContext`Má tři vlastnosti: `previousRetryCount` , `elapsedMilliseconds` a `retryReason` , které jsou a v `number` `number` `Error` uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-216">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="f3d8a-217">Před prvním pokusem o opětovné připojení budou obě `previousRetryCount` i `elapsedMilliseconds` nulové a `retryReason` dojde k chybě, která způsobila ztrátu připojení.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-217">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="f3d8a-218">Po každém neúspěšném pokusu o opakování se bude `previousRetryCount` aktualizovat o jednu, `elapsedMilliseconds` aby odrážela dobu strávenou opětovným opětovným připojením v milisekundách `retryReason` . výsledkem bude chyba, která způsobila selhání posledního pokusu o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-218">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="f3d8a-219">`nextRetryDelayInMilliseconds`musí vracet buď číslo představující počet milisekund, po které se má čekat před dalším pokusem o opětovné připojení, nebo `null` jestli se `HubConnection` má zastavit opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-219">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="f3d8a-220">Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="f3d8a-220">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="f3d8a-221">Ručně znovu připojit</span><span class="sxs-lookup"><span data-stu-id="f3d8a-221">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="f3d8a-222">Před 3,0 se klient JavaScriptu pro SignalR neautomaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-222">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="f3d8a-223">Musíte napsat kód, který bude znovu připojit klienta ručně.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-223">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="f3d8a-224">Následující kód ukazuje typický postup ručního opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="f3d8a-224">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="f3d8a-225">`start`Pro spuštění připojení je vytvořena funkce (v tomto případě funkce).</span><span class="sxs-lookup"><span data-stu-id="f3d8a-225">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="f3d8a-226">Zavolejte `start` funkci v `onclose` popisovači události připojení.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-226">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="f3d8a-227">Implementace reálného světa využije exponenciální přerušení nebo opakuje zadaný počet opakování.</span><span class="sxs-lookup"><span data-stu-id="f3d8a-227">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3d8a-228">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f3d8a-228">Additional resources</span></span>

* [<span data-ttu-id="f3d8a-229">Referenční dokumentace k rozhraní API v JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="f3d8a-229">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="f3d8a-230">Kurz JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="f3d8a-230">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="f3d8a-231">Kurz pro Webpack a TypeScript</span><span class="sxs-lookup"><span data-stu-id="f3d8a-231">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="f3d8a-232">Centra</span><span class="sxs-lookup"><span data-stu-id="f3d8a-232">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f3d8a-233">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="f3d8a-233">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="f3d8a-234">Publikování aplikací do Azure</span><span class="sxs-lookup"><span data-stu-id="f3d8a-234">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="f3d8a-235">Žádosti mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="f3d8a-235">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="f3d8a-236">[SignalRDokumentace k serveru se službou Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="f3d8a-236">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
