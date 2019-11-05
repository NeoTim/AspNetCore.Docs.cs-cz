---
title: ASP.NET Core modely hostování Blazor
author: guardrex
description: Pochopení modelů hostování serverů Blazor a Blazor pro WebAssembly
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/03/2019
uid: blazor/hosting-models
ms.openlocfilehash: d1b9e6ab7ba93c00a569be309f2334df9e3f4140
ms.sourcegitcommit: e5d4768aaf85703effb4557a520d681af8284e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73616593"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="628da-103">ASP.NET Core modely hostování Blazor</span><span class="sxs-lookup"><span data-stu-id="628da-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="628da-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="628da-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="628da-105">Blazor je webová architektura navržená tak, aby běžela na straně klienta v prohlížeči v prostředí .NET runtime založeném na webovém [sestavení](https://webassembly.org/)(*Blazor WebAssembly*) nebo na straně serveru v ASP.NET Core (*Server Blazor*).</span><span class="sxs-lookup"><span data-stu-id="628da-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="628da-106">Bez ohledu na model hostování jsou modely aplikací a komponent *stejné*.</span><span class="sxs-lookup"><span data-stu-id="628da-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="628da-107">Chcete-li vytvořit projekt pro modely hostování popsané v tomto článku, přečtěte si téma <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="628da-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="628da-108">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="628da-108">Blazor WebAssembly</span></span>

<span data-ttu-id="628da-109">Hlavní hostující model pro Blazor je spuštěn na straně klienta v prohlížeči na webovém sestavení.</span><span class="sxs-lookup"><span data-stu-id="628da-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="628da-110">Do prohlížeče se stáhnou aplikace Blazor, její závislosti a modul runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="628da-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="628da-111">Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="628da-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="628da-112">Aktualizace uživatelského rozhraní a zpracování událostí se vyskytují v rámci stejného procesu.</span><span class="sxs-lookup"><span data-stu-id="628da-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="628da-113">Prostředky aplikace se nasazují jako statické soubory na webový server nebo službu, která je schopná obsluhovat statický obsah klientům.</span><span class="sxs-lookup"><span data-stu-id="628da-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor WebAssembly: aplikace Blazor se spouští ve vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="628da-115">Chcete-li vytvořit aplikaci Blazor pomocí modelu hostování na straně klienta, použijte šablonu **aplikace Blazor WebAssembly** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="628da-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="628da-116">Po výběru šablony **aplikace Blazor WebAssembly** máte možnost konfigurovat aplikaci tak, aby používala ASP.NET Core back-end, a to tak, že vyberete zaškrtávací políčko **ASP.NET Core hostované** ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="628da-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="628da-117">Aplikace ASP.NET Core obsluhuje aplikaci Blazor klientům.</span><span class="sxs-lookup"><span data-stu-id="628da-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="628da-118">Aplikace WebAssembly v Blazor může komunikovat se serverem přes síť pomocí volání webového rozhraní API nebo [signalizace](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="628da-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="628da-119">Šablony obsahují skript *blazor. WebAssembly. js* , který zpracovává:</span><span class="sxs-lookup"><span data-stu-id="628da-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="628da-120">Stažení modulu runtime .NET, aplikace a závislostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="628da-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="628da-121">Inicializace modulu runtime pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="628da-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="628da-122">Model hostování WebAssembly Blazor nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="628da-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="628da-123">Neexistuje žádná závislost na straně serveru .NET.</span><span class="sxs-lookup"><span data-stu-id="628da-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="628da-124">Aplikace po stažení do klienta plně funguje.</span><span class="sxs-lookup"><span data-stu-id="628da-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="628da-125">Prostředky a možnosti klienta jsou plně využité.</span><span class="sxs-lookup"><span data-stu-id="628da-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="628da-126">Práce je ze serveru převedena na klienta.</span><span class="sxs-lookup"><span data-stu-id="628da-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="628da-127">Pro hostování aplikace není vyžadován ASP.NET Core webový server.</span><span class="sxs-lookup"><span data-stu-id="628da-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="628da-128">Jsou možné scénáře nasazení bez serveru (například poskytování aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="628da-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="628da-129">Existuje downsides hostování WebAssembly Blazor:</span><span class="sxs-lookup"><span data-stu-id="628da-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="628da-130">Aplikace je omezená na možnosti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="628da-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="628da-131">Je vyžadován klientský hardware a software (například podpora WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="628da-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="628da-132">Velikost ke stažení je větší a aplikace trvá déle, než se načtou.</span><span class="sxs-lookup"><span data-stu-id="628da-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="628da-133">Podpora modulu runtime .NET a nástrojů je méně vyspělá.</span><span class="sxs-lookup"><span data-stu-id="628da-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="628da-134">Například omezení existují v [.NET Standard](/dotnet/standard/net-standard) podpoře a ladění.</span><span class="sxs-lookup"><span data-stu-id="628da-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="628da-135">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="628da-135">Blazor Server</span></span>

<span data-ttu-id="628da-136">S modelem hostování serveru Blazor je aplikace spuštěná na serveru z aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="628da-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="628da-137">Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes připojení k [signalizaci](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="628da-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![Prohlížeč komunikuje s aplikací (hostovanou v aplikaci ASP.NET Core) na serveru přes připojení k signalizaci.](hosting-models/_static/blazor-server.png)

<span data-ttu-id="628da-139">Pokud chcete vytvořit aplikaci Blazor pomocí modelu hostování serveru Blazor, použijte šablonu **aplikace serveru Blazor** pro ASP.NET Core ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="628da-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="628da-140">Aplikace ASP.NET Core hostuje aplikaci serveru Blazor a vytvoří koncový bod signalizace, kde se klienti připojují.</span><span class="sxs-lookup"><span data-stu-id="628da-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="628da-141">Aplikace ASP.NET Core odkazuje na třídu `Startup` aplikace, která má přidat:</span><span class="sxs-lookup"><span data-stu-id="628da-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="628da-142">Služby na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="628da-142">Server-side services.</span></span>
* <span data-ttu-id="628da-143">Aplikace do kanálu pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="628da-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="628da-144">Skript *blazor. Server. js*&dagger; vytvoří připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="628da-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="628da-145">Je zodpovědností aplikace zachovat a obnovit stav aplikace podle potřeby (například v případě ztraceného síťového připojení).</span><span class="sxs-lookup"><span data-stu-id="628da-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="628da-146">Model hostování serveru Blazor nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="628da-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="628da-147">Velikost ke stažení je výrazně menší než Blazor aplikace WebAssembly a aplikace se načte mnohem rychleji.</span><span class="sxs-lookup"><span data-stu-id="628da-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="628da-148">Aplikace plně využívá možnosti serveru, včetně použití všech rozhraní API kompatibilních s rozhraním .NET Core.</span><span class="sxs-lookup"><span data-stu-id="628da-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="628da-149">Rozhraní .NET Core na serveru se používá ke spuštění aplikace, takže stávající nástroje .NET, jako je ladění, fungují podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="628da-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="628da-150">Podporují se tenké klienty.</span><span class="sxs-lookup"><span data-stu-id="628da-150">Thin clients are supported.</span></span> <span data-ttu-id="628da-151">Například aplikace serveru Blazor fungují s prohlížeči, které nepodporují WebAssembly a na zařízeních s omezením prostředků.</span><span class="sxs-lookup"><span data-stu-id="628da-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="628da-152">Základ pro .NET/C# kód aplikace, včetně kódu komponenty aplikace, není obsluhován klientům.</span><span class="sxs-lookup"><span data-stu-id="628da-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="628da-153">Downsides hostování serveru Blazor:</span><span class="sxs-lookup"><span data-stu-id="628da-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="628da-154">Vyšší latence obvykle existuje.</span><span class="sxs-lookup"><span data-stu-id="628da-154">Higher latency usually exists.</span></span> <span data-ttu-id="628da-155">Každá interakce uživatele zahrnuje směrování sítě.</span><span class="sxs-lookup"><span data-stu-id="628da-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="628da-156">Neexistuje žádná podpora offline.</span><span class="sxs-lookup"><span data-stu-id="628da-156">There's no offline support.</span></span> <span data-ttu-id="628da-157">Pokud připojení klienta neproběhne úspěšně, aplikace přestane fungovat.</span><span class="sxs-lookup"><span data-stu-id="628da-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="628da-158">Pro aplikace s mnoha uživateli je škálovatelnost náročná.</span><span class="sxs-lookup"><span data-stu-id="628da-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="628da-159">Server musí spravovat více připojení klientů a zpracovávat stav klienta.</span><span class="sxs-lookup"><span data-stu-id="628da-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="628da-160">Pro obsluhu aplikace je vyžadován ASP.NET Core Server.</span><span class="sxs-lookup"><span data-stu-id="628da-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="628da-161">Scénáře nasazení bez serveru nejsou možné (například poskytování aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="628da-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="628da-162">skript &dagger;The *blazor. Server. js* se obsluhuje z vloženého prostředku ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="628da-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="628da-163">Porovnání s uživatelským rozhraním vykresleným serverem</span><span class="sxs-lookup"><span data-stu-id="628da-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="628da-164">Jedním ze způsobů, jak pochopit aplikace Blazor serveru, je pochopit, jak se liší od tradičních modelů pro vykreslování uživatelského rozhraní v aplikacích ASP.NET Core pomocí zobrazení Razor nebo Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="628da-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="628da-165">Oba modely používají jazyk Razor k popisu obsahu HTML, ale významně se liší v tom, jak se vykreslují kód.</span><span class="sxs-lookup"><span data-stu-id="628da-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="628da-166">Když je vykreslena stránka nebo zobrazení Razor, každý řádek kódu Razor generuje kód HTML v textovém formátu.</span><span class="sxs-lookup"><span data-stu-id="628da-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="628da-167">Po vykreslení Server uvolní instanci stránky nebo zobrazení, včetně veškerého stavu, který byl vytvořen.</span><span class="sxs-lookup"><span data-stu-id="628da-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="628da-168">Pokud dojde k jiné žádosti na stránku, například když se ověření serveru nepovede a zobrazí se shrnutí ověření:</span><span class="sxs-lookup"><span data-stu-id="628da-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="628da-169">Celá stránka se znovu vykreslí do textu HTML.</span><span class="sxs-lookup"><span data-stu-id="628da-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="628da-170">Stránka je odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="628da-170">The page is sent to the client.</span></span>

<span data-ttu-id="628da-171">Aplikace Blazor se skládá z opakovaně použitelných prvků uživatelského rozhraní s názvem *Components*.</span><span class="sxs-lookup"><span data-stu-id="628da-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="628da-172">Komponenta obsahuje C# kód, značku a další komponenty.</span><span class="sxs-lookup"><span data-stu-id="628da-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="628da-173">Při vykreslení komponenty Blazor vytvoří graf zahrnutých komponent podobně jako HTML nebo XML model DOM (Document Object Model) (DOM).</span><span class="sxs-lookup"><span data-stu-id="628da-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="628da-174">Tento graf obsahuje stav součásti uložený ve vlastnostech a polích.</span><span class="sxs-lookup"><span data-stu-id="628da-174">This graph includes component state held in properties and fields.</span></span> <span data-ttu-id="628da-175">Blazor vyhodnotí graf komponent pro vytvoření binární reprezentace kódu.</span><span class="sxs-lookup"><span data-stu-id="628da-175">Blazor evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="628da-176">Binární formát může být:</span><span class="sxs-lookup"><span data-stu-id="628da-176">The binary format can be:</span></span>

* <span data-ttu-id="628da-177">Byl převeden na text HTML (během předvykreslování).</span><span class="sxs-lookup"><span data-stu-id="628da-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="628da-178">Slouží k efektivní aktualizaci značek během pravidelného vykreslování.</span><span class="sxs-lookup"><span data-stu-id="628da-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="628da-179">Aktualizace uživatelského rozhraní v Blazor se spouští:</span><span class="sxs-lookup"><span data-stu-id="628da-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="628da-180">Interakce s uživatelem, například výběr tlačítka.</span><span class="sxs-lookup"><span data-stu-id="628da-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="628da-181">Aktivační události aplikace, jako je například časovač.</span><span class="sxs-lookup"><span data-stu-id="628da-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="628da-182">Graf se znovu vykreslí a počítá se *rozdíl* v uživatelském rozhraní (rozdíl).</span><span class="sxs-lookup"><span data-stu-id="628da-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="628da-183">Tento rozdíl je nejmenší sada úprav modelu DOM, která je nutná k aktualizaci uživatelského rozhraní na klientovi.</span><span class="sxs-lookup"><span data-stu-id="628da-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="628da-184">Rozdíl se pošle klientovi v binárním formátu a použije ho prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="628da-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="628da-185">Komponenta je uvolněna poté, co uživatel z něj přejde na klienta.</span><span class="sxs-lookup"><span data-stu-id="628da-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="628da-186">I když uživatel pracuje s komponentou, musí se stav komponenty (služby, prostředky) uchovávat v paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="628da-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="628da-187">Vzhledem k tomu, že stav mnoha součástí může být serverem současně udržován, vyčerpání paměti je problém, který je třeba řešit.</span><span class="sxs-lookup"><span data-stu-id="628da-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="628da-188">Pokyny k vytvoření aplikace serveru Blazor pro zajištění nejlepšího využití paměti serveru najdete v tématu <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="628da-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="circuits"></a><span data-ttu-id="628da-189">Spoj</span><span class="sxs-lookup"><span data-stu-id="628da-189">Circuits</span></span>

<span data-ttu-id="628da-190">Serverová aplikace Blazor je postavená na [ASP.NET Core signalizaci](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="628da-190">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="628da-191">Každý klient komunikuje se serverem přes jedno nebo více připojení k signalizaci označovaného jako *okruh*.</span><span class="sxs-lookup"><span data-stu-id="628da-191">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="628da-192">Okruh je Blazor abstrakce přes připojení k signalizaci, která umožňuje tolerovat dočasné síťové přerušení.</span><span class="sxs-lookup"><span data-stu-id="628da-192">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="628da-193">Když klient Blazor uvidí, že připojení k signalizaci je odpojené, pokusí se znovu připojit k serveru pomocí nového připojení k signalizaci.</span><span class="sxs-lookup"><span data-stu-id="628da-193">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="628da-194">Každá obrazovka prohlížeče (karta prohlížeče nebo IFRAME), která je připojená k aplikaci Blazor Server, používá připojení k signalizaci.</span><span class="sxs-lookup"><span data-stu-id="628da-194">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="628da-195">Toto je ještě další důležité rozdíly ve srovnání s typickými aplikacemi vygenerovanými serverem.</span><span class="sxs-lookup"><span data-stu-id="628da-195">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="628da-196">V aplikaci vykreslené serverem se při otevření stejné aplikace v několika obrazovkách prohlížeče obvykle nepřevádí na další požadavky na prostředky na serveru.</span><span class="sxs-lookup"><span data-stu-id="628da-196">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="628da-197">V aplikaci Blazor serveru vyžaduje Každá obrazovka prohlížeče samostatný okruh a samostatné instance stavu součásti, které se mají spravovat serverem.</span><span class="sxs-lookup"><span data-stu-id="628da-197">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

<span data-ttu-id="628da-198">Blazor má za to, že se má zavřít karta prohlížeče nebo přejít na externí adresu URL s *řádným* ukončením.</span><span class="sxs-lookup"><span data-stu-id="628da-198">Blazor considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="628da-199">V případě řádného ukončení se okruh a přidružené prostředky ihned uvolňují.</span><span class="sxs-lookup"><span data-stu-id="628da-199">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="628da-200">Klient se může také bez problémů odpojit, například kvůli přerušení sítě.</span><span class="sxs-lookup"><span data-stu-id="628da-200">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> <span data-ttu-id="628da-201">Server Blazor ukládá odpojené okruhy pro Konfigurovatelný interval, aby se mohl klient znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="628da-201">Blazor Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="628da-202">Další informace najdete v části [opětovné připojení ke stejnému serveru](#reconnection-to-the-same-server) .</span><span class="sxs-lookup"><span data-stu-id="628da-202">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="628da-203">Latence uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="628da-203">UI Latency</span></span>

<span data-ttu-id="628da-204">Latence uživatelského rozhraní je doba, kterou trvá od iniciované akce až do doby, kdy je uživatelské rozhraní aktualizováno.</span><span class="sxs-lookup"><span data-stu-id="628da-204">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="628da-205">Menší hodnoty latence uživatelského rozhraní jsou pro aplikaci pro reakci na uživatele naprosto nezbytné.</span><span class="sxs-lookup"><span data-stu-id="628da-205">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="628da-206">V aplikaci Blazor serveru se každou akci pošle na server, zpracováno a rozdíl v uživatelském rozhraní se pošle zpátky.</span><span class="sxs-lookup"><span data-stu-id="628da-206">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="628da-207">V důsledku toho je latence uživatelského rozhraní součtem latence sítě a latencí serveru při zpracování akce.</span><span class="sxs-lookup"><span data-stu-id="628da-207">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="628da-208">Pro podnikovou aplikaci, která je omezená jenom na soukromou podnikovou síť, se většinou neprojeví vlivu na vnímání uživatele latence kvůli latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="628da-208">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="628da-209">V případě aplikace nasazené přes Internet může latence znamenat uživatele, zejména v případě, že uživatelé jsou geograficky distribuováni podrobněji.</span><span class="sxs-lookup"><span data-stu-id="628da-209">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="628da-210">Využití paměti může také přispět k latenci aplikace.</span><span class="sxs-lookup"><span data-stu-id="628da-210">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="628da-211">Zvýšení využití paměti vede k častému uvolňování paměti nebo paměti stránkování na disk, přičemž obě tyto čítače zvyšují výkon aplikace a následně zvyšují latenci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="628da-211">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="628da-212">Další informace najdete v tématu <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="628da-212">For more information, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="628da-213">Blazor serverové aplikace by měly být optimalizované pro minimalizaci latence uživatelského rozhraní tím, že se sníží latence sítě a využití paměti.</span><span class="sxs-lookup"><span data-stu-id="628da-213">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="628da-214">Přístup k měření latence sítě najdete v tématu <xref:host-and-deploy/blazor/server#measure-network-latency>.</span><span class="sxs-lookup"><span data-stu-id="628da-214">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="628da-215">Další informace o signalizaci a Blazor najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="628da-215">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="628da-216">Opětovné připojení ke stejnému serveru</span><span class="sxs-lookup"><span data-stu-id="628da-216">Reconnection to the same server</span></span>

<span data-ttu-id="628da-217">Serverové aplikace Blazor vyžadují připojení aktivního signálu k serveru.</span><span class="sxs-lookup"><span data-stu-id="628da-217">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="628da-218">Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru.</span><span class="sxs-lookup"><span data-stu-id="628da-218">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="628da-219">Pokud je stav klienta stále v paměti, klientská relace pokračuje bez ztráty stavu.</span><span class="sxs-lookup"><span data-stu-id="628da-219">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

<span data-ttu-id="628da-220">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="628da-220">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="628da-221">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="628da-221">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="628da-222">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `components-reconnect-modal` jako jeho `id` na stránce *_Host. cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="628da-222">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="628da-223">Klient aktualizuje tento prvek pomocí jedné z následujících tříd šablony stylů CSS na základě stavu připojení:</span><span class="sxs-lookup"><span data-stu-id="628da-223">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>

* <span data-ttu-id="628da-224">`components-reconnect-show` &ndash; zobrazí uživatelské rozhraní pro indikaci ztraceného připojení a klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="628da-224">`components-reconnect-show` &ndash; Show the UI to indicate a lost connection and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="628da-225">`components-reconnect-hide` &ndash; klient má aktivní připojení, skryje uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="628da-225">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="628da-226">`components-reconnect-failed` &ndash; opětovné připojení selhalo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="628da-226">`components-reconnect-failed` &ndash; Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="628da-227">Chcete-li se pokusit o opětovné připojení, zavolejte `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="628da-227">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span>
* <span data-ttu-id="628da-228">`components-reconnect-rejected` &ndash; opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="628da-228">`components-reconnect-rejected` &ndash; Reconnection rejected.</span></span> <span data-ttu-id="628da-229">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru zmizí.</span><span class="sxs-lookup"><span data-stu-id="628da-229">The server was reached but refused the connection, and the user's state on the server is gone.</span></span> <span data-ttu-id="628da-230">Chcete-li aplikaci znovu načíst, zavolejte `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="628da-230">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="628da-231">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="628da-231">This connection state may result when:</span></span>
  * <span data-ttu-id="628da-232">Dojde k chybě v okruhu (kód na straně serveru).</span><span class="sxs-lookup"><span data-stu-id="628da-232">A crash in the circuit (server-side code) occurs.</span></span>
  * <span data-ttu-id="628da-233">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="628da-233">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="628da-234">Instance komponent, se kterými uživatel spolupracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="628da-234">Instances of components that the user was interacting with are disposed.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="628da-235">Stav opětovného připojení po předvykreslování</span><span class="sxs-lookup"><span data-stu-id="628da-235">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="628da-236">Aplikace Blazor serveru se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="628da-236">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="628da-237">To je nastavené na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="628da-237">This is set up in the *_Host.cshtml* Razor page:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

<span data-ttu-id="628da-238">`RenderMode` nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="628da-238">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="628da-239">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="628da-239">Is prerendered into the page.</span></span>
* <span data-ttu-id="628da-240">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="628da-240">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="628da-241">Popis</span><span class="sxs-lookup"><span data-stu-id="628da-241">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="628da-242">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="628da-242">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="628da-243">Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor.</span><span class="sxs-lookup"><span data-stu-id="628da-243">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="628da-244">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="628da-244">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="628da-245">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="628da-245">Output from the component isn't included.</span></span> <span data-ttu-id="628da-246">Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor.</span><span class="sxs-lookup"><span data-stu-id="628da-246">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="628da-247">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="628da-247">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="628da-248">Popis</span><span class="sxs-lookup"><span data-stu-id="628da-248">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="628da-249">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="628da-249">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="628da-250">Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor.</span><span class="sxs-lookup"><span data-stu-id="628da-250">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="628da-251">Parametry nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="628da-251">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="628da-252">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="628da-252">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="628da-253">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="628da-253">Output from the component isn't included.</span></span> <span data-ttu-id="628da-254">Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor.</span><span class="sxs-lookup"><span data-stu-id="628da-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="628da-255">Parametry nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="628da-255">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="628da-256">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="628da-256">Renders the component into static HTML.</span></span> <span data-ttu-id="628da-257">Jsou podporovány parametry.</span><span class="sxs-lookup"><span data-stu-id="628da-257">Parameters are supported.</span></span> |

::: moniker-end

<span data-ttu-id="628da-258">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="628da-258">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="628da-259">Když je `ServerPrerendered``RenderMode`, komponenta se zpočátku generuje jako součást stránky staticky.</span><span class="sxs-lookup"><span data-stu-id="628da-259">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="628da-260">Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní.</span><span class="sxs-lookup"><span data-stu-id="628da-260">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="628da-261">Pokud je k dispozici [Metoda životního cyklu](xref:blazor/components#lifecycle-methods) pro inicializaci komponenty (`OnInitialized{Async}`), metoda je provedena *dvakrát*:</span><span class="sxs-lookup"><span data-stu-id="628da-261">If a [lifecycle method](xref:blazor/components#lifecycle-methods) for initializing the component (`OnInitialized{Async}`) is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="628da-262">Když se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="628da-262">When the component is prerendered statically.</span></span>
* <span data-ttu-id="628da-263">Po navázání připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="628da-263">After the server connection has been established.</span></span>

<span data-ttu-id="628da-264">Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.</span><span class="sxs-lookup"><span data-stu-id="628da-264">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="628da-265">Abyste se vyhnuli scénáři dvojího vykreslování v aplikaci Blazor serveru:</span><span class="sxs-lookup"><span data-stu-id="628da-265">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="628da-266">Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="628da-266">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="628da-267">Použijte identifikátor při předvykreslování k uložení stavu součásti.</span><span class="sxs-lookup"><span data-stu-id="628da-267">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="628da-268">Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="628da-268">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="628da-269">Následující kód demonstruje aktualizované `WeatherForecastService` v serverové aplikaci Blazor založené na šablonách, které brání dvojímu vykreslení:</span><span class="sxs-lookup"><span data-stu-id="628da-269">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="628da-270">Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="628da-270">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="628da-271">Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="628da-271">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="628da-272">Při vykreslení stránky nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="628da-272">When the page or view renders:</span></span>

* <span data-ttu-id="628da-273">Komponenta je předem vykreslena se stránkou nebo zobrazením.</span><span class="sxs-lookup"><span data-stu-id="628da-273">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="628da-274">Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="628da-274">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="628da-275">Po navázání připojení k signalizaci se vytvoří nový stav součásti.</span><span class="sxs-lookup"><span data-stu-id="628da-275">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="628da-276">Následující stránka Razor vykreslí součást `Counter`:</span><span class="sxs-lookup"><span data-stu-id="628da-276">The following Razor page renders a `Counter` component:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="628da-277">Vykreslování neinteraktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="628da-277">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="628da-278">Na následující stránce Razor je součást `MyComponent` staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="628da-278">In the following Razor page, the `MyComponent` component is statically rendered with an initial value that's specified using a form:</span></span>

::: moniker range=">= aspnetcore-3.1"

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

::: moniker-end

::: moniker range="< aspnetcore-3.1"

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

::: moniker-end

<span data-ttu-id="628da-279">Vzhledem k tomu, že je `MyComponent` staticky vykreslen, součást nemůže být interaktivní.</span><span class="sxs-lookup"><span data-stu-id="628da-279">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="628da-280">Rozpoznat, kdy se aplikace předvykresluje</span><span class="sxs-lookup"><span data-stu-id="628da-280">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="628da-281">Konfigurace klienta nástroje Signal pro aplikace serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="628da-281">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="628da-282">V některých případech je třeba nakonfigurovat klienta nástroje Signal, který používají aplikace Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="628da-282">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="628da-283">Například můžete chtít nakonfigurovat protokolování na straně klienta signalizace, aby bylo možné diagnostikovat problém s připojením.</span><span class="sxs-lookup"><span data-stu-id="628da-283">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="628da-284">Konfigurace klienta signalizace v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="628da-284">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="628da-285">Přidejte atribut `autostart="false"` do značky `<script>` pro skript *blazor. Server. js* .</span><span class="sxs-lookup"><span data-stu-id="628da-285">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="628da-286">Zavolejte `Blazor.start` a předejte objekt konfigurace, který určuje tvůrce signálu.</span><span class="sxs-lookup"><span data-stu-id="628da-286">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="628da-287">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="628da-287">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
