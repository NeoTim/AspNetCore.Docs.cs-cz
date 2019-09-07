---
title: ASP.NET Core modely hostování Blazor
author: guardrex
description: Pochopení Blazor modelů hostování na straně klienta a na straně serveru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/hosting-models
ms.openlocfilehash: f7a16d64e1f874a4f6b3c8db5217810b13c7c6ff
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800427"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="23ec4-103">ASP.NET Core modely hostování Blazor</span><span class="sxs-lookup"><span data-stu-id="23ec4-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="23ec4-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="23ec4-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="23ec4-105">Blazor je webová architektura navržená tak, aby běžela na straně klienta v prohlížeči na bázi .NET runtime založené na webovém [sestavení](https://webassembly.org/)(*Blazor na straně klienta*) nebo na straně serveru v ASP.NET Core (*Blazor Server*).</span><span class="sxs-lookup"><span data-stu-id="23ec4-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor client-side*) or server-side in ASP.NET Core (*Blazor server-side*).</span></span> <span data-ttu-id="23ec4-106">Bez ohledu na model hostování jsou modely aplikací a komponent *stejné*.</span><span class="sxs-lookup"><span data-stu-id="23ec4-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="23ec4-107">Chcete-li vytvořit projekt pro modely hostování popsané v tomto článku, přečtěte si téma <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="23ec4-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="client-side"></a><span data-ttu-id="23ec4-108">Na straně klienta</span><span class="sxs-lookup"><span data-stu-id="23ec4-108">Client-side</span></span>

<span data-ttu-id="23ec4-109">Hlavní hostující model pro Blazor je spuštěn na straně klienta v prohlížeči na webovém sestavení.</span><span class="sxs-lookup"><span data-stu-id="23ec4-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="23ec4-110">Do prohlížeče se stáhnou aplikace Blazor, její závislosti a modul runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="23ec4-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="23ec4-111">Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="23ec4-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="23ec4-112">Aktualizace uživatelského rozhraní a zpracování událostí se vyskytují v rámci stejného procesu.</span><span class="sxs-lookup"><span data-stu-id="23ec4-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="23ec4-113">Prostředky aplikace se nasazují jako statické soubory na webový server nebo službu, která je schopná obsluhovat statický obsah klientům.</span><span class="sxs-lookup"><span data-stu-id="23ec4-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor na straně klienta: Aplikace Blazor běží ve vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/client-side.png)

<span data-ttu-id="23ec4-115">Chcete-li vytvořit aplikaci Blazor pomocí modelu hostování na straně klienta, použijte šablonu **aplikace Blazor WebAssembly** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="23ec4-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="23ec4-116">Po výběru šablony **aplikace Blazor WebAssembly** máte možnost konfigurovat aplikaci tak, aby používala ASP.NET Core back-end, a to tak, že vyberete zaškrtávací políčko **ASP.NET Core hostované** ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="23ec4-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="23ec4-117">Aplikace ASP.NET Core obsluhuje aplikaci Blazor klientům.</span><span class="sxs-lookup"><span data-stu-id="23ec4-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="23ec4-118">Klientská aplikace Blazor může komunikovat se serverem přes síť pomocí volání webového rozhraní API nebo [signalizace](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="23ec4-118">The Blazor client-side app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="23ec4-119">Šablony obsahují skript *blazor. WebAssembly. js* , který zpracovává:</span><span class="sxs-lookup"><span data-stu-id="23ec4-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="23ec4-120">Stažení modulu runtime .NET, aplikace a závislostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="23ec4-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="23ec4-121">Inicializace modulu runtime pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="23ec4-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="23ec4-122">Model hostování na straně klienta nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="23ec4-122">The client-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="23ec4-123">Neexistuje žádná závislost na straně serveru .NET.</span><span class="sxs-lookup"><span data-stu-id="23ec4-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="23ec4-124">Aplikace po stažení do klienta plně funguje.</span><span class="sxs-lookup"><span data-stu-id="23ec4-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="23ec4-125">Prostředky a možnosti klienta jsou plně využité.</span><span class="sxs-lookup"><span data-stu-id="23ec4-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="23ec4-126">Práce je ze serveru převedena na klienta.</span><span class="sxs-lookup"><span data-stu-id="23ec4-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="23ec4-127">Pro hostování aplikace není vyžadován ASP.NET Core webový server.</span><span class="sxs-lookup"><span data-stu-id="23ec4-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="23ec4-128">Jsou možné scénáře nasazení bez serveru (například poskytování aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="23ec4-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="23ec4-129">Downsides hostování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="23ec4-129">There are downsides to client-side hosting:</span></span>

* <span data-ttu-id="23ec4-130">Aplikace je omezená na možnosti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="23ec4-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="23ec4-131">Je vyžadován klientský hardware a software (například podpora WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="23ec4-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="23ec4-132">Velikost ke stažení je větší a aplikace trvá déle, než se načtou.</span><span class="sxs-lookup"><span data-stu-id="23ec4-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="23ec4-133">Podpora modulu runtime .NET a nástrojů je méně vyspělá.</span><span class="sxs-lookup"><span data-stu-id="23ec4-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="23ec4-134">Například omezení existují v [.NET Standard](/dotnet/standard/net-standard) podpoře a ladění.</span><span class="sxs-lookup"><span data-stu-id="23ec4-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="server-side"></a><span data-ttu-id="23ec4-135">Na straně serveru</span><span class="sxs-lookup"><span data-stu-id="23ec4-135">Server-side</span></span>

<span data-ttu-id="23ec4-136">S modelem hostování na straně serveru se aplikace spouští na serveru z aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="23ec4-136">With the server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="23ec4-137">Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes připojení k [signalizaci](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="23ec4-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![Prohlížeč komunikuje s aplikací (hostovanou v aplikaci ASP.NET Core) na serveru přes připojení k signalizaci.](hosting-models/_static/server-side.png)

<span data-ttu-id="23ec4-139">K vytvoření aplikace Blazor pomocí modelu hostování na straně serveru použijte šablonu **aplikace serveru** pro ASP.NET Core Blazor ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="23ec4-139">To create a Blazor app using the server-side hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="23ec4-140">Aplikace ASP.NET Core hostuje serverovou aplikaci a vytvoří koncový bod signalizace, kde se klienti připojují.</span><span class="sxs-lookup"><span data-stu-id="23ec4-140">The ASP.NET Core app hosts the server-side app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="23ec4-141">Aplikace ASP.NET Core odkazuje na `Startup` třídu aplikace, která se má přidat:</span><span class="sxs-lookup"><span data-stu-id="23ec4-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="23ec4-142">Služby na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="23ec4-142">Server-side services.</span></span>
* <span data-ttu-id="23ec4-143">Aplikace do kanálu pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="23ec4-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="23ec4-144">Skript&dagger; *blazor. Server. js* vytvoří připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="23ec4-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="23ec4-145">Je zodpovědností aplikace zachovat a obnovit stav aplikace podle potřeby (například v případě ztraceného síťového připojení).</span><span class="sxs-lookup"><span data-stu-id="23ec4-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="23ec4-146">Model hostování na straně serveru nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="23ec4-146">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="23ec4-147">Velikost ke stažení je výrazně menší než aplikace na straně klienta a aplikace se načítá mnohem rychleji.</span><span class="sxs-lookup"><span data-stu-id="23ec4-147">Download size is significantly smaller than a client-side app, and the app loads much faster.</span></span>
* <span data-ttu-id="23ec4-148">Aplikace plně využívá možnosti serveru, včetně použití všech rozhraní API kompatibilních s rozhraním .NET Core.</span><span class="sxs-lookup"><span data-stu-id="23ec4-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="23ec4-149">Rozhraní .NET Core na serveru se používá ke spuštění aplikace, takže stávající nástroje .NET, jako je ladění, fungují podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="23ec4-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="23ec4-150">Podporují se tenké klienty.</span><span class="sxs-lookup"><span data-stu-id="23ec4-150">Thin clients are supported.</span></span> <span data-ttu-id="23ec4-151">Například aplikace na straně serveru fungují s prohlížeči, které nepodporují WebAssembly a na zařízeních s omezením prostředků.</span><span class="sxs-lookup"><span data-stu-id="23ec4-151">For example, server-side apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="23ec4-152">Základ pro .NET/C# kód aplikace, včetně kódu komponenty aplikace, není obsluhován klientům.</span><span class="sxs-lookup"><span data-stu-id="23ec4-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="23ec4-153">Existuje downsides hostování na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="23ec4-153">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="23ec4-154">Vyšší latence obvykle existuje.</span><span class="sxs-lookup"><span data-stu-id="23ec4-154">Higher latency usually exists.</span></span> <span data-ttu-id="23ec4-155">Každá interakce uživatele zahrnuje směrování sítě.</span><span class="sxs-lookup"><span data-stu-id="23ec4-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="23ec4-156">Neexistuje žádná podpora offline.</span><span class="sxs-lookup"><span data-stu-id="23ec4-156">There's no offline support.</span></span> <span data-ttu-id="23ec4-157">Pokud připojení klienta neproběhne úspěšně, aplikace přestane fungovat.</span><span class="sxs-lookup"><span data-stu-id="23ec4-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="23ec4-158">Pro aplikace s mnoha uživateli je škálovatelnost náročná.</span><span class="sxs-lookup"><span data-stu-id="23ec4-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="23ec4-159">Server musí spravovat více připojení klientů a zpracovávat stav klienta.</span><span class="sxs-lookup"><span data-stu-id="23ec4-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="23ec4-160">Pro obsluhu aplikace je vyžadován ASP.NET Core Server.</span><span class="sxs-lookup"><span data-stu-id="23ec4-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="23ec4-161">Scénáře nasazení bez serveru nejsou možné (například poskytování aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="23ec4-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="23ec4-162">&dagger;Skript *blazor. Server. js* se obsluhuje z vloženého prostředku v ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="23ec4-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="23ec4-163">Opětovné připojení ke stejnému serveru</span><span class="sxs-lookup"><span data-stu-id="23ec4-163">Reconnection to the same server</span></span>

<span data-ttu-id="23ec4-164">Blazor aplikace na straně serveru vyžadují, aby se k serveru mohl připojit aktivní signál.</span><span class="sxs-lookup"><span data-stu-id="23ec4-164">Blazor server-side apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="23ec4-165">Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru.</span><span class="sxs-lookup"><span data-stu-id="23ec4-165">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="23ec4-166">Pokud je stav klienta stále v paměti, klientská relace pokračuje bez ztráty stavu.</span><span class="sxs-lookup"><span data-stu-id="23ec4-166">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>
 
<span data-ttu-id="23ec4-167">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="23ec4-167">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="23ec4-168">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="23ec4-168">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="23ec4-169">Chcete-li přizpůsobit uživatelské rozhraní, definujte element `components-reconnect-modal` s jako `id` svůj na stránce *_Host. cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="23ec4-169">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="23ec4-170">Klient aktualizuje tento prvek pomocí jedné z následujících tříd šablony stylů CSS na základě stavu připojení:</span><span class="sxs-lookup"><span data-stu-id="23ec4-170">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>
 
* <span data-ttu-id="23ec4-171">`components-reconnect-show`&ndash; Zobrazit uživatelské rozhraní, které indikuje, že připojení bylo ztraceno a klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="23ec4-171">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="23ec4-172">`components-reconnect-hide`&ndash; Klient má aktivní připojení, skryje uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="23ec4-172">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="23ec4-173">`components-reconnect-failed`&ndash; Opětovné připojení se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="23ec4-173">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="23ec4-174">Chcete-li se znovu pokusit `window.Blazor.reconnect()`o opětovné připojení, zavolejte.</span><span class="sxs-lookup"><span data-stu-id="23ec4-174">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="23ec4-175">Stav opětovného připojení po předvykreslování</span><span class="sxs-lookup"><span data-stu-id="23ec4-175">Stateful reconnection after prerendering</span></span>
 
<span data-ttu-id="23ec4-176">Blazor aplikace na straně serveru jsou nastavené ve výchozím nastavení tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="23ec4-176">Blazor server-side apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="23ec4-177">To je nastavené na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="23ec4-177">This is set up in the *_Host.cshtml* Razor page:</span></span>
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="23ec4-178">`RenderMode`nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="23ec4-178">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="23ec4-179">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="23ec4-179">Is prerendered into the page.</span></span>
* <span data-ttu-id="23ec4-180">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="23ec4-180">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="23ec4-181">Popis</span><span class="sxs-lookup"><span data-stu-id="23ec4-181">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="23ec4-182">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor aplikaci na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="23ec4-182">Renders the component into static HTML and includes a marker for a Blazor server-side app.</span></span> <span data-ttu-id="23ec4-183">Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor.</span><span class="sxs-lookup"><span data-stu-id="23ec4-183">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="23ec4-184">Parametry nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="23ec4-184">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="23ec4-185">Vykreslí značku pro Blazor aplikaci na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="23ec4-185">Renders a marker for a Blazor server-side app.</span></span> <span data-ttu-id="23ec4-186">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="23ec4-186">Output from the component isn't included.</span></span> <span data-ttu-id="23ec4-187">Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor.</span><span class="sxs-lookup"><span data-stu-id="23ec4-187">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="23ec4-188">Parametry nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="23ec4-188">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="23ec4-189">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="23ec4-189">Renders the component into static HTML.</span></span> <span data-ttu-id="23ec4-190">Jsou podporovány parametry.</span><span class="sxs-lookup"><span data-stu-id="23ec4-190">Parameters are supported.</span></span> |

<span data-ttu-id="23ec4-191">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="23ec4-191">Rendering server components from a static HTML page isn't supported.</span></span>
 
<span data-ttu-id="23ec4-192">Klient se znovu připojí k serveru se stejným stavem, který se použil k proprerender aplikace.</span><span class="sxs-lookup"><span data-stu-id="23ec4-192">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="23ec4-193">Pokud je stav aplikace stále v paměti, stav součásti nebude po navázání připojení k signalizaci znovu vykreslen.</span><span class="sxs-lookup"><span data-stu-id="23ec4-193">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="23ec4-194">Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="23ec4-194">Render stateful interactive components from Razor pages and views</span></span>
 
<span data-ttu-id="23ec4-195">Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="23ec4-195">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="23ec4-196">Při vykreslení stránky nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="23ec4-196">When the page or view renders:</span></span>

* <span data-ttu-id="23ec4-197">Komponenta je předem vykreslena se stránkou nebo zobrazením.</span><span class="sxs-lookup"><span data-stu-id="23ec4-197">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="23ec4-198">Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="23ec4-198">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="23ec4-199">Po navázání připojení k signalizaci se vytvoří nový stav součásti.</span><span class="sxs-lookup"><span data-stu-id="23ec4-199">New component state is created when the SignalR connection is established.</span></span>
 
<span data-ttu-id="23ec4-200">Následující stránka Razor vykresluje `Counter` komponentu:</span><span class="sxs-lookup"><span data-stu-id="23ec4-200">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>
 
@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="23ec4-201">Vykreslování neinteraktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="23ec4-201">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="23ec4-202">Na následující stránce `MyComponent` Razor je komponenta staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="23ec4-202">In the following Razor page, the `MyComponent` component is statically rendered with an initial value that's specified using a form:</span></span>
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>
 
@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="23ec4-203">Vzhledem `MyComponent` k tomu, že se staticky vykreslují, komponenta nemůže být interaktivní.</span><span class="sxs-lookup"><span data-stu-id="23ec4-203">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="23ec4-204">Rozpoznat, kdy se aplikace předvykresluje</span><span class="sxs-lookup"><span data-stu-id="23ec4-204">Detect when the app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a><span data-ttu-id="23ec4-205">Konfigurace klienta nástroje Signal pro aplikace Blazor na straně serveru</span><span class="sxs-lookup"><span data-stu-id="23ec4-205">Configure the SignalR client for Blazor server-side apps</span></span>
 
<span data-ttu-id="23ec4-206">V některých případech je třeba nakonfigurovat klienta nástroje Signal používaného Blazor aplikacemi na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="23ec4-206">Sometimes, you need to configure the SignalR client used by Blazor server-side apps.</span></span> <span data-ttu-id="23ec4-207">Například můžete chtít nakonfigurovat protokolování na straně klienta signalizace, aby bylo možné diagnostikovat problém s připojením.</span><span class="sxs-lookup"><span data-stu-id="23ec4-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
 
<span data-ttu-id="23ec4-208">Konfigurace klienta signalizace v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="23ec4-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="23ec4-209">Přidejte atribut do značky pro skript *blazor. Server. js.* `<script>` `autostart="false"`</span><span class="sxs-lookup"><span data-stu-id="23ec4-209">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="23ec4-210">Zavolejte `Blazor.start` a předejte do konfiguračního objektu, který určuje tvůrce signálu.</span><span class="sxs-lookup"><span data-stu-id="23ec4-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>
 
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

## <a name="additional-resources"></a><span data-ttu-id="23ec4-211">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="23ec4-211">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
