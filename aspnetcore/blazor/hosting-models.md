---
title: ASP.NET Core Blazor hostování modelů
author: guardrex
description: Seznamte se s Blazor na straně klienta i stranu serveru hostování modely.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/hosting-models
ms.openlocfilehash: 2f5a8a037534f4a881b474dd8bcc3119f504123a
ms.sourcegitcommit: 040aedca220ed24ee1726e6886daf6906f95a028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67892244"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="41448-103">ASP.NET Core Blazor hostování modelů</span><span class="sxs-lookup"><span data-stu-id="41448-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="41448-104">Podle [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="41448-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="41448-105">Blazor je webové rozhraní navržené ke spuštění na straně klienta v prohlížeči na [WebAssembly](https://webassembly.org/)– na základě modulu runtime .NET (*Blazor na straně klienta*) nebo na serveru ASP.NET Core (*Blazor na straně serveru* ).</span><span class="sxs-lookup"><span data-stu-id="41448-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor client-side*) or server-side in ASP.NET Core (*Blazor server-side*).</span></span> <span data-ttu-id="41448-106">Bez ohledu na modelech hostování modelu, aplikace a komponenty *jsou stejné*.</span><span class="sxs-lookup"><span data-stu-id="41448-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="41448-107">Vytvoření projektu pro modelech hostování popsané v tomto článku najdete v tématu <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="41448-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="client-side"></a><span data-ttu-id="41448-108">Na straně klienta</span><span class="sxs-lookup"><span data-stu-id="41448-108">Client-side</span></span>

<span data-ttu-id="41448-109">Hlavní model hostingu pro Blazor je spuštěné v prohlížeči na WebAssembly straně klienta.</span><span class="sxs-lookup"><span data-stu-id="41448-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="41448-110">Aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="41448-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="41448-111">Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="41448-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="41448-112">Aktualizace uživatelského rozhraní a zpracování událostí dochází v rámci stejného procesu.</span><span class="sxs-lookup"><span data-stu-id="41448-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="41448-113">Prostředky aplikace se nasadí jako statické soubory na webový server nebo služba je schopná obsluhuje statický obsah, na klienty.</span><span class="sxs-lookup"><span data-stu-id="41448-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor straně klienta: Blazor aplikace běží na vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/client-side.png)

<span data-ttu-id="41448-115">Chcete-li vytvořit aplikaci Blazor používá model hostování na straně klienta, použijte jednu z následujících šablon:</span><span class="sxs-lookup"><span data-stu-id="41448-115">To create a Blazor app using the client-side hosting model, use either of the following templates:</span></span>

* <span data-ttu-id="41448-116">**Blazor (na straně klienta)** ([nové blazor dotnet](/dotnet/core/tools/dotnet-new)) &ndash; nasazený jako sada statické soubory.</span><span class="sxs-lookup"><span data-stu-id="41448-116">**Blazor (client-side)** ([dotnet new blazor](/dotnet/core/tools/dotnet-new)) &ndash; Deployed as a set of static files.</span></span>
* <span data-ttu-id="41448-117">**Blazor (ASP.NET Core v prostředí)** ([nové blazorhosted dotnet](/dotnet/core/tools/dotnet-new)) &ndash; hostované serveru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41448-117">**Blazor (ASP.NET Core Hosted)** ([dotnet new blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; Hosted by an ASP.NET Core server.</span></span> <span data-ttu-id="41448-118">Aplikace ASP.NET Core obsluhuje Blazor aplikaci pro klienty.</span><span class="sxs-lookup"><span data-stu-id="41448-118">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="41448-119">Aplikace na straně klienta Blazor komunikovat se serverem přes síť pomocí volání webového rozhraní API nebo [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="41448-119">The Blazor client-side app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="41448-120">Šablony zahrnují *blazor.webassembly.js* skript, který zpracovává:</span><span class="sxs-lookup"><span data-stu-id="41448-120">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="41448-121">Stahuje se modul .NET runtime, aplikace a závislostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="41448-121">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="41448-122">Inicializace modulu runtime a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41448-122">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="41448-123">Model hostingu na straně klienta nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="41448-123">The client-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="41448-124">Neexistuje žádná závislost .NET na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="41448-124">There's no .NET server-side dependency.</span></span> <span data-ttu-id="41448-125">Aplikace je plně funkční po stažení do klienta.</span><span class="sxs-lookup"><span data-stu-id="41448-125">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="41448-126">Materiály pro klienta a možnosti se plně využívají.</span><span class="sxs-lookup"><span data-stu-id="41448-126">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="41448-127">Práce se přebírá ze serveru klienta.</span><span class="sxs-lookup"><span data-stu-id="41448-127">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="41448-128">Webový server ASP.NET Core není vyžadován pro hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="41448-128">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="41448-129">Scénářů bez serveru nasazení je možné (například poskytuje aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="41448-129">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="41448-130">Existují nevýhody hostování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="41448-130">There are downsides to client-side hosting:</span></span>

* <span data-ttu-id="41448-131">Aplikace je omezen na možnosti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="41448-131">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="41448-132">Klienta s podporou, hardware a software (třeba podporu WebAssembly) je povinný.</span><span class="sxs-lookup"><span data-stu-id="41448-132">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="41448-133">Velikost ke stažení je větší a trvat delší dobu načtení aplikace.</span><span class="sxs-lookup"><span data-stu-id="41448-133">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="41448-134">Modul runtime rozhraní .NET a nástroje podpory je méně až po zralé.</span><span class="sxs-lookup"><span data-stu-id="41448-134">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="41448-135">Například v existují omezení [.NET Standard](/dotnet/standard/net-standard) ladění a podporu.</span><span class="sxs-lookup"><span data-stu-id="41448-135">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="server-side"></a><span data-ttu-id="41448-136">Na straně serveru</span><span class="sxs-lookup"><span data-stu-id="41448-136">Server-side</span></span>

<span data-ttu-id="41448-137">S model hostingu na straně serveru aplikace spuštěné na serveru z v rámci aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41448-137">With the server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="41448-138">Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="41448-138">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![V prohlížeči komunikuje s aplikaci (už je hostovaná v rámci aplikace ASP.NET Core) na serveru pomocí připojení SignalR.](hosting-models/_static/server-side.png)

<span data-ttu-id="41448-140">Pokud chcete vytvořit aplikaci Blazor používá model hostování na straně serveru, použijte technologii ASP.NET Core **Blazor serverovou aplikaci** šablony ([nové blazorserverside dotnet](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="41448-140">To create a Blazor app using the server-side hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserverside](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="41448-141">Aplikace ASP.NET Core je hostitelem aplikace na straně serveru a vytvoří SignalR koncový bod, ve kterém se klienti připojují.</span><span class="sxs-lookup"><span data-stu-id="41448-141">The ASP.NET Core app hosts the server-side app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="41448-142">Aplikace ASP.NET Core odkazuje aplikaci `Startup` třídy přidejte:</span><span class="sxs-lookup"><span data-stu-id="41448-142">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="41448-143">Služby na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="41448-143">Server-side services.</span></span>
* <span data-ttu-id="41448-144">Aplikace na žádost o zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="41448-144">The app to the request handling pipeline.</span></span>

<span data-ttu-id="41448-145">*Blazor.server.js* skript&dagger; naváže připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="41448-145">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="41448-146">Je zodpovědností aplikace k zachování a obnovení stavu aplikace podle potřeby (například v případě ztráty připojení).</span><span class="sxs-lookup"><span data-stu-id="41448-146">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="41448-147">Model hostingu na straně serveru nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="41448-147">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="41448-148">Velikost ke stažení je výrazně menší než aplikace na straně klienta a aplikace načte mnohem rychleji.</span><span class="sxs-lookup"><span data-stu-id="41448-148">Download size is significantly smaller than a client-side app, and the app loads much faster.</span></span>
* <span data-ttu-id="41448-149">Aplikace plně využívá funkce serveru, včetně použití libovolné rozhraní API .NET Core kompatibilní.</span><span class="sxs-lookup"><span data-stu-id="41448-149">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="41448-150">.NET core na serveru se používá ke spuštění aplikace, takže existující .NET nástrojů, jako je ladění, funguje podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="41448-150">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="41448-151">Tencí klienti jsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="41448-151">Thin clients are supported.</span></span> <span data-ttu-id="41448-152">Například serverové aplikace fungovat v prohlížečích, které nepodporují WebAssembly a na zařízeních s omezenými zdroji.</span><span class="sxs-lookup"><span data-stu-id="41448-152">For example, server-side apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="41448-153">Aplikace .NET /C# základu kódu, včetně kódu komponenty aplikace, není poskytováni, na klienty.</span><span class="sxs-lookup"><span data-stu-id="41448-153">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="41448-154">Existují nevýhody hostování na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="41448-154">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="41448-155">Obvykle existuje vyšší latencí.</span><span class="sxs-lookup"><span data-stu-id="41448-155">Higher latency usually exists.</span></span> <span data-ttu-id="41448-156">Každá interakce uživatele zahrnuje směrování v síti.</span><span class="sxs-lookup"><span data-stu-id="41448-156">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="41448-157">Není dostupná podpora v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="41448-157">There's no offline support.</span></span> <span data-ttu-id="41448-158">Pokud klienta nepovede, aplikace přestane fungovat.</span><span class="sxs-lookup"><span data-stu-id="41448-158">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="41448-159">Škálovatelnost je obtížné pro aplikace s mnoha uživateli.</span><span class="sxs-lookup"><span data-stu-id="41448-159">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="41448-160">Server musí spravovat připojení více klientů a zpracování stavu klienta.</span><span class="sxs-lookup"><span data-stu-id="41448-160">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="41448-161">Server služby ASP.NET Core je zapotřebí pro zpracování aplikace.</span><span class="sxs-lookup"><span data-stu-id="41448-161">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="41448-162">Scénářů bez serveru nasazení nejsou možné (například poskytuje aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="41448-162">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="41448-163">&dagger;*Blazor.server.js* skript pochází z vloženého prostředku v rámci sdílené ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41448-163">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="41448-164">Opětovné připojení ke stejnému serveru</span><span class="sxs-lookup"><span data-stu-id="41448-164">Reconnection to the same server</span></span>

<span data-ttu-id="41448-165">Blazor serverové aplikace vyžadují aktivní připojení k serveru funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="41448-165">Blazor server-side apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="41448-166">Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru.</span><span class="sxs-lookup"><span data-stu-id="41448-166">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="41448-167">Za předpokladu, stav klienta je stále v paměti, bude pokračovat bez ztráty stavu relace klienta.</span><span class="sxs-lookup"><span data-stu-id="41448-167">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>
 
<span data-ttu-id="41448-168">Když klient zjistí, že připojení bylo ztraceno, je výchozí uživatelské rozhraní a klient se pokusí znovu připojit, zobrazit uživateli.</span><span class="sxs-lookup"><span data-stu-id="41448-168">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="41448-169">Pokud opětovného připojení selže, uživateli se nabídnou možnost opakovat.</span><span class="sxs-lookup"><span data-stu-id="41448-169">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="41448-170">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `components-reconnect-modal` jako jeho `id`.</span><span class="sxs-lookup"><span data-stu-id="41448-170">To customize the UI, define an element with `components-reconnect-modal` as its `id`.</span></span> <span data-ttu-id="41448-171">Klient aktualizuje tento element s jedním z následujících tříd šablon stylů CSS na základě stavu připojení:</span><span class="sxs-lookup"><span data-stu-id="41448-171">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>
 
* <span data-ttu-id="41448-172">`components-reconnect-show` &ndash; Zobrazit uživatelské rozhraní, které označíte, se ztratí připojení a klient se pokouší o obnovení připojení.</span><span class="sxs-lookup"><span data-stu-id="41448-172">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="41448-173">`components-reconnect-hide` &ndash; Klient má aktivní připojení, Skrýt uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="41448-173">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="41448-174">`components-reconnect-failed` &ndash; Opětovné připojení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="41448-174">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="41448-175">Pokusu o opětovné připojení znovu, volání `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="41448-175">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="41448-176">Stavové opětovného připojení po dokončení fáze před vykreslením</span><span class="sxs-lookup"><span data-stu-id="41448-176">Stateful reconnection after prerendering</span></span>
 
<span data-ttu-id="41448-177">Aplikace na straně serveru Blazor nastavení jsou ve výchozím nastavení prerender uživatelského rozhraní na serveru před navázáním připojení klienta k serveru.</span><span class="sxs-lookup"><span data-stu-id="41448-177">Blazor server-side apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="41448-178">Nastavuje se to *_Host.cshtml* stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="41448-178">This is set up in the *_Host.cshtml* Razor page:</span></span>
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
<span data-ttu-id="41448-179">Klient znovu připojí k serveru pomocí stejného stavu, který se použil k prerender aplikace.</span><span class="sxs-lookup"><span data-stu-id="41448-179">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="41448-180">Pokud se stav aplikace je stále v paměti, není stav komponenty rerendered po navázání připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="41448-180">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="41448-181">Vykreslení stavové interaktivní součásti ze stránky Razor a zobrazení</span><span class="sxs-lookup"><span data-stu-id="41448-181">Render stateful interactive components from Razor pages and views</span></span>
 
<span data-ttu-id="41448-182">Stavové interaktivní komponenty lze přidat stránky Razor nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="41448-182">Stateful interactive components can be added to a Razor page or view.</span></span> <span data-ttu-id="41448-183">Při vykreslení stránky nebo zobrazení, komponenta je předkreslených s ním.</span><span class="sxs-lookup"><span data-stu-id="41448-183">When the page or view renders, the component is prerendered with it.</span></span> <span data-ttu-id="41448-184">Aplikace potom se znovu připojí k stav komponenty po připojení klienta pokládáme stav, stav je stále v paměti.</span><span class="sxs-lookup"><span data-stu-id="41448-184">The app then reconnects to the component state once the client connection is established as long as the state is still in memory.</span></span>
 
<span data-ttu-id="41448-185">Například následující stránky Razor vykreslí `Counter` komponentu s počáteční počet, který je určen pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="41448-185">For example, the following Razor page renders a `Counter` component with an initial count that's specified using a form:</span></span>
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialCount" />
    <button type="submit">Set initial count</button>
</form>
 
@(await Html.RenderComponentAsync<Counter>(new { InitialCount = InitialCount }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialCount { get; set; }
}
```

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="41448-186">Rozpoznat, kdy aplikace je před vykreslením</span><span class="sxs-lookup"><span data-stu-id="41448-186">Detect when the app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a><span data-ttu-id="41448-187">Konfigurace klienta SignalR pro Blazor serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="41448-187">Configure the SignalR client for Blazor server-side apps</span></span>
 
<span data-ttu-id="41448-188">V některých případech budete muset nakonfigurovat klienta SignalR používat Blazor aplikace na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="41448-188">Sometimes, you need to configure the SignalR client used by Blazor server-side apps.</span></span> <span data-ttu-id="41448-189">Například můžete chtít konfigurovat protokolování na straně klienta SignalR pro diagnostiku problému připojení.</span><span class="sxs-lookup"><span data-stu-id="41448-189">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
 
<span data-ttu-id="41448-190">Abyste mohli nakonfigurovat klienta SignalR v *Pages/_Host.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="41448-190">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="41448-191">Přidat `autostart="false"` atribut `<script>` značky *blazor.server.js* skriptu.</span><span class="sxs-lookup"><span data-stu-id="41448-191">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="41448-192">Volání `Blazor.start` a předat objekt konfigurace, který určuje Tvůrce SignalR.</span><span class="sxs-lookup"><span data-stu-id="41448-192">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="41448-193">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="41448-193">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
