---
title: ASP.NET Core Blazor hostování modelů
author: guardrex
description: Seznamte se s na straně klienta i stranu serveru Blazor hostování modely.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: blazor/hosting-models
ms.openlocfilehash: c794daf6f33138c57500a04116a3d172f0201bd5
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152798"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="50bf3-103">ASP.NET Core Blazor hostování modelů</span><span class="sxs-lookup"><span data-stu-id="50bf3-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="50bf3-104">Podle [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="50bf3-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="50bf3-105">Blazor je webové rozhraní navržené ke spuštění na straně klienta v prohlížeči na [WebAssembly](http://webassembly.org/)– na základě modulu runtime .NET (*Blazor na straně klienta*) nebo na serveru ASP.NET Core (*Blazor na straně serveru* ).</span><span class="sxs-lookup"><span data-stu-id="50bf3-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](http://webassembly.org/)-based .NET runtime (*Blazor client-side*) or server-side in ASP.NET Core (*Blazor server-side*).</span></span> <span data-ttu-id="50bf3-106">Bez ohledu na modelech hostování modelu, aplikace a komponenty *jsou stejné*.</span><span class="sxs-lookup"><span data-stu-id="50bf3-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="50bf3-107">Vytvoření projektu pro modelech hostování popsané v tomto článku najdete v tématu <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="50bf3-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="client-side"></a><span data-ttu-id="50bf3-108">Na straně klienta</span><span class="sxs-lookup"><span data-stu-id="50bf3-108">Client-side</span></span>

<span data-ttu-id="50bf3-109">Hlavní model hostingu pro Blazor je spuštěné v prohlížeči na WebAssembly straně klienta.</span><span class="sxs-lookup"><span data-stu-id="50bf3-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="50bf3-110">Aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="50bf3-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="50bf3-111">Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="50bf3-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="50bf3-112">Aktualizace uživatelského rozhraní a zpracování událostí dochází v rámci stejného procesu.</span><span class="sxs-lookup"><span data-stu-id="50bf3-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="50bf3-113">Prostředky aplikace se nasadí jako statické soubory na webový server nebo služba je schopná obsluhuje statický obsah, na klienty.</span><span class="sxs-lookup"><span data-stu-id="50bf3-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor straně klienta: Blazor aplikace běží na vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/client-side.png)

<span data-ttu-id="50bf3-115">Chcete-li vytvořit aplikaci Blazor používá model hostování na straně klienta, použijte jednu z následujících šablon:</span><span class="sxs-lookup"><span data-stu-id="50bf3-115">To create a Blazor app using the client-side hosting model, use either of the following templates:</span></span>

* <span data-ttu-id="50bf3-116">**Blazor (na straně klienta)** ([nové blazor dotnet](/dotnet/core/tools/dotnet-new)) &ndash; nasazený jako sada statické soubory.</span><span class="sxs-lookup"><span data-stu-id="50bf3-116">**Blazor (client-side)** ([dotnet new blazor](/dotnet/core/tools/dotnet-new)) &ndash; Deployed as a set of static files.</span></span>
* <span data-ttu-id="50bf3-117">**Blazor (ASP.NET Core v prostředí)** ([nové blazorhosted dotnet](/dotnet/core/tools/dotnet-new)) &ndash; hostované serveru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="50bf3-117">**Blazor (ASP.NET Core Hosted)** ([dotnet new blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; Hosted by an ASP.NET Core server.</span></span> <span data-ttu-id="50bf3-118">Aplikace ASP.NET Core obsluhuje Blazor aplikaci pro klienty.</span><span class="sxs-lookup"><span data-stu-id="50bf3-118">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="50bf3-119">Aplikace na straně klienta Blazor můžete spolupracovat se serverem přes síť pomocí volání webového rozhraní API nebo [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="50bf3-119">The client-side Blazor app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="50bf3-120">Šablony zahrnují *blazor.webassembly.js* skript, který zpracovává:</span><span class="sxs-lookup"><span data-stu-id="50bf3-120">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="50bf3-121">Stahuje se modul .NET runtime, aplikace a závislostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="50bf3-121">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="50bf3-122">Inicializace modulu runtime a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="50bf3-122">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="50bf3-123">Model hostingu na straně klienta nabízí několik výhod.</span><span class="sxs-lookup"><span data-stu-id="50bf3-123">The client-side hosting model offers several benefits.</span></span> <span data-ttu-id="50bf3-124">Blazor na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="50bf3-124">Client-side Blazor:</span></span>

* <span data-ttu-id="50bf3-125">Nemá žádné závislosti .NET na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="50bf3-125">Has no .NET server-side dependency.</span></span>
* <span data-ttu-id="50bf3-126">Plně využívá prostředky klienta a možnosti.</span><span class="sxs-lookup"><span data-stu-id="50bf3-126">Fully leverages client resources and capabilities.</span></span>
* <span data-ttu-id="50bf3-127">Snižování zátěže pracovat ze serveru do klienta.</span><span class="sxs-lookup"><span data-stu-id="50bf3-127">Offloads work from the server to the client.</span></span>
* <span data-ttu-id="50bf3-128">Podporuje scénáře v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="50bf3-128">Supports offline scenarios.</span></span>

<span data-ttu-id="50bf3-129">Existují nevýhody hostování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="50bf3-129">There are downsides to client-side hosting.</span></span> <span data-ttu-id="50bf3-130">Blazor na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="50bf3-130">Client-side Blazor:</span></span>

* <span data-ttu-id="50bf3-131">Omezí aplikace funkcí v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="50bf3-131">Restricts the app to the capabilities of the browser.</span></span>
* <span data-ttu-id="50bf3-132">Vyžaduje klienta s podporou, hardware a software (třeba podporu WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="50bf3-132">Requires capable client hardware and software (for example, WebAssembly support).</span></span>
* <span data-ttu-id="50bf3-133">Má větší velikost ke stažení a delší dobu načítání aplikace.</span><span class="sxs-lookup"><span data-stu-id="50bf3-133">Has a larger download size and longer app load time.</span></span>
* <span data-ttu-id="50bf3-134">Má menší modul .NET runtime a podpora nástrojů pro dospělé (například omezení [.NET Standard](/dotnet/standard/net-standard) podpory a ladění).</span><span class="sxs-lookup"><span data-stu-id="50bf3-134">Has less mature .NET runtime and tooling support (for example, limitations in [.NET Standard](/dotnet/standard/net-standard) support and debugging).</span></span>

## <a name="server-side"></a><span data-ttu-id="50bf3-135">Na straně serveru</span><span class="sxs-lookup"><span data-stu-id="50bf3-135">Server-side</span></span>

<span data-ttu-id="50bf3-136">S model hostingu na straně serveru aplikace spuštěné na serveru z v rámci aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="50bf3-136">With the server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="50bf3-137">Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="50bf3-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![V prohlížeči komunikuje s aplikaci (už je hostovaná v rámci aplikace ASP.NET Core) na serveru pomocí připojení SignalR.](hosting-models/_static/server-side.png)

<span data-ttu-id="50bf3-139">Chcete-li vytvořit aplikaci Blazor používá model hostování na straně serveru, použijte ASP.NET Core **Blazor (serverové)** šablony ([nové blazorserverside dotnet](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="50bf3-139">To create a Blazor app using the server-side hosting model, use the ASP.NET Core **Blazor (server-side)** template ([dotnet new blazorserverside](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="50bf3-140">Aplikace ASP.NET Core hostitelem aplikace na straně serveru a nastaví koncových bodů SignalR, ve kterém se klienti připojují.</span><span class="sxs-lookup"><span data-stu-id="50bf3-140">The ASP.NET Core app hosts the server-side app and sets up the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="50bf3-141">Aplikace ASP.NET Core odkazuje aplikaci `Startup` třídy přidejte:</span><span class="sxs-lookup"><span data-stu-id="50bf3-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="50bf3-142">Služby na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="50bf3-142">Server-side services.</span></span>
* <span data-ttu-id="50bf3-143">Aplikace na žádost o zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="50bf3-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="50bf3-144">*Blazor.server.js* skript&dagger; naváže připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="50bf3-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="50bf3-145">Je zodpovědností aplikace k zachování a obnovení stavu aplikace podle potřeby (například v případě ztráty připojení).</span><span class="sxs-lookup"><span data-stu-id="50bf3-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="50bf3-146">Model hostingu na straně serveru nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="50bf3-146">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="50bf3-147">Má velikost výrazně menší aplikace, než aplikace na straně klienta a načte mnohem rychleji.</span><span class="sxs-lookup"><span data-stu-id="50bf3-147">Has a significantly smaller app size than a client-side app and loads much faster.</span></span>
* <span data-ttu-id="50bf3-148">Využití výhod funkce serveru, včetně použití libovolné rozhraní API .NET Core kompatibilní.</span><span class="sxs-lookup"><span data-stu-id="50bf3-148">Takes full advantage of server capabilities, including using any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="50bf3-149">V rozhraní .NET Core běží na serveru, takže existující .NET nástrojů, jako je ladění, funguje podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="50bf3-149">Runs on .NET Core on the server, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="50bf3-150">Funguje s tenké klienty.</span><span class="sxs-lookup"><span data-stu-id="50bf3-150">Works with thin clients.</span></span> <span data-ttu-id="50bf3-151">Například funguje v prohlížečích, které nepodporují WebAssembly a prostředků omezené zařízení.</span><span class="sxs-lookup"><span data-stu-id="50bf3-151">For example, works with browsers that don't support WebAssembly and resource constrained devices.</span></span>
* <span data-ttu-id="50bf3-152">.NET /C# základu kódu, včetně kódu komponenty aplikace, není poskytováni, na klienty.</span><span class="sxs-lookup"><span data-stu-id="50bf3-152">The .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="50bf3-153">Existují nevýhody hostování na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="50bf3-153">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="50bf3-154">Vyšší latence: Každá interakce uživatele zahrnuje směrování v síti.</span><span class="sxs-lookup"><span data-stu-id="50bf3-154">Higher latency: Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="50bf3-155">Žádná podpora pro offline: Pokud klienta nepovede, aplikace přestane fungovat.</span><span class="sxs-lookup"><span data-stu-id="50bf3-155">No offline support: If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="50bf3-156">Snížení škálovatelnosti: Server musí spravovat připojení více klientů a zpracování stavu klienta.</span><span class="sxs-lookup"><span data-stu-id="50bf3-156">Reduced scalability: The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="50bf3-157">Server služby ASP.NET Core je zapotřebí pro zpracování aplikace.</span><span class="sxs-lookup"><span data-stu-id="50bf3-157">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="50bf3-158">Nasazení bez serveru (například ze sítě CDN) není možné.</span><span class="sxs-lookup"><span data-stu-id="50bf3-158">Deployment without a server (for example, from a CDN) isn't possible.</span></span>

<span data-ttu-id="50bf3-159">&dagger;*Blazor.server.js* skript pochází z vloženého prostředku v rámci sdílené ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="50bf3-159">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="50bf3-160">Opětovné připojení ke stejnému serveru</span><span class="sxs-lookup"><span data-stu-id="50bf3-160">Reconnection to the same server</span></span>

<span data-ttu-id="50bf3-161">Blazor serverové aplikace vyžadují aktivní připojení k serveru funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="50bf3-161">Blazor server-side apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="50bf3-162">Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru.</span><span class="sxs-lookup"><span data-stu-id="50bf3-162">If a connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="50bf3-163">Za předpokladu, stav klienta je stále v paměti, bude pokračovat bez ztráty stavu relace klienta.</span><span class="sxs-lookup"><span data-stu-id="50bf3-163">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>
 
<span data-ttu-id="50bf3-164">Když klient zjistí, že připojení bylo ztraceno, je výchozí uživatelské rozhraní a klient se pokusí znovu připojit, zobrazit uživateli.</span><span class="sxs-lookup"><span data-stu-id="50bf3-164">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="50bf3-165">Pokud opětovného připojení selže, uživateli se nabídnou možnost opakovat.</span><span class="sxs-lookup"><span data-stu-id="50bf3-165">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="50bf3-166">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `components-reconnect-modal` jako jeho `id`.</span><span class="sxs-lookup"><span data-stu-id="50bf3-166">To customize the UI, define an element with `components-reconnect-modal` as its `id`.</span></span> <span data-ttu-id="50bf3-167">Klient aktualizuje tento element s jedním z následujících tříd šablon stylů CSS na základě stavu připojení:</span><span class="sxs-lookup"><span data-stu-id="50bf3-167">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>
 
* <span data-ttu-id="50bf3-168">`components-reconnect-show` &ndash; Zobrazit uživatelské rozhraní, které označíte, se ztratí připojení a klient se pokouší o obnovení připojení.</span><span class="sxs-lookup"><span data-stu-id="50bf3-168">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="50bf3-169">`components-reconnect-hide` &ndash; Klient má aktivní připojení, Skrýt uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="50bf3-169">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="50bf3-170">`components-reconnect-failed` &ndash; Opětovné připojení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="50bf3-170">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="50bf3-171">Pokusu o opětovné připojení znovu, volání `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="50bf3-171">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="50bf3-172">Stavové opětovného připojení po dokončení fáze před vykreslením</span><span class="sxs-lookup"><span data-stu-id="50bf3-172">Stateful reconnection after prerendering</span></span>
 
<span data-ttu-id="50bf3-173">Aplikace na straně serveru Blazor nastavení jsou ve výchozím nastavení prerender uživatelského rozhraní na serveru před navázáním připojení klienta k serveru.</span><span class="sxs-lookup"><span data-stu-id="50bf3-173">Blazor server-side apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="50bf3-174">Nastavuje se to *_Host.cshtml* stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="50bf3-174">This is set up in the *_Host.cshtml* Razor page:</span></span>
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
<span data-ttu-id="50bf3-175">Klient znovu připojí k serveru pomocí stejného stavu, který se použil k prerender aplikace.</span><span class="sxs-lookup"><span data-stu-id="50bf3-175">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="50bf3-176">Pokud se stav aplikace je stále v paměti, není stav komponenty rerendered po navázání připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="50bf3-176">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="50bf3-177">Vykreslení stavové interaktivní součásti ze stránky Razor a zobrazení</span><span class="sxs-lookup"><span data-stu-id="50bf3-177">Render stateful interactive components from Razor pages and views</span></span>
 
<span data-ttu-id="50bf3-178">Stavové interaktivní komponenty lze přidat stránky Razor nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="50bf3-178">Stateful interactive components can be added to a Razor page or view.</span></span> <span data-ttu-id="50bf3-179">Při vykreslení stránky nebo zobrazení, komponenta je předkreslených s ním.</span><span class="sxs-lookup"><span data-stu-id="50bf3-179">When the page or view renders, the component is prerendered with it.</span></span> <span data-ttu-id="50bf3-180">Aplikace potom se znovu připojí k stav komponenty po připojení klienta pokládáme stav, stav je stále v paměti.</span><span class="sxs-lookup"><span data-stu-id="50bf3-180">The app then reconnects to the component state once the client connection is established as long as the state is still in memory.</span></span>
 
<span data-ttu-id="50bf3-181">Například následující stránky Razor vykreslí součást čítače s počáteční počet, který je určen pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="50bf3-181">For example, the following Razor page renders a Counter component with an initial count that's specified using a form:</span></span>
 
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

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="50bf3-182">Rozpoznat, kdy aplikace je před vykreslením</span><span class="sxs-lookup"><span data-stu-id="50bf3-182">Detect when the app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a><span data-ttu-id="50bf3-183">Konfigurace klienta SignalR pro Blazor serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="50bf3-183">Configure the SignalR client for Blazor server-side apps</span></span>
 
<span data-ttu-id="50bf3-184">V některých případech budete muset nakonfigurovat klienta SignalR používat Blazor aplikace na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="50bf3-184">Sometimes, you need to configure the SignalR client used by Blazor server-side apps.</span></span> <span data-ttu-id="50bf3-185">Například můžete chtít konfigurovat protokolování na straně klienta SignalR pro diagnostiku problému připojení.</span><span class="sxs-lookup"><span data-stu-id="50bf3-185">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
 
<span data-ttu-id="50bf3-186">Abyste mohli nakonfigurovat klienta SignalR v *stránek /\_Host.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="50bf3-186">To configure the SignalR client in the *Pages/\_Host.cshtml* file:</span></span>

* <span data-ttu-id="50bf3-187">Přidat `autostart="false"` atribut `<script>` značky *blazor.server.js* skriptu.</span><span class="sxs-lookup"><span data-stu-id="50bf3-187">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="50bf3-188">Volání `Blazor.start` a předat objekt konfigurace, který určuje Tvůrce SignalR.</span><span class="sxs-lookup"><span data-stu-id="50bf3-188">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>
 
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

## <a name="additional-resources"></a><span data-ttu-id="50bf3-189">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="50bf3-189">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
