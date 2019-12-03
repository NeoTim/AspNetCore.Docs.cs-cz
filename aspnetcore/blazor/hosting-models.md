---
title: Modely hostování ASP.NET Core Blazor
author: guardrex
description: Pochopení Blazor modelů hostování serverů a Blazor serveru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2019
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 38db9804c9cdd1aa31ca48af2dd9ec2e85175156
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681042"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a><span data-ttu-id="bb309-103">Modely hostování ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="bb309-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="bb309-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="bb309-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="bb309-105"> je webová architektura navržená tak, aby na straně klienta běžela na klientském počítači .NET runtime založeném na webovém [sestavení](https://webassembly.org/)( *Blazor webové sestavení*) nebo na straně serveru v ASP.NET Core ( *ServerBlazor* ).</span><span class="sxs-lookup"><span data-stu-id="bb309-105"> is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="bb309-106">Bez ohledu na model hostování jsou modely aplikací a komponent *stejné*.</span><span class="sxs-lookup"><span data-stu-id="bb309-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="bb309-107">Chcete-li vytvořit projekt pro modely hostování popsané v tomto článku, přečtěte si téma <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="bb309-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="bb309-108"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="bb309-108"> WebAssembly</span></span>

<span data-ttu-id="bb309-109">Hlavní hostující model pro Blazor spouští klientskou stranu v prohlížeči na webovém sestavení.</span><span class="sxs-lookup"><span data-stu-id="bb309-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="bb309-110">Aplikace Blazor, její závislosti a modul runtime .NET se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="bb309-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="bb309-111">Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="bb309-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="bb309-112">Aktualizace uživatelského rozhraní a zpracování událostí se vyskytují v rámci stejného procesu.</span><span class="sxs-lookup"><span data-stu-id="bb309-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="bb309-113">Prostředky aplikace se nasazují jako statické soubory na webový server nebo službu, která je schopná obsluhovat statický obsah klientům.</span><span class="sxs-lookup"><span data-stu-id="bb309-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![[! Evřít. Rozhraní Blazor (NO-LOC)]<span data-ttu-id="bb309-114"> WebAssembly: [! Evřít. Aplikace NO-LOC (Blazor)] běží ve vlákně uživatelského rozhraní v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="bb309-114"> WebAssembly: The Blazor app runs on a UI thread inside the browser.</span></span>](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="bb309-115">Chcete-li vytvořit aplikaci Blazor pomocí modelu hostování na straně klienta, použijte šablonu **aplikaceBlazor WebAssembly** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="bb309-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="bb309-116">Po výběru šablony **aplikaceBlazor WebAssembly** máte možnost konfigurovat aplikaci tak, aby používala ASP.NET Core back-end, a to tak, že vyberete zaškrtávací políčko **ASP.NET Core hostované** ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="bb309-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="bb309-117">Aplikace ASP.NET Core obsluhuje aplikaci Blazor klientům.</span><span class="sxs-lookup"><span data-stu-id="bb309-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="bb309-118">Aplikace Blazor WebAssembly může komunikovat se serverem přes síť pomocí volání webového rozhraní API nebo [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="bb309-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="bb309-119">Šablony obsahují skript *blazor. WebAssembly. js* , který zpracovává:</span><span class="sxs-lookup"><span data-stu-id="bb309-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="bb309-120">Stažení modulu runtime .NET, aplikace a závislostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="bb309-121">Inicializace modulu runtime pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="bb309-122">Model hostování Blazor WebAssembly nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="bb309-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="bb309-123">Neexistuje žádná závislost na straně serveru .NET.</span><span class="sxs-lookup"><span data-stu-id="bb309-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="bb309-124">Aplikace po stažení do klienta plně funguje.</span><span class="sxs-lookup"><span data-stu-id="bb309-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="bb309-125">Prostředky a možnosti klienta jsou plně využité.</span><span class="sxs-lookup"><span data-stu-id="bb309-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="bb309-126">Práce je ze serveru převedena na klienta.</span><span class="sxs-lookup"><span data-stu-id="bb309-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="bb309-127">Pro hostování aplikace není vyžadován ASP.NET Core webový server.</span><span class="sxs-lookup"><span data-stu-id="bb309-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="bb309-128">Jsou možné scénáře nasazení bez serveru (například poskytování aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="bb309-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="bb309-129">K Blazor hostování WebAssembly je downsides:</span><span class="sxs-lookup"><span data-stu-id="bb309-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="bb309-130">Aplikace je omezená na možnosti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="bb309-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="bb309-131">Je vyžadován klientský hardware a software (například podpora WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="bb309-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="bb309-132">Velikost ke stažení je větší a aplikace trvá déle, než se načtou.</span><span class="sxs-lookup"><span data-stu-id="bb309-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="bb309-133">Podpora modulu runtime .NET a nástrojů je méně vyspělá.</span><span class="sxs-lookup"><span data-stu-id="bb309-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="bb309-134">Například omezení existují v [.NET Standard](/dotnet/standard/net-standard) podpoře a ladění.</span><span class="sxs-lookup"><span data-stu-id="bb309-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="opno-locblazor-server"></a><span data-ttu-id="bb309-135">Server Blazor</span><span class="sxs-lookup"><span data-stu-id="bb309-135">Blazor Server</span></span>

<span data-ttu-id="bb309-136">S modelem hostování serveru Blazor se aplikace spouští na serveru z aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb309-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="bb309-137">Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu jsou zpracovávána prostřednictvím [SignalRho](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="bb309-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![Prohlížeč komunikuje s aplikací (hostovanou v aplikaci ASP.NET Core) na serveru přes [! Evřít. Připojení NO-LOC (Signal)]](hosting-models/_static/blazor-server.png)

<span data-ttu-id="bb309-139">Pokud chcete vytvořit aplikaci Blazor pomocí modelu hostování Blazor serveru, použijte šablonu aplikace ASP.NET Core **Blazor Server** ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="bb309-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="bb309-140">Aplikace ASP.NET Core hostuje aplikaci Blazor serveru a vytvoří SignalR koncový bod, ke kterému se klienti připojují.</span><span class="sxs-lookup"><span data-stu-id="bb309-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="bb309-141">ASP.NET Core aplikace odkazuje na třídu `Startup` aplikace, která se má přidat:</span><span class="sxs-lookup"><span data-stu-id="bb309-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="bb309-142">Služby na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="bb309-142">Server-side services.</span></span>
* <span data-ttu-id="bb309-143">Aplikace do kanálu pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="bb309-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="bb309-144">Skript *blazor. Server. js*&dagger; vytvoří připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="bb309-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="bb309-145">Je zodpovědností aplikace zachovat a obnovit stav aplikace podle potřeby (například v případě ztraceného síťového připojení).</span><span class="sxs-lookup"><span data-stu-id="bb309-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="bb309-146">Model hostování serveru Blazor nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="bb309-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="bb309-147">Velikost ke stažení je výrazně menší než Blazor aplikace WebAssembly a aplikace se načte mnohem rychleji.</span><span class="sxs-lookup"><span data-stu-id="bb309-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="bb309-148">Aplikace plně využívá možnosti serveru, včetně použití všech rozhraní API kompatibilních s rozhraním .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb309-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="bb309-149">Rozhraní .NET Core na serveru se používá ke spuštění aplikace, takže stávající nástroje .NET, jako je ladění, fungují podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="bb309-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="bb309-150">Podporují se tenké klienty.</span><span class="sxs-lookup"><span data-stu-id="bb309-150">Thin clients are supported.</span></span> <span data-ttu-id="bb309-151">Například Blazor serverové aplikace fungují s prohlížeči, které nepodporují WebAssembly a na zařízeních s omezením prostředků.</span><span class="sxs-lookup"><span data-stu-id="bb309-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="bb309-152">Základ pro .NET/C# kód aplikace, včetně kódu komponenty aplikace, není obsluhován klientům.</span><span class="sxs-lookup"><span data-stu-id="bb309-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="bb309-153">K dispozici je downsides Blazor hostování serveru:</span><span class="sxs-lookup"><span data-stu-id="bb309-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="bb309-154">Vyšší latence obvykle existuje.</span><span class="sxs-lookup"><span data-stu-id="bb309-154">Higher latency usually exists.</span></span> <span data-ttu-id="bb309-155">Každá interakce uživatele zahrnuje směrování sítě.</span><span class="sxs-lookup"><span data-stu-id="bb309-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="bb309-156">Neexistuje žádná podpora offline.</span><span class="sxs-lookup"><span data-stu-id="bb309-156">There's no offline support.</span></span> <span data-ttu-id="bb309-157">Pokud připojení klienta neproběhne úspěšně, aplikace přestane fungovat.</span><span class="sxs-lookup"><span data-stu-id="bb309-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="bb309-158">Pro aplikace s mnoha uživateli je škálovatelnost náročná.</span><span class="sxs-lookup"><span data-stu-id="bb309-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="bb309-159">Server musí spravovat více připojení klientů a zpracovávat stav klienta.</span><span class="sxs-lookup"><span data-stu-id="bb309-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="bb309-160">Pro obsluhu aplikace je vyžadován ASP.NET Core Server.</span><span class="sxs-lookup"><span data-stu-id="bb309-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="bb309-161">Scénáře nasazení bez serveru nejsou možné (například poskytování aplikace z CDN).</span><span class="sxs-lookup"><span data-stu-id="bb309-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="bb309-162">&dagger;skript *blazor. Server. js* se obsluhuje z vloženého prostředku ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bb309-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="bb309-163">Porovnání s uživatelským rozhraním vykresleným serverem</span><span class="sxs-lookup"><span data-stu-id="bb309-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="bb309-164">Jedním ze způsobů, jak pochopit Blazor serverových aplikací, je pochopit, jak se liší od tradičních modelů pro vykreslování uživatelského rozhraní v ASP.NET Core aplikacích, které používají zobrazení Razor nebo Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bb309-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="bb309-165">Oba modely používají jazyk Razor k popisu obsahu HTML, ale významně se liší v tom, jak se vykreslují kód.</span><span class="sxs-lookup"><span data-stu-id="bb309-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="bb309-166">Když je vykreslena stránka nebo zobrazení Razor, každý řádek kódu Razor generuje kód HTML v textovém formátu.</span><span class="sxs-lookup"><span data-stu-id="bb309-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="bb309-167">Po vykreslení Server uvolní instanci stránky nebo zobrazení, včetně veškerého stavu, který byl vytvořen.</span><span class="sxs-lookup"><span data-stu-id="bb309-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="bb309-168">Pokud dojde k jiné žádosti na stránku, například když se ověření serveru nepovede a zobrazí se shrnutí ověření:</span><span class="sxs-lookup"><span data-stu-id="bb309-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="bb309-169">Celá stránka se znovu vykreslí do textu HTML.</span><span class="sxs-lookup"><span data-stu-id="bb309-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="bb309-170">Stránka je odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="bb309-170">The page is sent to the client.</span></span>

<span data-ttu-id="bb309-171">Blazor aplikace se skládá z opakovaně použitelných prvků uživatelského rozhraní s názvem *Components*.</span><span class="sxs-lookup"><span data-stu-id="bb309-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="bb309-172">Komponenta obsahuje C# kód, značku a další komponenty.</span><span class="sxs-lookup"><span data-stu-id="bb309-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="bb309-173">Když je vykreslena komponenta, Blazor vytvoří graf zahrnutých součástí, podobně jako HTML nebo XML model DOM (Document Object Model) (DOM).</span><span class="sxs-lookup"><span data-stu-id="bb309-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="bb309-174">Tento graf obsahuje stav součásti uložený ve vlastnostech a polích.</span><span class="sxs-lookup"><span data-stu-id="bb309-174">This graph includes component state held in properties and fields.</span></span> Blazor<span data-ttu-id="bb309-175"> vyhodnotí graf komponent pro vytvoření binární reprezentace kódu.</span><span class="sxs-lookup"><span data-stu-id="bb309-175"> evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="bb309-176">Binární formát může být:</span><span class="sxs-lookup"><span data-stu-id="bb309-176">The binary format can be:</span></span>

* <span data-ttu-id="bb309-177">Byl převeden na text HTML (během předvykreslování).</span><span class="sxs-lookup"><span data-stu-id="bb309-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="bb309-178">Slouží k efektivní aktualizaci značek během pravidelného vykreslování.</span><span class="sxs-lookup"><span data-stu-id="bb309-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="bb309-179">Aktualizace uživatelského rozhraní v Blazor je aktivována pomocí:</span><span class="sxs-lookup"><span data-stu-id="bb309-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="bb309-180">Interakce s uživatelem, například výběr tlačítka.</span><span class="sxs-lookup"><span data-stu-id="bb309-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="bb309-181">Aktivační události aplikace, jako je například časovač.</span><span class="sxs-lookup"><span data-stu-id="bb309-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="bb309-182">Graf se znovu vykreslí a počítá se *rozdíl* v uživatelském rozhraní (rozdíl).</span><span class="sxs-lookup"><span data-stu-id="bb309-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="bb309-183">Tento rozdíl je nejmenší sada úprav modelu DOM, která je nutná k aktualizaci uživatelského rozhraní na klientovi.</span><span class="sxs-lookup"><span data-stu-id="bb309-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="bb309-184">Rozdíl se pošle klientovi v binárním formátu a použije ho prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="bb309-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="bb309-185">Komponenta je uvolněna poté, co uživatel z něj přejde na klienta.</span><span class="sxs-lookup"><span data-stu-id="bb309-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="bb309-186">I když uživatel pracuje s komponentou, musí se stav komponenty (služby, prostředky) uchovávat v paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="bb309-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="bb309-187">Vzhledem k tomu, že stav mnoha součástí může být serverem současně udržován, vyčerpání paměti je problém, který je třeba řešit.</span><span class="sxs-lookup"><span data-stu-id="bb309-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="bb309-188">Pokyny k vytvoření aplikace Blazor serveru pro zajištění nejlepšího využití paměti serveru najdete v tématu <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="bb309-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="circuits"></a><span data-ttu-id="bb309-189">Spoj</span><span class="sxs-lookup"><span data-stu-id="bb309-189">Circuits</span></span>

<span data-ttu-id="bb309-190">Aplikace Blazor serveru je postavená nad [ASP.NET Core SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="bb309-190">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="bb309-191">Každý klient komunikuje se serverem přes jedno nebo více SignalR připojení s názvem *okruh*.</span><span class="sxs-lookup"><span data-stu-id="bb309-191">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="bb309-192">Okruh je Blazorabstrakce prostřednictvím SignalR připojení, která mohou tolerovat dočasné síťové přerušení.</span><span class="sxs-lookup"><span data-stu-id="bb309-192">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="bb309-193">Když klient Blazor uvidí, že připojení SignalR je odpojené, pokusí se znovu připojit k serveru pomocí nového SignalR připojení.</span><span class="sxs-lookup"><span data-stu-id="bb309-193">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="bb309-194">Každá obrazovka prohlížeče (karta prohlížeče nebo IFRAME), která je připojená k aplikaci Blazor serveru, používá SignalR připojení.</span><span class="sxs-lookup"><span data-stu-id="bb309-194">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="bb309-195">Toto je ještě další důležité rozdíly ve srovnání s typickými aplikacemi vygenerovanými serverem.</span><span class="sxs-lookup"><span data-stu-id="bb309-195">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="bb309-196">V aplikaci vykreslené serverem se při otevření stejné aplikace v několika obrazovkách prohlížeče obvykle nepřevádí na další požadavky na prostředky na serveru.</span><span class="sxs-lookup"><span data-stu-id="bb309-196">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="bb309-197">V aplikaci Blazor serveru vyžaduje Každá obrazovka prohlížeče samostatný okruh a samostatné instance stavu součásti, které má server spravovat.</span><span class="sxs-lookup"><span data-stu-id="bb309-197">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

Blazor<span data-ttu-id="bb309-198"> se domnívá zavřít kartu prohlížeče nebo přejít na externí adresu URL s *řádným* ukončením.</span><span class="sxs-lookup"><span data-stu-id="bb309-198"> considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="bb309-199">V případě řádného ukončení se okruh a přidružené prostředky ihned uvolňují.</span><span class="sxs-lookup"><span data-stu-id="bb309-199">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="bb309-200">Klient se může také bez problémů odpojit, například kvůli přerušení sítě.</span><span class="sxs-lookup"><span data-stu-id="bb309-200">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> Blazor<span data-ttu-id="bb309-201"> server ukládá odpojené okruhy po Konfigurovatelný interval, aby se klient mohl znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="bb309-201"> Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="bb309-202">Další informace najdete v části [opětovné připojení ke stejnému serveru](#reconnection-to-the-same-server) .</span><span class="sxs-lookup"><span data-stu-id="bb309-202">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="bb309-203">Latence uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="bb309-203">UI Latency</span></span>

<span data-ttu-id="bb309-204">Latence uživatelského rozhraní je doba, kterou trvá od iniciované akce až do doby, kdy je uživatelské rozhraní aktualizováno.</span><span class="sxs-lookup"><span data-stu-id="bb309-204">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="bb309-205">Menší hodnoty latence uživatelského rozhraní jsou pro aplikaci pro reakci na uživatele naprosto nezbytné.</span><span class="sxs-lookup"><span data-stu-id="bb309-205">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="bb309-206">V aplikaci Blazor serveru se všechny akce odesílají na server, zpracováno a rozdíl v uživatelském rozhraní se pošle zpátky.</span><span class="sxs-lookup"><span data-stu-id="bb309-206">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="bb309-207">V důsledku toho je latence uživatelského rozhraní součtem latence sítě a latencí serveru při zpracování akce.</span><span class="sxs-lookup"><span data-stu-id="bb309-207">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="bb309-208">Pro podnikovou aplikaci, která je omezená jenom na soukromou podnikovou síť, se většinou neprojeví vlivu na vnímání uživatele latence kvůli latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="bb309-208">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="bb309-209">V případě aplikace nasazené přes Internet může latence znamenat uživatele, zejména v případě, že uživatelé jsou geograficky distribuováni podrobněji.</span><span class="sxs-lookup"><span data-stu-id="bb309-209">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="bb309-210">Využití paměti může také přispět k latenci aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-210">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="bb309-211">Zvýšení využití paměti vede k častému uvolňování paměti nebo paměti stránkování na disk, přičemž obě tyto čítače zvyšují výkon aplikace a následně zvyšují latenci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bb309-211">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="bb309-212">Další informace najdete v tématu <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="bb309-212">For more information, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="bb309-213">aplikace Blazor Server by měly být optimalizované pro minimalizaci latence uživatelského rozhraní tím, že se sníží latence sítě a využití paměti.</span><span class="sxs-lookup"><span data-stu-id="bb309-213">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="bb309-214">Přístup k měření latence sítě najdete v tématu <xref:host-and-deploy/blazor/server#measure-network-latency>.</span><span class="sxs-lookup"><span data-stu-id="bb309-214">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="bb309-215">Další informace o SignalR a Blazornajdete v těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="bb309-215">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a><span data-ttu-id="bb309-216">Připojení k serveru</span><span class="sxs-lookup"><span data-stu-id="bb309-216">Connection to the server</span></span>

<span data-ttu-id="bb309-217">aplikace Blazor serveru vyžadují připojení k serveru aktivní SignalR.</span><span class="sxs-lookup"><span data-stu-id="bb309-217">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="bb309-218">Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru.</span><span class="sxs-lookup"><span data-stu-id="bb309-218">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="bb309-219">Pokud je stav klienta stále v paměti, klientská relace pokračuje bez ztráty stavu.</span><span class="sxs-lookup"><span data-stu-id="bb309-219">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

#### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="bb309-220">Opětovné připojení ke stejnému serveru</span><span class="sxs-lookup"><span data-stu-id="bb309-220">Reconnection to the same server</span></span>

<span data-ttu-id="bb309-221">Aplikace Blazor serveru se předem vykreslí v reakci na první požadavek klienta, který nastaví stav uživatelského rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="bb309-221">A Blazor Server app prerenders in response to the first client request, which sets up the UI state on the server.</span></span> <span data-ttu-id="bb309-222">Když se klient pokusí vytvořit připojení SignalR, klient se musí znovu připojit ke stejnému serveru.</span><span class="sxs-lookup"><span data-stu-id="bb309-222">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> <span data-ttu-id="bb309-223">aplikace Blazor serveru, které používají více než jeden back-end Server, by měly implementovat *rychlé relace* pro SignalR připojení.</span><span class="sxs-lookup"><span data-stu-id="bb309-223">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="bb309-224">Pro Blazor serverových aplikací doporučujeme používat [službu Azure SignalR](/azure/azure-signalr) .</span><span class="sxs-lookup"><span data-stu-id="bb309-224">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="bb309-225">Služba umožňuje horizontální navýšení kapacity aplikace Blazor serveru na velký počet souběžných připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="bb309-225">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="bb309-226">Rychlé relace jsou povolené pro službu Azure SignalR tím, že nastavíte možnost `ServerStickyMode` služby nebo hodnotu konfigurace na `Required`.</span><span class="sxs-lookup"><span data-stu-id="bb309-226">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="bb309-227">Další informace najdete v tématu <xref:host-and-deploy/blazor/server#signalr-configuration>.</span><span class="sxs-lookup"><span data-stu-id="bb309-227">For more information, see <xref:host-and-deploy/blazor/server#signalr-configuration>.</span></span>

<span data-ttu-id="bb309-228">Při použití služby IIS jsou v rámci směrování žádostí na aplikace povoleny rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="bb309-228">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="bb309-229">Další informace najdete v tématu [Vyrovnávání zatížení HTTP pomocí směrování žádostí na aplikace](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="bb309-229">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="bb309-230">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="bb309-230">Reflect the connection state in the UI</span></span>

<span data-ttu-id="bb309-231">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="bb309-231">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="bb309-232">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="bb309-232">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="bb309-233">Chcete-li přizpůsobit uživatelské rozhraní, definujte element s `id` `components-reconnect-modal` v `<body>` stránky *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="bb309-233">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```html
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="bb309-234">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="bb309-234">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="bb309-235">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="bb309-235">CSS class</span></span>                       | <span data-ttu-id="bb309-236">Indikuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="bb309-236">Indicates&hellip;</span></span> |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | <span data-ttu-id="bb309-237">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="bb309-237">A lost connection.</span></span> <span data-ttu-id="bb309-238">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="bb309-238">The client is attempting to reconnect.</span></span> <span data-ttu-id="bb309-239">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="bb309-239">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="bb309-240">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="bb309-240">An active connection is re-established to the server.</span></span> <span data-ttu-id="bb309-241">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="bb309-241">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="bb309-242">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="bb309-242">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="bb309-243">Chcete-li se pokusit o opětovné připojení, zavolejte `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="bb309-243">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="bb309-244">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="bb309-244">Reconnection rejected.</span></span> <span data-ttu-id="bb309-245">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="bb309-245">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="bb309-246">Chcete-li aplikaci znovu načíst, zavolejte `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="bb309-246">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="bb309-247">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="bb309-247">This connection state may result when:</span></span><ul><li><span data-ttu-id="bb309-248">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="bb309-248">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="bb309-249">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="bb309-249">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="bb309-250">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="bb309-250">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="bb309-251">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="bb309-251">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="bb309-252">Stav opětovného připojení po předvykreslování</span><span class="sxs-lookup"><span data-stu-id="bb309-252">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="bb309-253">aplikace Blazor serveru se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="bb309-253">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="bb309-254">To je nastaveno na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="bb309-254">This is set up in the *_Host.cshtml* Razor page:</span></span>

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

<span data-ttu-id="bb309-255">`RenderMode` nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="bb309-255">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="bb309-256">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="bb309-256">Is prerendered into the page.</span></span>
* <span data-ttu-id="bb309-257">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="bb309-257">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="bb309-258">Popis</span><span class="sxs-lookup"><span data-stu-id="bb309-258">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="bb309-259">Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="bb309-259">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="bb309-260">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-260">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="bb309-261">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="bb309-261">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="bb309-262">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="bb309-262">Output from the component isn't included.</span></span> <span data-ttu-id="bb309-263">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-263">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="bb309-264">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="bb309-264">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="bb309-265">Popis</span><span class="sxs-lookup"><span data-stu-id="bb309-265">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="bb309-266">Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="bb309-266">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="bb309-267">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-267">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="bb309-268">Parametry nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="bb309-268">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="bb309-269">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="bb309-269">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="bb309-270">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="bb309-270">Output from the component isn't included.</span></span> <span data-ttu-id="bb309-271">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-271">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="bb309-272">Parametry nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="bb309-272">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="bb309-273">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="bb309-273">Renders the component into static HTML.</span></span> <span data-ttu-id="bb309-274">Jsou podporovány parametry.</span><span class="sxs-lookup"><span data-stu-id="bb309-274">Parameters are supported.</span></span> |

::: moniker-end

<span data-ttu-id="bb309-275">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="bb309-275">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="bb309-276">Když je `ServerPrerendered``RenderMode`, komponenta se zpočátku generuje jako součást stránky staticky.</span><span class="sxs-lookup"><span data-stu-id="bb309-276">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="bb309-277">Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní.</span><span class="sxs-lookup"><span data-stu-id="bb309-277">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="bb309-278">Pokud je k dispozici metoda inicializace životního cyklu "inicializujd [{Async}](xref:blazor/lifecycle#component-initialization-methods) " pro inicializaci komponenty, je metoda provedena *dvakrát*:</span><span class="sxs-lookup"><span data-stu-id="bb309-278">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="bb309-279">Když se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="bb309-279">When the component is prerendered statically.</span></span>
* <span data-ttu-id="bb309-280">Po navázání připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="bb309-280">After the server connection has been established.</span></span>

<span data-ttu-id="bb309-281">Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.</span><span class="sxs-lookup"><span data-stu-id="bb309-281">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="bb309-282">Abyste se vyhnuli scénáři dvojího vykreslování v aplikaci Blazor serveru:</span><span class="sxs-lookup"><span data-stu-id="bb309-282">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="bb309-283">Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb309-283">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="bb309-284">Použijte identifikátor při předvykreslování k uložení stavu součásti.</span><span class="sxs-lookup"><span data-stu-id="bb309-284">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="bb309-285">Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="bb309-285">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="bb309-286">Následující kód demonstruje aktualizované `WeatherForecastService` v aplikaci Blazor serveru založeném na šablonách, která vylučuje dvojité vykreslování:</span><span class="sxs-lookup"><span data-stu-id="bb309-286">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="bb309-287">Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="bb309-287">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="bb309-288">Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="bb309-288">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="bb309-289">Při vykreslení stránky nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="bb309-289">When the page or view renders:</span></span>

* <span data-ttu-id="bb309-290">Komponenta je předem vykreslena se stránkou nebo zobrazením.</span><span class="sxs-lookup"><span data-stu-id="bb309-290">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="bb309-291">Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="bb309-291">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="bb309-292">Po navázání SignalRho připojení se vytvoří nový stav součásti.</span><span class="sxs-lookup"><span data-stu-id="bb309-292">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="bb309-293">Následující stránka Razor vykresluje komponentu `Counter`:</span><span class="sxs-lookup"><span data-stu-id="bb309-293">The following Razor page renders a `Counter` component:</span></span>

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

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="bb309-294">Vykreslování neinteraktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="bb309-294">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="bb309-295">Na následující stránce Razor je součást `MyComponent` staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="bb309-295">In the following Razor page, the `MyComponent` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="bb309-296">Vzhledem k tomu, že `MyComponent` jsou staticky vykresleny, nemůže být komponenta interaktivní.</span><span class="sxs-lookup"><span data-stu-id="bb309-296">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="bb309-297">Rozpoznat, kdy se aplikace předvykresluje</span><span class="sxs-lookup"><span data-stu-id="bb309-297">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="bb309-298">Konfigurace klienta SignalR pro aplikace Blazor serveru</span><span class="sxs-lookup"><span data-stu-id="bb309-298">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="bb309-299">V některých případech je třeba nakonfigurovat klienta SignalR používaného serverovými aplikacemi Blazor.</span><span class="sxs-lookup"><span data-stu-id="bb309-299">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="bb309-300">Například můžete chtít nakonfigurovat protokolování klienta SignalR pro diagnostiku potíží s připojením.</span><span class="sxs-lookup"><span data-stu-id="bb309-300">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="bb309-301">Konfigurace klienta SignalR v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bb309-301">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="bb309-302">Přidejte atribut `autostart="false"` do značky `<script>` pro skript *blazor. Server. js* .</span><span class="sxs-lookup"><span data-stu-id="bb309-302">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="bb309-303">Zavolejte `Blazor.start` a předejte objekt konfigurace, který určuje SignalR Builder.</span><span class="sxs-lookup"><span data-stu-id="bb309-303">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="bb309-304">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="bb309-304">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
