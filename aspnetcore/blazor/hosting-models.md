---
title: ASP.NET Core modely hostování Blazor
author: guardrex
description: Pochopení Blazor modelů hostování na straně klienta a na straně serveru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/hosting-models
ms.openlocfilehash: 9dd96ff6e3539bf1c3e932b33776b16d0fbb2d34
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371792"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="80944-103">ASP.NET Core modely hostování Blazor</span><span class="sxs-lookup"><span data-stu-id="80944-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="80944-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="80944-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="80944-105">Blazor je webová architektura navržená tak, aby běžela na straně klienta v prohlížeči na bázi .NET runtime založené na webovém [sestavení](https://webassembly.org/)(*Blazor na straně klienta*) nebo na straně serveru v ASP.NET Core (*Blazor Server*).</span><span class="sxs-lookup"><span data-stu-id="80944-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor client-side*) or server-side in ASP.NET Core (*Blazor server-side*).</span></span> <span data-ttu-id="80944-106">Bez ohledu na model hostování jsou modely aplikací a komponent *stejné*.</span><span class="sxs-lookup"><span data-stu-id="80944-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="80944-107">Chcete-li vytvořit projekt pro modely hostování popsané v tomto článku, přečtěte si téma <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="80944-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="client-side"></a><span data-ttu-id="80944-108">Na straně klienta</span><span class="sxs-lookup"><span data-stu-id="80944-108">Client-side</span></span>

<span data-ttu-id="80944-109">Hlavní hostující model pro Blazor je spuštěn na straně klienta v prohlížeči na webovém sestavení.</span><span class="sxs-lookup"><span data-stu-id="80944-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="80944-110">Do prohlížeče se stáhnou aplikace Blazor, její závislosti a modul runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="80944-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="80944-111">Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="80944-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="80944-112">Aktualizace uživatelského rozhraní a zpracování událostí se vyskytují v rámci stejného procesu.</span><span class="sxs-lookup"><span data-stu-id="80944-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="80944-113">Prostředky aplikace se nasazují jako statické soubory na webový server nebo službu, která je schopná obsluhovat statický obsah klientům.</span><span class="sxs-lookup"><span data-stu-id="80944-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor na straně klienta: Aplikace Blazor běží ve vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/client-side.png)

<span data-ttu-id="80944-115">Chcete-li vytvořit aplikaci Blazor pomocí modelu hostování na straně klienta, použijte kteroukoli z následujících šablon:</span><span class="sxs-lookup"><span data-stu-id="80944-115">To create a Blazor app using the client-side hosting model, use either of the following templates:</span></span>

* <span data-ttu-id="80944-116">**Blazor (na straně klienta)** ([dotnet New blazor](/dotnet/core/tools/dotnet-new)) &ndash; Nasazeno jako sada statických souborů.</span><span class="sxs-lookup"><span data-stu-id="80944-116">**Blazor (client-side)** ([dotnet new blazor](/dotnet/core/tools/dotnet-new)) &ndash; Deployed as a set of static files.</span></span>
* <span data-ttu-id="80944-117">**Blazor (ASP.NET Core Hosted)** ([dotnet New blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; Hostováno serverem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="80944-117">**Blazor (ASP.NET Core Hosted)** ([dotnet new blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; Hosted by an ASP.NET Core server.</span></span> <span data-ttu-id="80944-118">Aplikace ASP.NET Core obsluhuje aplikaci Blazor klientům.</span><span class="sxs-lookup"><span data-stu-id="80944-118">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="80944-119">Klientská aplikace Blazor může komunikovat se serverem přes síť pomocí volání webového rozhraní API nebo signalizace. [](xref:signalr/introduction)</span><span class="sxs-lookup"><span data-stu-id="80944-119">The Blazor client-side app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="80944-120">Šablony obsahují skript *blazor. WebAssembly. js* , který zpracovává:</span><span class="sxs-lookup"><span data-stu-id="80944-120">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="80944-121">Stažení modulu runtime .NET, aplikace a závislostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="80944-121">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="80944-122">Inicializace modulu runtime pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="80944-122">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="80944-123">Model hostování na straně klienta nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="80944-123">The client-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="80944-124">Neexistuje žádná závislost na straně serveru .NET.</span><span class="sxs-lookup"><span data-stu-id="80944-124">There's no .NET server-side dependency.</span></span> <span data-ttu-id="80944-125">Aplikace po stažení do klienta plně funguje.</span><span class="sxs-lookup"><span data-stu-id="80944-125">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="80944-126">Prostředky a možnosti klienta jsou plně využité.</span><span class="sxs-lookup"><span data-stu-id="80944-126">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="80944-127">Práce je ze serveru převedena na klienta.</span><span class="sxs-lookup"><span data-stu-id="80944-127">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="80944-128">Pro hostování aplikace není vyžadován ASP.NET Core webový server.</span><span class="sxs-lookup"><span data-stu-id="80944-128">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="80944-129">Jsou možné scénáře nasazení bez serveru (například poskytování aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="80944-129">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="80944-130">Downsides hostování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="80944-130">There are downsides to client-side hosting:</span></span>

* <span data-ttu-id="80944-131">Aplikace je omezená na možnosti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="80944-131">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="80944-132">Je vyžadován klientský hardware a software (například podpora WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="80944-132">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="80944-133">Velikost ke stažení je větší a aplikace trvá déle, než se načtou.</span><span class="sxs-lookup"><span data-stu-id="80944-133">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="80944-134">Podpora modulu runtime .NET a nástrojů je méně vyspělá.</span><span class="sxs-lookup"><span data-stu-id="80944-134">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="80944-135">Například omezení existují v [.NET Standard](/dotnet/standard/net-standard) podpoře a ladění.</span><span class="sxs-lookup"><span data-stu-id="80944-135">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="server-side"></a><span data-ttu-id="80944-136">Na straně serveru</span><span class="sxs-lookup"><span data-stu-id="80944-136">Server-side</span></span>

<span data-ttu-id="80944-137">S modelem hostování na straně serveru se aplikace spouští na serveru z aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="80944-137">With the server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="80944-138">Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes připojení [](xref:signalr/introduction) k signalizaci.</span><span class="sxs-lookup"><span data-stu-id="80944-138">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![Prohlížeč komunikuje s aplikací (hostovanou v aplikaci ASP.NET Core) na serveru přes připojení k signalizaci.](hosting-models/_static/server-side.png)

<span data-ttu-id="80944-140">K vytvoření aplikace Blazor pomocí modelu hostování na straně serveru použijte šablonu **aplikace serveru** pro ASP.NET Core Blazor ([dotnet New blazorserverside](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="80944-140">To create a Blazor app using the server-side hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserverside](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="80944-141">Aplikace ASP.NET Core hostuje serverovou aplikaci a vytvoří koncový bod signalizace, kde se klienti připojují.</span><span class="sxs-lookup"><span data-stu-id="80944-141">The ASP.NET Core app hosts the server-side app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="80944-142">Aplikace ASP.NET Core odkazuje na `Startup` třídu aplikace, která se má přidat:</span><span class="sxs-lookup"><span data-stu-id="80944-142">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="80944-143">Služby na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="80944-143">Server-side services.</span></span>
* <span data-ttu-id="80944-144">Aplikace do kanálu pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="80944-144">The app to the request handling pipeline.</span></span>

<span data-ttu-id="80944-145">Skript&dagger; *blazor. Server. js* vytvoří připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="80944-145">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="80944-146">Je zodpovědností aplikace zachovat a obnovit stav aplikace podle potřeby (například v případě ztraceného síťového připojení).</span><span class="sxs-lookup"><span data-stu-id="80944-146">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="80944-147">Model hostování na straně serveru nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="80944-147">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="80944-148">Velikost ke stažení je výrazně menší než aplikace na straně klienta a aplikace se načítá mnohem rychleji.</span><span class="sxs-lookup"><span data-stu-id="80944-148">Download size is significantly smaller than a client-side app, and the app loads much faster.</span></span>
* <span data-ttu-id="80944-149">Aplikace plně využívá možnosti serveru, včetně použití všech rozhraní API kompatibilních s rozhraním .NET Core.</span><span class="sxs-lookup"><span data-stu-id="80944-149">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="80944-150">Rozhraní .NET Core na serveru se používá ke spuštění aplikace, takže stávající nástroje .NET, jako je ladění, fungují podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="80944-150">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="80944-151">Podporují se tenké klienty.</span><span class="sxs-lookup"><span data-stu-id="80944-151">Thin clients are supported.</span></span> <span data-ttu-id="80944-152">Například aplikace na straně serveru fungují s prohlížeči, které nepodporují WebAssembly a na zařízeních s omezením prostředků.</span><span class="sxs-lookup"><span data-stu-id="80944-152">For example, server-side apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="80944-153">Základ pro .NET/C# kód aplikace, včetně kódu komponenty aplikace, není obsluhován klientům.</span><span class="sxs-lookup"><span data-stu-id="80944-153">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="80944-154">Existuje downsides hostování na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="80944-154">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="80944-155">Vyšší latence obvykle existuje.</span><span class="sxs-lookup"><span data-stu-id="80944-155">Higher latency usually exists.</span></span> <span data-ttu-id="80944-156">Každá interakce uživatele zahrnuje směrování sítě.</span><span class="sxs-lookup"><span data-stu-id="80944-156">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="80944-157">Neexistuje žádná podpora offline.</span><span class="sxs-lookup"><span data-stu-id="80944-157">There's no offline support.</span></span> <span data-ttu-id="80944-158">Pokud připojení klienta neproběhne úspěšně, aplikace přestane fungovat.</span><span class="sxs-lookup"><span data-stu-id="80944-158">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="80944-159">Pro aplikace s mnoha uživateli je škálovatelnost náročná.</span><span class="sxs-lookup"><span data-stu-id="80944-159">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="80944-160">Server musí spravovat více připojení klientů a zpracovávat stav klienta.</span><span class="sxs-lookup"><span data-stu-id="80944-160">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="80944-161">Pro obsluhu aplikace je vyžadován ASP.NET Core Server.</span><span class="sxs-lookup"><span data-stu-id="80944-161">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="80944-162">Scénáře nasazení bez serveru nejsou možné (například poskytování aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="80944-162">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="80944-163">&dagger;Skript *blazor. Server. js* se obsluhuje z vloženého prostředku v ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="80944-163">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="80944-164">Opětovné připojení ke stejnému serveru</span><span class="sxs-lookup"><span data-stu-id="80944-164">Reconnection to the same server</span></span>

<span data-ttu-id="80944-165">Blazor aplikace na straně serveru vyžadují, aby se k serveru mohl připojit aktivní signál.</span><span class="sxs-lookup"><span data-stu-id="80944-165">Blazor server-side apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="80944-166">Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru.</span><span class="sxs-lookup"><span data-stu-id="80944-166">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="80944-167">Pokud je stav klienta stále v paměti, klientská relace pokračuje bez ztráty stavu.</span><span class="sxs-lookup"><span data-stu-id="80944-167">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>
 
<span data-ttu-id="80944-168">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="80944-168">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="80944-169">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="80944-169">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="80944-170">Chcete-li přizpůsobit uživatelské rozhraní, definujte element `components-reconnect-modal` s jako `id` svůj na stránce *_Host. cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="80944-170">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="80944-171">Klient aktualizuje tento prvek pomocí jedné z následujících tříd šablony stylů CSS na základě stavu připojení:</span><span class="sxs-lookup"><span data-stu-id="80944-171">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>
 
* <span data-ttu-id="80944-172">`components-reconnect-show`&ndash; Zobrazit uživatelské rozhraní, které indikuje, že připojení bylo ztraceno a klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="80944-172">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="80944-173">`components-reconnect-hide`&ndash; Klient má aktivní připojení, skryje uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="80944-173">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="80944-174">`components-reconnect-failed`&ndash; Opětovné připojení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="80944-174">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="80944-175">Chcete-li se znovu pokusit `window.Blazor.reconnect()`o opětovné připojení, zavolejte.</span><span class="sxs-lookup"><span data-stu-id="80944-175">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="80944-176">Stav opětovného připojení po předvykreslování</span><span class="sxs-lookup"><span data-stu-id="80944-176">Stateful reconnection after prerendering</span></span>
 
<span data-ttu-id="80944-177">Blazor aplikace na straně serveru jsou nastavené ve výchozím nastavení tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="80944-177">Blazor server-side apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="80944-178">To je nastavené na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="80944-178">This is set up in the *_Host.cshtml* Razor page:</span></span>
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
<span data-ttu-id="80944-179">Klient se znovu připojí k serveru se stejným stavem, který se použil k proprerender aplikace.</span><span class="sxs-lookup"><span data-stu-id="80944-179">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="80944-180">Pokud je stav aplikace stále v paměti, stav součásti nebude po navázání připojení k signalizaci znovu vykreslen.</span><span class="sxs-lookup"><span data-stu-id="80944-180">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="80944-181">Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="80944-181">Render stateful interactive components from Razor pages and views</span></span>
 
<span data-ttu-id="80944-182">Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="80944-182">Stateful interactive components can be added to a Razor page or view.</span></span> <span data-ttu-id="80944-183">Když se stránka nebo zobrazení vykresluje, komponenta se s ní předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="80944-183">When the page or view renders, the component is prerendered with it.</span></span> <span data-ttu-id="80944-184">Aplikace se pak znovu připojí ke stavu součásti, jakmile se naváže připojení klienta, pokud je stav stále v paměti.</span><span class="sxs-lookup"><span data-stu-id="80944-184">The app then reconnects to the component state once the client connection is established as long as the state is still in memory.</span></span>
 
<span data-ttu-id="80944-185">Například následující stránka Razor vykresluje `Counter` komponentu s počátečním počtem zadaným pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="80944-185">For example, the following Razor page renders a `Counter` component with an initial count that's specified using a form:</span></span>
 
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

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="80944-186">Rozpoznat, kdy se aplikace předvykresluje</span><span class="sxs-lookup"><span data-stu-id="80944-186">Detect when the app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a><span data-ttu-id="80944-187">Konfigurace klienta nástroje Signal pro aplikace Blazor na straně serveru</span><span class="sxs-lookup"><span data-stu-id="80944-187">Configure the SignalR client for Blazor server-side apps</span></span>
 
<span data-ttu-id="80944-188">V některých případech je třeba nakonfigurovat klienta nástroje Signal používaného Blazor aplikacemi na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="80944-188">Sometimes, you need to configure the SignalR client used by Blazor server-side apps.</span></span> <span data-ttu-id="80944-189">Například můžete chtít nakonfigurovat protokolování na straně klienta signalizace, aby bylo možné diagnostikovat problém s připojením.</span><span class="sxs-lookup"><span data-stu-id="80944-189">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
 
<span data-ttu-id="80944-190">Konfigurace klienta signalizace v souboru Pages */_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="80944-190">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="80944-191">Přidejte atribut do značky pro skript *blazor. Server. js.* `<script>` `autostart="false"`</span><span class="sxs-lookup"><span data-stu-id="80944-191">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="80944-192">Zavolejte `Blazor.start` a předejte do konfiguračního objektu, který určuje tvůrce signálu.</span><span class="sxs-lookup"><span data-stu-id="80944-192">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>
 
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

## <a name="additional-resources"></a><span data-ttu-id="80944-193">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="80944-193">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
