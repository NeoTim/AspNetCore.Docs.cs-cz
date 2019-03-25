---
title: Hostování modely součásti syntaxe Razor
author: guardrex
description: Vysvětlení Blazor na straně klienta a ASP.NET Core Razor součástmi hostování modely.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/hosting-models
ms.openlocfilehash: efb96ab628cb643d3fb6d221f68758039b195d47
ms.sourcegitcommit: 7d6019f762fc5b8cbedcd69801e8310f51a17c18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419356"
---
# <a name="razor-components-hosting-models"></a><span data-ttu-id="dfe6d-103">Hostování modely součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="dfe6d-103">Razor Components hosting models</span></span>

<span data-ttu-id="dfe6d-104">Podle [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="dfe6d-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="dfe6d-105">Součásti Razor je webová architektura navržený pro běh na straně klienta v prohlížeči na základě WebAssembly .NET runtime (*Blazor*) nebo na serveru ASP.NET Core (*ASP.NET Core Razor komponenty*).</span><span class="sxs-lookup"><span data-stu-id="dfe6d-105">Razor Components is a web framework designed to run client-side in the browser on a WebAssembly-based .NET runtime (*Blazor*) or server-side in ASP.NET Core (*ASP.NET Core Razor Components*).</span></span> <span data-ttu-id="dfe6d-106">Bez ohledu na modelech hostování modelu, aplikace a komponenty *zůstávají stejné*.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-106">Regardless of the hosting model, the app and component models *remain the same*.</span></span> <span data-ttu-id="dfe6d-107">Tento článek popisuje dostupné modelech hostování.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-107">This article discusses the available hosting models.</span></span>

## <a name="client-side-hosting-model"></a><span data-ttu-id="dfe6d-108">Model hostingu na straně klienta</span><span class="sxs-lookup"><span data-stu-id="dfe6d-108">Client-side hosting model</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="dfe6d-109">Hlavní model hostingu pro Blazor je spuštěné straně klienta v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-109">The principal hosting model for Blazor is running client-side in the browser.</span></span> <span data-ttu-id="dfe6d-110">V tomto modelu aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-110">In this model, the Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="dfe6d-111">Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="dfe6d-112">Všechny aktualizace uživatelského rozhraní a zpracování událostí se stane v rámci stejného procesu.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-112">All UI updates and event handling happens within the same process.</span></span> <span data-ttu-id="dfe6d-113">Prostředky aplikace můžete nasadit jako statické soubory pomocí libovolné webový server je upřednostňovaný (viz [hostitele a nasadit](xref:host-and-deploy/razor-components/index)).</span><span class="sxs-lookup"><span data-stu-id="dfe6d-113">The app assets can be deployed as static files using whatever web server is preferred (see [Host and deploy](xref:host-and-deploy/razor-components/index)).</span></span>

![Blazor straně klienta: Blazor aplikace běží na vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/client-side.png)

<span data-ttu-id="dfe6d-115">Chcete-li vytvořit aplikaci Blazor používá model hostování na straně klienta, použijte **Blazor** nebo **Blazor (ASP.NET Core v prostředí)** šablony projektů (`blazor` nebo `blazorhosted` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkazu na příkazovém řádku).</span><span class="sxs-lookup"><span data-stu-id="dfe6d-115">To create a Blazor app using the client-side hosting model, use the **Blazor** or **Blazor (ASP.NET Core Hosted)** project templates (`blazor` or `blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command at a command prompt).</span></span> <span data-ttu-id="dfe6d-116">Zahrnout *components.webassembly.js* skriptu obslužné rutiny:</span><span class="sxs-lookup"><span data-stu-id="dfe6d-116">The included *components.webassembly.js* script handles:</span></span>

* <span data-ttu-id="dfe6d-117">Stahuje se modul .NET runtime, aplikace a jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-117">Downloading the .NET runtime, the app, and its dependencies.</span></span>
* <span data-ttu-id="dfe6d-118">Inicializace modulu runtime a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-118">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="dfe6d-119">Model hostingu na straně klienta nabízí několik výhod.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-119">The client-side hosting model offers several benefits.</span></span> <span data-ttu-id="dfe6d-120">Blazor na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="dfe6d-120">Client-side Blazor:</span></span>

* <span data-ttu-id="dfe6d-121">Nemá žádné závislosti .NET na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-121">Has no .NET server-side dependency.</span></span>
* <span data-ttu-id="dfe6d-122">Má bohaté interaktivní uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-122">Has a rich interactive UI.</span></span>
* <span data-ttu-id="dfe6d-123">Plně využívá prostředky klienta a možnosti.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-123">Fully leverages client resources and capabilities.</span></span>
* <span data-ttu-id="dfe6d-124">Snižování zátěže pracovat ze serveru do klienta.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-124">Offloads work from the server to the client.</span></span>
* <span data-ttu-id="dfe6d-125">Podporuje scénáře v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-125">Supports offline scenarios.</span></span>

<span data-ttu-id="dfe6d-126">Existují nevýhody hostování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-126">There are downsides to client-side hosting.</span></span> <span data-ttu-id="dfe6d-127">Blazor na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="dfe6d-127">Client-side Blazor:</span></span>

* <span data-ttu-id="dfe6d-128">Omezí aplikace funkcí v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-128">Restricts the app to the capabilities of the browser.</span></span>
* <span data-ttu-id="dfe6d-129">Vyžaduje klienta s podporou, hardware a software (třeba podporu WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="dfe6d-129">Requires capable client hardware and software (for example, WebAssembly support).</span></span>
* <span data-ttu-id="dfe6d-130">Má větší velikost ke stažení a delší dobu načítání aplikace.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-130">Has a larger download size and longer app load time.</span></span>
* <span data-ttu-id="dfe6d-131">Má menší vyspělejší modulu runtime .NET a nástroje podpory (třeba omezení v .NET Standard support a ladění).</span><span class="sxs-lookup"><span data-stu-id="dfe6d-131">Has less mature .NET runtime and tooling support (for example, limitations in .NET Standard support and debugging).</span></span>

<span data-ttu-id="dfe6d-132">Visual Studio obsahuje **Blazor (ASP.NET Core hostované)** šablona projektu pro vytvoření Blazor aplikace, která běží na WebAssembly a je hostovaná na serveru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-132">Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template for creating a Blazor app that runs on WebAssembly and is hosted on an ASP.NET Core server.</span></span> <span data-ttu-id="dfe6d-133">Aplikace ASP.NET Core obsluhuje Blazor aplikaci pro klienty, ale jinak je samostatný proces.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-133">The ASP.NET Core app serves the Blazor app to clients but is otherwise a separate process.</span></span> <span data-ttu-id="dfe6d-134">Aplikace na straně klienta Blazor můžete spolupracovat se serverem přes síť pomocí volání webového rozhraní API nebo připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-134">The client-side Blazor app can interact with the server over the network using Web API calls or SignalR connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dfe6d-135">Pokud aplikace na straně klienta Blazor slouží jako sub aplikace IIS hostované aplikace ASP.NET Core, zakažte zděděné obslužnou rutinu modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-135">If a client-side Blazor app is served by an ASP.NET Core app hosted as an IIS sub-app, disable the inherited ASP.NET Core Module handler.</span></span> <span data-ttu-id="dfe6d-136">Nastavení základní cesty aplikace v aplikaci Blazor *index.html* soubor do služby IIS alias používaný při konfiguraci podřízeným aplikacím ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-136">Set the app base path in the Blazor app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>
>
> <span data-ttu-id="dfe6d-137">Další informace najdete v tématu [základní cesty aplikace](xref:host-and-deploy/razor-components/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="dfe6d-137">For more information, see [App base path](xref:host-and-deploy/razor-components/index#app-base-path).</span></span>

## <a name="server-side-hosting-model"></a><span data-ttu-id="dfe6d-138">Model hostingu na straně serveru</span><span class="sxs-lookup"><span data-stu-id="dfe6d-138">Server-side hosting model</span></span>

<span data-ttu-id="dfe6d-139">V ASP.NET Core Razor komponenty na straně serveru model hostingu aplikace provádí na serveru z v rámci aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-139">In the ASP.NET Core Razor Components server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="dfe6d-140">Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-140">UI updates, event handling, and JavaScript calls are handled over a SignalR connection.</span></span>

![ASP.NET Core Razor součásti serverové: V prohlížeči komunikuje s aplikaci (už je hostovaná v rámci aplikace ASP.NET Core) na serveru pomocí připojení SignalR.](hosting-models/_static/server-side.png)

<span data-ttu-id="dfe6d-142">Vytváření Razor součásti aplikace pomocí modelu hostingu na straně serveru, používat ASP.NET Core **Razor komponenty** šablony (`razorcomponents` při použití [dotnet nové](/dotnet/core/tools/dotnet-new) z příkazového řádku).</span><span class="sxs-lookup"><span data-stu-id="dfe6d-142">To create a Razor Components app using the server-side hosting model, use the ASP.NET Core **Razor Components** template (`razorcomponents` when using [dotnet new](/dotnet/core/tools/dotnet-new) at a command prompt).</span></span> <span data-ttu-id="dfe6d-143">Aplikace ASP.NET Core hostitelem aplikace Razor komponenty na straně serveru a nastaví koncových bodů SignalR, ve kterém se klienti připojují.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-143">An ASP.NET Core app hosts the Razor Components server-side app and sets up the SignalR endpoint where clients connect.</span></span> <span data-ttu-id="dfe6d-144">Aplikace ASP.NET Core odkazuje aplikaci `Startup` třídy přidejte:</span><span class="sxs-lookup"><span data-stu-id="dfe6d-144">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="dfe6d-145">Služby Razor komponenty na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-145">Server-side Razor Components services.</span></span>
* <span data-ttu-id="dfe6d-146">Aplikace na žádost o zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-146">The app to the request handling pipeline.</span></span>

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

<span data-ttu-id="dfe6d-147">*Components.server.js* skript&dagger; naváže připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-147">The *components.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="dfe6d-148">Je zodpovědností aplikace k zachování a obnovení stavu aplikace podle potřeby (například v případě ztráty připojení).</span><span class="sxs-lookup"><span data-stu-id="dfe6d-148">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="dfe6d-149">Model hostingu na straně serveru nabízí několik výhod:</span><span class="sxs-lookup"><span data-stu-id="dfe6d-149">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="dfe6d-150">Umožňuje napsat celou aplikaci s využitím .NET a C# pomocí komponenty modelu.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-150">Allows you to write your entire app with .NET and C# using the component model.</span></span>
* <span data-ttu-id="dfe6d-151">Poskytuje bohaté interaktivní chování a zabraňuje zbytečným stránka se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-151">Provides a rich interactive feel and avoids unnecessary page refreshes.</span></span>
* <span data-ttu-id="dfe6d-152">Má výrazného zmenšení velikosti aplikace než Blazor aplikace na straně klienta a načte mnohem rychleji.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-152">Has a significantly smaller app size than a client-side Blazor app and loads much faster.</span></span>
* <span data-ttu-id="dfe6d-153">Součástí logiky můžete plně využít serverových funkcí, včetně použití libovolné rozhraní API .NET Core kompatibilní.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-153">Component logic can take full advantage of server capabilities, including using any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="dfe6d-154">V rozhraní .NET Core běží na serveru, takže existující .NET nástrojů, jako je ladění, funguje podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-154">Runs on .NET Core on the server, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="dfe6d-155">Funguje s tencí klienti (například prohlížeče, které nepodporují WebAssembly a prostředků omezené zařízení).</span><span class="sxs-lookup"><span data-stu-id="dfe6d-155">Works with thin clients (for example, browsers that don't support WebAssembly and resource constrained devices).</span></span>

<span data-ttu-id="dfe6d-156">Existují nevýhody hostování na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="dfe6d-156">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="dfe6d-157">Má vyšší latence: Každá interakce uživatele zahrnuje směrování v síti.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-157">Has higher latency: Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="dfe6d-158">Nabízí nepodporuje offline: Pokud klienta nepovede, aplikace přestane fungovat.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-158">Offers no offline support: If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="dfe6d-159">Má omezit škálovatelnost: Server musí spravovat připojení více klientů a zpracování stavu klienta.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-159">Has reduced scalability: The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="dfe6d-160">Vyžaduje server služby ASP.NET Core pro obsluhu aplikace.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-160">Requires an ASP.NET Core server to serve the app.</span></span> <span data-ttu-id="dfe6d-161">Nasazení bez serveru (například ze sítě CDN) není možné.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-161">Deployment without a server (for example, from a CDN) isn't possible.</span></span>

<span data-ttu-id="dfe6d-162">&dagger;*Components.server.js* do následujícího umístění je publikován skriptu: *bin / {ladění | Verze} / {CÍLOVÁ ARCHITEKTURA} /publish/ {název aplikace}. Aplikace/dist/_architektura*.</span><span class="sxs-lookup"><span data-stu-id="dfe6d-162">&dagger;The *components.server.js* script is published to the following path: *bin/{Debug|Release}/{TARGET FRAMEWORK}/publish/{APPLICATION NAME}.App/dist/_framework*.</span></span>
