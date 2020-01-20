---
title: Modely hostování ASP.NET Core Blazor
author: guardrex
description: Pochopení Blazor modelů hostování serverů a Blazor serveru.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
- blazor.webassembly.js
uid: blazor/hosting-models
ms.openlocfilehash: c9521acf40317c90d1197660bfa516710263cfc9
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160038"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a><span data-ttu-id="aaea3-103">Modely hostování ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="aaea3-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="aaea3-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="aaea3-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="aaea3-105"> je webová architektura navržená tak, aby na straně klienta běžela na klientském počítači .NET runtime založeném na webovém [sestavení](https://webassembly.org/)( *Blazor webové sestavení*) nebo na straně serveru v ASP.NET Core ( *ServerBlazor* ).</span><span class="sxs-lookup"><span data-stu-id="aaea3-105"> is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="aaea3-106">Bez ohledu na model hostování jsou modely aplikací a komponent *stejné*.</span><span class="sxs-lookup"><span data-stu-id="aaea3-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="aaea3-107">Chcete-li vytvořit projekt pro modely hostování popsané v tomto článku, přečtěte si téma <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="aaea3-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="aaea3-108"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="aaea3-108"> WebAssembly</span></span>

<span data-ttu-id="aaea3-109">Hlavní hostující model pro Blazor spouští klientskou stranu v prohlížeči na webovém sestavení.</span><span class="sxs-lookup"><span data-stu-id="aaea3-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="aaea3-110">Aplikace Blazor, její závislosti a modul runtime .NET se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="aaea3-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="aaea3-111">Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="aaea3-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="aaea3-112">Aktualizace uživatelského rozhraní a zpracování událostí se vyskytují v rámci stejného procesu.</span><span class="sxs-lookup"><span data-stu-id="aaea3-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="aaea3-113">Prostředky aplikace se nasazují jako statické soubory na webový server nebo službu, která je schopná obsluhovat statický obsah klientům.</span><span class="sxs-lookup"><span data-stu-id="aaea3-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![[! Evřít. Rozhraní Blazor (NO-LOC)]<span data-ttu-id="aaea3-114"> WebAssembly: [! Evřít. Aplikace NO-LOC (Blazor)] běží ve vlákně uživatelského rozhraní v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="aaea3-114"> WebAssembly: The Blazor app runs on a UI thread inside the browser.</span></span>](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="aaea3-115">Chcete-li vytvořit aplikaci Blazor pomocí modelu hostování na straně klienta, použijte šablonu **aplikaceBlazor WebAssembly** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="aaea3-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="aaea3-116">Po výběru šablony **aplikaceBlazor WebAssembly** máte možnost konfigurovat aplikaci tak, aby používala ASP.NET Core back-end, a to tak, že vyberete zaškrtávací políčko **ASP.NET Core hostované** ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="aaea3-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="aaea3-117">Aplikace ASP.NET Core obsluhuje aplikaci Blazor klientům.</span><span class="sxs-lookup"><span data-stu-id="aaea3-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="aaea3-118">Aplikace Blazor WebAssembly může komunikovat se serverem přes síť pomocí volání webového rozhraní API nebo [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="aaea3-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="aaea3-119">Šablony obsahují skript `blazor.webassembly.js`, který zpracovává:</span><span class="sxs-lookup"><span data-stu-id="aaea3-119">The templates include the `blazor.webassembly.js` script that handles:</span></span>

* <span data-ttu-id="aaea3-120">Stažení modulu runtime .NET, aplikace a závislostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="aaea3-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="aaea3-121">Inicializace modulu runtime pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="aaea3-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="aaea3-122">Model hostování Blazor WebAssembly nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="aaea3-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="aaea3-123">Neexistuje žádná závislost na straně serveru .NET.</span><span class="sxs-lookup"><span data-stu-id="aaea3-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="aaea3-124">Aplikace po stažení do klienta plně funguje.</span><span class="sxs-lookup"><span data-stu-id="aaea3-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="aaea3-125">Prostředky a možnosti klienta jsou plně využité.</span><span class="sxs-lookup"><span data-stu-id="aaea3-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="aaea3-126">Práce je ze serveru převedena na klienta.</span><span class="sxs-lookup"><span data-stu-id="aaea3-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="aaea3-127">Pro hostování aplikace není vyžadován ASP.NET Core webový server.</span><span class="sxs-lookup"><span data-stu-id="aaea3-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="aaea3-128">Jsou možné scénáře nasazení bez serveru (například poskytování aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="aaea3-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="aaea3-129">K Blazor hostování WebAssembly je downsides:</span><span class="sxs-lookup"><span data-stu-id="aaea3-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="aaea3-130">Aplikace je omezená na možnosti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="aaea3-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="aaea3-131">Je vyžadován klientský hardware a software (například podpora WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="aaea3-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="aaea3-132">Velikost ke stažení je větší a aplikace trvá déle, než se načtou.</span><span class="sxs-lookup"><span data-stu-id="aaea3-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="aaea3-133">Podpora modulu runtime .NET a nástrojů je méně vyspělá.</span><span class="sxs-lookup"><span data-stu-id="aaea3-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="aaea3-134">Například omezení existují v [.NET Standard](/dotnet/standard/net-standard) podpoře a ladění.</span><span class="sxs-lookup"><span data-stu-id="aaea3-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="opno-locblazor-server"></a><span data-ttu-id="aaea3-135">Server Blazor</span><span class="sxs-lookup"><span data-stu-id="aaea3-135">Blazor Server</span></span>

<span data-ttu-id="aaea3-136">S modelem hostování serveru Blazor se aplikace spouští na serveru z aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aaea3-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="aaea3-137">Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu jsou zpracovávána prostřednictvím [SignalRho](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="aaea3-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![Prohlížeč komunikuje s aplikací (hostovanou v aplikaci ASP.NET Core) na serveru přes [! Evřít. Připojení NO-LOC (Signal)]](hosting-models/_static/blazor-server.png)

<span data-ttu-id="aaea3-139">Pokud chcete vytvořit aplikaci Blazor pomocí modelu hostování Blazor serveru, použijte šablonu aplikace ASP.NET Core **Blazor Server** ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="aaea3-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="aaea3-140">Aplikace ASP.NET Core hostuje aplikaci Blazor serveru a vytvoří SignalR koncový bod, ke kterému se klienti připojují.</span><span class="sxs-lookup"><span data-stu-id="aaea3-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="aaea3-141">ASP.NET Core aplikace odkazuje na třídu `Startup` aplikace, která se má přidat:</span><span class="sxs-lookup"><span data-stu-id="aaea3-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="aaea3-142">Služby na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="aaea3-142">Server-side services.</span></span>
* <span data-ttu-id="aaea3-143">Aplikace do kanálu pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="aaea3-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="aaea3-144">Skript `blazor.server.js`&dagger; vytvoří připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="aaea3-144">The `blazor.server.js` script&dagger; establishes the client connection.</span></span> <span data-ttu-id="aaea3-145">Je zodpovědností aplikace zachovat a obnovit stav aplikace podle potřeby (například v případě ztraceného síťového připojení).</span><span class="sxs-lookup"><span data-stu-id="aaea3-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="aaea3-146">Model hostování serveru Blazor nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="aaea3-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="aaea3-147">Velikost ke stažení je výrazně menší než Blazor aplikace WebAssembly a aplikace se načte mnohem rychleji.</span><span class="sxs-lookup"><span data-stu-id="aaea3-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="aaea3-148">Aplikace plně využívá možnosti serveru, včetně použití všech rozhraní API kompatibilních s rozhraním .NET Core.</span><span class="sxs-lookup"><span data-stu-id="aaea3-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="aaea3-149">Rozhraní .NET Core na serveru se používá ke spuštění aplikace, takže stávající nástroje .NET, jako je ladění, fungují podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="aaea3-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="aaea3-150">Podporují se tenké klienty.</span><span class="sxs-lookup"><span data-stu-id="aaea3-150">Thin clients are supported.</span></span> <span data-ttu-id="aaea3-151">Například Blazor serverové aplikace fungují s prohlížeči, které nepodporují WebAssembly a na zařízeních s omezením prostředků.</span><span class="sxs-lookup"><span data-stu-id="aaea3-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="aaea3-152">Základ pro .NET/C# kód aplikace, včetně kódu komponenty aplikace, není obsluhován klientům.</span><span class="sxs-lookup"><span data-stu-id="aaea3-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="aaea3-153">K dispozici je downsides Blazor hostování serveru:</span><span class="sxs-lookup"><span data-stu-id="aaea3-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="aaea3-154">Vyšší latence obvykle existuje.</span><span class="sxs-lookup"><span data-stu-id="aaea3-154">Higher latency usually exists.</span></span> <span data-ttu-id="aaea3-155">Každá interakce uživatele zahrnuje směrování sítě.</span><span class="sxs-lookup"><span data-stu-id="aaea3-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="aaea3-156">Neexistuje žádná podpora offline.</span><span class="sxs-lookup"><span data-stu-id="aaea3-156">There's no offline support.</span></span> <span data-ttu-id="aaea3-157">Pokud připojení klienta neproběhne úspěšně, aplikace přestane fungovat.</span><span class="sxs-lookup"><span data-stu-id="aaea3-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="aaea3-158">Pro aplikace s mnoha uživateli je škálovatelnost náročná.</span><span class="sxs-lookup"><span data-stu-id="aaea3-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="aaea3-159">Server musí spravovat více připojení klientů a zpracovávat stav klienta.</span><span class="sxs-lookup"><span data-stu-id="aaea3-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="aaea3-160">Pro obsluhu aplikace je vyžadován ASP.NET Core Server.</span><span class="sxs-lookup"><span data-stu-id="aaea3-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="aaea3-161">Scénáře nasazení bez serveru nejsou možné (například poskytování aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="aaea3-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="aaea3-162">&dagger;`blazor.server.js` skript se obsluhuje z vloženého prostředku ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="aaea3-162">&dagger;The `blazor.server.js` script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="aaea3-163">Porovnání s uživatelským rozhraním vykresleným serverem</span><span class="sxs-lookup"><span data-stu-id="aaea3-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="aaea3-164">Jedním ze způsobů, jak pochopit Blazor serverových aplikací, je pochopit, jak se liší od tradičních modelů pro vykreslování uživatelského rozhraní v ASP.NET Core aplikacích, které používají zobrazení Razor nebo Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="aaea3-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="aaea3-165">Oba modely používají jazyk Razor k popisu obsahu HTML, ale významně se liší v tom, jak se vykreslují kód.</span><span class="sxs-lookup"><span data-stu-id="aaea3-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="aaea3-166">Když je vykreslena stránka nebo zobrazení Razor, každý řádek kódu Razor generuje kód HTML v textovém formátu.</span><span class="sxs-lookup"><span data-stu-id="aaea3-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="aaea3-167">Po vykreslení Server uvolní instanci stránky nebo zobrazení, včetně veškerého stavu, který byl vytvořen.</span><span class="sxs-lookup"><span data-stu-id="aaea3-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="aaea3-168">Pokud dojde k jiné žádosti na stránku, například když se ověření serveru nepovede a zobrazí se shrnutí ověření:</span><span class="sxs-lookup"><span data-stu-id="aaea3-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="aaea3-169">Celá stránka se znovu vykreslí do textu HTML.</span><span class="sxs-lookup"><span data-stu-id="aaea3-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="aaea3-170">Stránka je odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="aaea3-170">The page is sent to the client.</span></span>

<span data-ttu-id="aaea3-171">Blazor aplikace se skládá z opakovaně použitelných prvků uživatelského rozhraní s názvem *Components*.</span><span class="sxs-lookup"><span data-stu-id="aaea3-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="aaea3-172">Komponenta obsahuje C# kód, značku a další komponenty.</span><span class="sxs-lookup"><span data-stu-id="aaea3-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="aaea3-173">Když je vykreslena komponenta, Blazor vytvoří graf zahrnutých součástí, podobně jako HTML nebo XML model DOM (Document Object Model) (DOM).</span><span class="sxs-lookup"><span data-stu-id="aaea3-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="aaea3-174">Tento graf obsahuje stav součásti uložený ve vlastnostech a polích.</span><span class="sxs-lookup"><span data-stu-id="aaea3-174">This graph includes component state held in properties and fields.</span></span> Blazor<span data-ttu-id="aaea3-175"> vyhodnotí graf komponent pro vytvoření binární reprezentace kódu.</span><span class="sxs-lookup"><span data-stu-id="aaea3-175"> evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="aaea3-176">Binární formát může být:</span><span class="sxs-lookup"><span data-stu-id="aaea3-176">The binary format can be:</span></span>

* <span data-ttu-id="aaea3-177">Byl převeden na text HTML (během předvykreslování).</span><span class="sxs-lookup"><span data-stu-id="aaea3-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="aaea3-178">Slouží k efektivní aktualizaci značek během pravidelného vykreslování.</span><span class="sxs-lookup"><span data-stu-id="aaea3-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="aaea3-179">Aktualizace uživatelského rozhraní v Blazor je aktivována pomocí:</span><span class="sxs-lookup"><span data-stu-id="aaea3-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="aaea3-180">Interakce s uživatelem, například výběr tlačítka.</span><span class="sxs-lookup"><span data-stu-id="aaea3-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="aaea3-181">Aktivační události aplikace, jako je například časovač.</span><span class="sxs-lookup"><span data-stu-id="aaea3-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="aaea3-182">Graf se znovu vykreslí a počítá se *rozdíl* v uživatelském rozhraní (rozdíl).</span><span class="sxs-lookup"><span data-stu-id="aaea3-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="aaea3-183">Tento rozdíl je nejmenší sada úprav modelu DOM, která je nutná k aktualizaci uživatelského rozhraní na klientovi.</span><span class="sxs-lookup"><span data-stu-id="aaea3-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="aaea3-184">Rozdíl se pošle klientovi v binárním formátu a použije ho prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="aaea3-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="aaea3-185">Komponenta je uvolněna poté, co uživatel z něj přejde na klienta.</span><span class="sxs-lookup"><span data-stu-id="aaea3-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="aaea3-186">I když uživatel pracuje s komponentou, musí se stav komponenty (služby, prostředky) uchovávat v paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="aaea3-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="aaea3-187">Vzhledem k tomu, že stav mnoha součástí může být serverem současně udržován, vyčerpání paměti je problém, který je třeba řešit.</span><span class="sxs-lookup"><span data-stu-id="aaea3-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="aaea3-188">Pokyny k vytvoření aplikace Blazor serveru pro zajištění nejlepšího využití paměti serveru najdete v tématu <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="aaea3-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="aaea3-189">Integrace součástí Razor do aplikací Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="aaea3-189">Integrate Razor components into Razor Pages and MVC apps</span></span>

#### <a name="use-components-in-pages-and-views"></a><span data-ttu-id="aaea3-190">Použití součástí na stránkách a pohledech</span><span class="sxs-lookup"><span data-stu-id="aaea3-190">Use components in pages and views</span></span>

<span data-ttu-id="aaea3-191">Existující Razor Pages nebo aplikace MVC mohou integrovat součásti Razor do stránek a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="aaea3-191">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="aaea3-192">V souboru rozložení aplikace ( *_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="aaea3-192">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="aaea3-193">Přidejte následující značku `<base>` do prvku `<head>`:</span><span class="sxs-lookup"><span data-stu-id="aaea3-193">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="aaea3-194">Hodnota `href` ( *základní cesta aplikace*) v předchozím příkladu předpokládá, že se aplikace nachází v kořenové cestě URL (`/`).</span><span class="sxs-lookup"><span data-stu-id="aaea3-194">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="aaea3-195">Pokud je aplikace podaplikace, postupujte podle pokynů v části *základní cesta k aplikaci* v <xref:host-and-deploy/blazor/index#app-base-path> článku.</span><span class="sxs-lookup"><span data-stu-id="aaea3-195">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="aaea3-196">Soubor *_Layout. cshtml* se nachází ve složce *stránky nebo sdílené* složky v aplikaci Razor Pages nebo *zobrazení/sdílená* složka v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="aaea3-196">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="aaea3-197">Přidejte značku `<script>` pro skript *blazor. Server. js* uvnitř uzavírací značky `</body>`:</span><span class="sxs-lookup"><span data-stu-id="aaea3-197">Add a `<script>` tag for the *blazor.server.js* script inside of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="aaea3-198">Rozhraní přidá do aplikace skript *blazor. Server. js* .</span><span class="sxs-lookup"><span data-stu-id="aaea3-198">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="aaea3-199">Nemusíte ručně přidávat do aplikace skript.</span><span class="sxs-lookup"><span data-stu-id="aaea3-199">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="aaea3-200">Přidejte soubor *_Imports. Razor* do kořenové složky projektu s následujícím obsahem (změňte poslední obor názvů `MyAppNamespace`na obor názvů aplikace):</span><span class="sxs-lookup"><span data-stu-id="aaea3-200">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="aaea3-201">V `Startup.ConfigureServices`přidejte službu Blazor serveru:</span><span class="sxs-lookup"><span data-stu-id="aaea3-201">In `Startup.ConfigureServices`, add the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="aaea3-202">V `Startup.Configure`přidejte koncový bod centra Blazor do `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="aaea3-202">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="aaea3-203">Integrujte součásti na libovolnou stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="aaea3-203">Integrate components into any page or view.</span></span> <span data-ttu-id="aaea3-204">Další informace najdete v části *integrace součástí do Razor Pages a MVC Apps* v článku <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="aaea3-204">For more information, see the *Integrate components into Razor Pages and MVC apps* section of the <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> article.</span></span>

#### <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="aaea3-205">Použití směrovatelných komponent v aplikaci Razor Pages</span><span class="sxs-lookup"><span data-stu-id="aaea3-205">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="aaea3-206">Podpora směrování komponent s více prvky Razor v aplikacích Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="aaea3-206">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="aaea3-207">Postupujte podle pokynů v části [použití součástí v části stránky a zobrazení](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="aaea3-207">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="aaea3-208">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="aaea3-208">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="aaea3-209">Do složky *Pages* přidejte *_Host soubor. cshtml* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="aaea3-209">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="aaea3-210">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="aaea3-210">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="aaea3-211">Přidejte trasu s nízkou prioritou pro stránku *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="aaea3-211">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="aaea3-212">Přidejte do aplikace směrovatelné součásti.</span><span class="sxs-lookup"><span data-stu-id="aaea3-212">Add routable components to the app.</span></span> <span data-ttu-id="aaea3-213">Příklad:</span><span class="sxs-lookup"><span data-stu-id="aaea3-213">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="aaea3-214">Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do souboru *Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="aaea3-214">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Pages/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="aaea3-215">Další informace najdete v tématu <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="aaea3-215">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

#### <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="aaea3-216">Použití směrovatelných komponent v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="aaea3-216">Use routable components in an MVC app</span></span>

<span data-ttu-id="aaea3-217">Podpora směrovatelných komponent Razor v aplikacích MVC:</span><span class="sxs-lookup"><span data-stu-id="aaea3-217">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="aaea3-218">Postupujte podle pokynů v části [použití součástí v části stránky a zobrazení](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="aaea3-218">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="aaea3-219">Přidejte soubor *App. Razor* do kořenového adresáře projektu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="aaea3-219">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="aaea3-220">Přidejte *_Host soubor. cshtml* do *zobrazení/domovské* složky s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="aaea3-220">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="aaea3-221">Komponenty používají pro své rozložení sdílený *_Layout soubor. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="aaea3-221">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="aaea3-222">Přidat akci do domovského kontroleru:</span><span class="sxs-lookup"><span data-stu-id="aaea3-222">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="aaea3-223">Přidejte trasu s nízkou prioritou pro akci kontroleru, která vrací zobrazení *_Host. cshtml* do konfigurace koncového bodu v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="aaea3-223">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="aaea3-224">Vytvořte složku *Pages* a přidejte do ní součásti s funkcí směrování.</span><span class="sxs-lookup"><span data-stu-id="aaea3-224">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="aaea3-225">Příklad:</span><span class="sxs-lookup"><span data-stu-id="aaea3-225">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="aaea3-226">Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do souboru *views/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="aaea3-226">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="aaea3-227">Další informace najdete v tématu <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="aaea3-227">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

### <a name="circuits"></a><span data-ttu-id="aaea3-228">Spoj</span><span class="sxs-lookup"><span data-stu-id="aaea3-228">Circuits</span></span>

<span data-ttu-id="aaea3-229">Aplikace Blazor serveru je postavená nad [ASP.NET Core SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="aaea3-229">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="aaea3-230">Každý klient komunikuje se serverem přes jedno nebo více SignalR připojení s názvem *okruh*.</span><span class="sxs-lookup"><span data-stu-id="aaea3-230">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="aaea3-231">Okruh je Blazorabstrakce prostřednictvím SignalR připojení, která mohou tolerovat dočasné síťové přerušení.</span><span class="sxs-lookup"><span data-stu-id="aaea3-231">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="aaea3-232">Když klient Blazor uvidí, že připojení SignalR je odpojené, pokusí se znovu připojit k serveru pomocí nového SignalR připojení.</span><span class="sxs-lookup"><span data-stu-id="aaea3-232">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="aaea3-233">Každá obrazovka prohlížeče (karta prohlížeče nebo IFRAME), která je připojená k aplikaci Blazor serveru, používá SignalR připojení.</span><span class="sxs-lookup"><span data-stu-id="aaea3-233">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="aaea3-234">Toto je ještě další důležité rozdíly ve srovnání s typickými aplikacemi vygenerovanými serverem.</span><span class="sxs-lookup"><span data-stu-id="aaea3-234">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="aaea3-235">V aplikaci vykreslené serverem se při otevření stejné aplikace v několika obrazovkách prohlížeče obvykle nepřevádí na další požadavky na prostředky na serveru.</span><span class="sxs-lookup"><span data-stu-id="aaea3-235">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="aaea3-236">V aplikaci Blazor serveru vyžaduje Každá obrazovka prohlížeče samostatný okruh a samostatné instance stavu součásti, které má server spravovat.</span><span class="sxs-lookup"><span data-stu-id="aaea3-236">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

Blazor<span data-ttu-id="aaea3-237"> se domnívá zavřít kartu prohlížeče nebo přejít na externí adresu URL s *řádným* ukončením.</span><span class="sxs-lookup"><span data-stu-id="aaea3-237"> considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="aaea3-238">V případě řádného ukončení se okruh a přidružené prostředky ihned uvolňují.</span><span class="sxs-lookup"><span data-stu-id="aaea3-238">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="aaea3-239">Klient se může také bez problémů odpojit, například kvůli přerušení sítě.</span><span class="sxs-lookup"><span data-stu-id="aaea3-239">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> Blazor<span data-ttu-id="aaea3-240"> server ukládá odpojené okruhy po Konfigurovatelný interval, aby se klient mohl znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="aaea3-240"> Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="aaea3-241">Další informace najdete v části [opětovné připojení ke stejnému serveru](#reconnection-to-the-same-server) .</span><span class="sxs-lookup"><span data-stu-id="aaea3-241">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="aaea3-242">Latence uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="aaea3-242">UI Latency</span></span>

<span data-ttu-id="aaea3-243">Latence uživatelského rozhraní je doba, kterou trvá od iniciované akce až do doby, kdy je uživatelské rozhraní aktualizováno.</span><span class="sxs-lookup"><span data-stu-id="aaea3-243">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="aaea3-244">Menší hodnoty latence uživatelského rozhraní jsou pro aplikaci pro reakci na uživatele naprosto nezbytné.</span><span class="sxs-lookup"><span data-stu-id="aaea3-244">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="aaea3-245">V aplikaci Blazor serveru se všechny akce odesílají na server, zpracováno a rozdíl v uživatelském rozhraní se pošle zpátky.</span><span class="sxs-lookup"><span data-stu-id="aaea3-245">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="aaea3-246">V důsledku toho je latence uživatelského rozhraní součtem latence sítě a latencí serveru při zpracování akce.</span><span class="sxs-lookup"><span data-stu-id="aaea3-246">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="aaea3-247">Pro podnikovou aplikaci, která je omezená jenom na soukromou podnikovou síť, se většinou neprojeví vlivu na vnímání uživatele latence kvůli latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="aaea3-247">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="aaea3-248">V případě aplikace nasazené přes Internet může latence znamenat uživatele, zejména v případě, že uživatelé jsou geograficky distribuováni podrobněji.</span><span class="sxs-lookup"><span data-stu-id="aaea3-248">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="aaea3-249">Využití paměti může také přispět k latenci aplikace.</span><span class="sxs-lookup"><span data-stu-id="aaea3-249">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="aaea3-250">Zvýšení využití paměti vede k častému uvolňování paměti nebo paměti stránkování na disk, přičemž obě tyto čítače zvyšují výkon aplikace a následně zvyšují latenci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="aaea3-250">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="aaea3-251">Další informace najdete v tématu <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="aaea3-251">For more information, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="aaea3-252">aplikace Blazor Server by měly být optimalizované pro minimalizaci latence uživatelského rozhraní tím, že se sníží latence sítě a využití paměti.</span><span class="sxs-lookup"><span data-stu-id="aaea3-252">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="aaea3-253">Přístup k měření latence sítě najdete v tématu <xref:host-and-deploy/blazor/server#measure-network-latency>.</span><span class="sxs-lookup"><span data-stu-id="aaea3-253">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="aaea3-254">Další informace o SignalR a Blazornajdete v těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="aaea3-254">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a><span data-ttu-id="aaea3-255">Připojení k serveru</span><span class="sxs-lookup"><span data-stu-id="aaea3-255">Connection to the server</span></span>

<span data-ttu-id="aaea3-256">aplikace Blazor serveru vyžadují připojení k serveru aktivní SignalR.</span><span class="sxs-lookup"><span data-stu-id="aaea3-256">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="aaea3-257">Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru.</span><span class="sxs-lookup"><span data-stu-id="aaea3-257">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="aaea3-258">Pokud je stav klienta stále v paměti, klientská relace pokračuje bez ztráty stavu.</span><span class="sxs-lookup"><span data-stu-id="aaea3-258">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

#### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="aaea3-259">Opětovné připojení ke stejnému serveru</span><span class="sxs-lookup"><span data-stu-id="aaea3-259">Reconnection to the same server</span></span>

<span data-ttu-id="aaea3-260">Aplikace Blazor serveru se předem vykreslí v reakci na první požadavek klienta, který nastaví stav uživatelského rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="aaea3-260">A Blazor Server app prerenders in response to the first client request, which sets up the UI state on the server.</span></span> <span data-ttu-id="aaea3-261">Když se klient pokusí vytvořit připojení SignalR, klient se musí znovu připojit ke stejnému serveru.</span><span class="sxs-lookup"><span data-stu-id="aaea3-261">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> <span data-ttu-id="aaea3-262">aplikace Blazor serveru, které používají více než jeden back-end Server, by měly implementovat *rychlé relace* pro SignalR připojení.</span><span class="sxs-lookup"><span data-stu-id="aaea3-262">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="aaea3-263">Pro Blazor serverových aplikací doporučujeme používat [službu Azure SignalR](/azure/azure-signalr) .</span><span class="sxs-lookup"><span data-stu-id="aaea3-263">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="aaea3-264">Služba umožňuje horizontální navýšení kapacity aplikace Blazor serveru na velký počet souběžných připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="aaea3-264">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="aaea3-265">Rychlé relace jsou povolené pro službu Azure SignalR tím, že nastavíte možnost `ServerStickyMode` služby nebo hodnotu konfigurace na `Required`.</span><span class="sxs-lookup"><span data-stu-id="aaea3-265">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="aaea3-266">Další informace najdete v tématu <xref:host-and-deploy/blazor/server#signalr-configuration>.</span><span class="sxs-lookup"><span data-stu-id="aaea3-266">For more information, see <xref:host-and-deploy/blazor/server#signalr-configuration>.</span></span>

<span data-ttu-id="aaea3-267">Při použití služby IIS jsou v rámci směrování žádostí na aplikace povoleny rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="aaea3-267">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="aaea3-268">Další informace najdete v tématu [Vyrovnávání zatížení HTTP pomocí směrování žádostí na aplikace](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="aaea3-268">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="aaea3-269">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="aaea3-269">Reflect the connection state in the UI</span></span>

<span data-ttu-id="aaea3-270">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="aaea3-270">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="aaea3-271">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="aaea3-271">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="aaea3-272">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `id` `components-reconnect-modal` v `<body>` stránky *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="aaea3-272">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="aaea3-273">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="aaea3-273">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="aaea3-274">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="aaea3-274">CSS class</span></span>                       | <span data-ttu-id="aaea3-275">Indikuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="aaea3-275">Indicates&hellip;</span></span> |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | <span data-ttu-id="aaea3-276">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="aaea3-276">A lost connection.</span></span> <span data-ttu-id="aaea3-277">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="aaea3-277">The client is attempting to reconnect.</span></span> <span data-ttu-id="aaea3-278">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="aaea3-278">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="aaea3-279">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="aaea3-279">An active connection is re-established to the server.</span></span> <span data-ttu-id="aaea3-280">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="aaea3-280">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="aaea3-281">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="aaea3-281">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="aaea3-282">Chcete-li se pokusit o opětovné připojení, zavolejte `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="aaea3-282">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="aaea3-283">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="aaea3-283">Reconnection rejected.</span></span> <span data-ttu-id="aaea3-284">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="aaea3-284">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="aaea3-285">Chcete-li aplikaci znovu načíst, zavolejte `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="aaea3-285">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="aaea3-286">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="aaea3-286">This connection state may result when:</span></span><ul><li><span data-ttu-id="aaea3-287">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="aaea3-287">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="aaea3-288">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="aaea3-288">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="aaea3-289">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="aaea3-289">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="aaea3-290">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="aaea3-290">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="aaea3-291">Stav opětovného připojení po předvykreslování</span><span class="sxs-lookup"><span data-stu-id="aaea3-291">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="aaea3-292">aplikace Blazor serveru se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="aaea3-292">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="aaea3-293">To je nastaveno na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="aaea3-293">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="aaea3-294">`RenderMode` nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="aaea3-294">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="aaea3-295">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="aaea3-295">Is prerendered into the page.</span></span>
* <span data-ttu-id="aaea3-296">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="aaea3-296">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="aaea3-297">Popis</span><span class="sxs-lookup"><span data-stu-id="aaea3-297">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="aaea3-298">Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="aaea3-298">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="aaea3-299">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="aaea3-299">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="aaea3-300">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="aaea3-300">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="aaea3-301">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="aaea3-301">Output from the component isn't included.</span></span> <span data-ttu-id="aaea3-302">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="aaea3-302">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="aaea3-303">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="aaea3-303">Renders the component into static HTML.</span></span> |

<span data-ttu-id="aaea3-304">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="aaea3-304">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="aaea3-305">Když je `ServerPrerendered``RenderMode`, komponenta se zpočátku generuje jako součást stránky staticky.</span><span class="sxs-lookup"><span data-stu-id="aaea3-305">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="aaea3-306">Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní.</span><span class="sxs-lookup"><span data-stu-id="aaea3-306">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="aaea3-307">Pokud je k dispozici metoda inicializace životního cyklu "inicializujd [{Async}](xref:blazor/lifecycle#component-initialization-methods) " pro inicializaci komponenty, je metoda provedena *dvakrát*:</span><span class="sxs-lookup"><span data-stu-id="aaea3-307">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="aaea3-308">Když se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="aaea3-308">When the component is prerendered statically.</span></span>
* <span data-ttu-id="aaea3-309">Po navázání připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="aaea3-309">After the server connection has been established.</span></span>

<span data-ttu-id="aaea3-310">Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.</span><span class="sxs-lookup"><span data-stu-id="aaea3-310">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="aaea3-311">Abyste se vyhnuli scénáři dvojího vykreslování v aplikaci Blazor serveru:</span><span class="sxs-lookup"><span data-stu-id="aaea3-311">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="aaea3-312">Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="aaea3-312">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="aaea3-313">Použijte identifikátor při předvykreslování k uložení stavu součásti.</span><span class="sxs-lookup"><span data-stu-id="aaea3-313">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="aaea3-314">Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="aaea3-314">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="aaea3-315">Následující kód demonstruje aktualizované `WeatherForecastService` v aplikaci Blazor serveru založeném na šablonách, která vylučuje dvojité vykreslování:</span><span class="sxs-lookup"><span data-stu-id="aaea3-315">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] Summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="aaea3-316">Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="aaea3-316">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="aaea3-317">Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="aaea3-317">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="aaea3-318">Při vykreslení stránky nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="aaea3-318">When the page or view renders:</span></span>

* <span data-ttu-id="aaea3-319">Komponenta je předem vykreslena se stránkou nebo zobrazením.</span><span class="sxs-lookup"><span data-stu-id="aaea3-319">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="aaea3-320">Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="aaea3-320">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="aaea3-321">Po navázání SignalRho připojení se vytvoří nový stav součásti.</span><span class="sxs-lookup"><span data-stu-id="aaea3-321">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="aaea3-322">Následující stránka Razor vykresluje komponentu `Counter`:</span><span class="sxs-lookup"><span data-stu-id="aaea3-322">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="aaea3-323">Vykreslování neinteraktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="aaea3-323">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="aaea3-324">Na následující stránce Razor je součást `Counter` staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="aaea3-324">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="aaea3-325">Vzhledem k tomu, že `MyComponent` jsou staticky vykresleny, nemůže být komponenta interaktivní.</span><span class="sxs-lookup"><span data-stu-id="aaea3-325">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="aaea3-326">Rozpoznat, kdy se aplikace předvykresluje</span><span class="sxs-lookup"><span data-stu-id="aaea3-326">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="aaea3-327">Konfigurace klienta SignalR pro aplikace Blazor serveru</span><span class="sxs-lookup"><span data-stu-id="aaea3-327">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="aaea3-328">V některých případech je třeba nakonfigurovat klienta SignalR používaného serverovými aplikacemi Blazor.</span><span class="sxs-lookup"><span data-stu-id="aaea3-328">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="aaea3-329">Například můžete chtít nakonfigurovat protokolování klienta SignalR pro diagnostiku potíží s připojením.</span><span class="sxs-lookup"><span data-stu-id="aaea3-329">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="aaea3-330">Konfigurace klienta SignalR v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="aaea3-330">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="aaea3-331">Přidejte atribut `autostart="false"` do značky `<script>` pro skript `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="aaea3-331">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="aaea3-332">Zavolejte `Blazor.start` a předejte objekt konfigurace, který určuje SignalR Builder.</span><span class="sxs-lookup"><span data-stu-id="aaea3-332">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="aaea3-333">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="aaea3-333">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
