---
title: Blazor modelech hostování
author: guardrex
description: Vysvětlení Blazor na straně klienta a ASP.NET Core Razor součástmi hostování modely.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: blazor/hosting-models
ms.openlocfilehash: 0e7598c9f27201a989d1088764e09461a37beae4
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614780"
---
# <a name="blazor-hosting-models"></a><span data-ttu-id="8e840-103">Blazor modelech hostování</span><span class="sxs-lookup"><span data-stu-id="8e840-103">Blazor hosting models</span></span>

<span data-ttu-id="8e840-104">Podle [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8e840-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="8e840-105">Blazor je webové rozhraní navržené ke spuštění na straně klienta v prohlížeči na [WebAssembly](http://webassembly.org/)– na základě modulu runtime .NET (*Blazor*) nebo na serveru ASP.NET Core (*ASP.NET Core Razor součásti*).</span><span class="sxs-lookup"><span data-stu-id="8e840-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](http://webassembly.org/)-based .NET runtime (*Blazor*) or server-side in ASP.NET Core (*ASP.NET Core Razor Components*).</span></span> <span data-ttu-id="8e840-106">Bez ohledu na modelech hostování modelu, aplikace a komponenty *zůstávají stejné*.</span><span class="sxs-lookup"><span data-stu-id="8e840-106">Regardless of the hosting model, the app and component models *remain the same*.</span></span> <span data-ttu-id="8e840-107">Tento článek popisuje dostupné modelech hostování:</span><span class="sxs-lookup"><span data-stu-id="8e840-107">This article discusses the available hosting models:</span></span>

* [<span data-ttu-id="8e840-108">Blazor na straně klienta</span><span class="sxs-lookup"><span data-stu-id="8e840-108">Client-side Blazor</span></span>](#client-side-hosting-model)
* [<span data-ttu-id="8e840-109">Komponenty na straně serveru ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="8e840-109">Server-side ASP.NET Core Razor Components</span></span>](#server-side-hosting-model)

## <a name="client-side-hosting-model"></a><span data-ttu-id="8e840-110">Model hostingu na straně klienta</span><span class="sxs-lookup"><span data-stu-id="8e840-110">Client-side hosting model</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="8e840-111">Hlavní model hostingu pro Blazor je spuštěné v prohlížeči na WebAssembly straně klienta.</span><span class="sxs-lookup"><span data-stu-id="8e840-111">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="8e840-112">Aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="8e840-112">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="8e840-113">Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8e840-113">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="8e840-114">Aktualizace uživatelského rozhraní a zpracování událostí dochází v rámci stejného procesu.</span><span class="sxs-lookup"><span data-stu-id="8e840-114">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="8e840-115">Prostředky aplikace se nasadí jako statické soubory na webový server nebo služba je schopná obsluhuje statický obsah, na klienty.</span><span class="sxs-lookup"><span data-stu-id="8e840-115">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor straně klienta: Blazor aplikace běží na vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/client-side.png)

<span data-ttu-id="8e840-117">Chcete-li vytvořit aplikaci Blazor používá model hostování na straně klienta, použijte jednu z následujících šablon:</span><span class="sxs-lookup"><span data-stu-id="8e840-117">To create a Blazor app using the client-side hosting model, use either of the following templates:</span></span>

* <span data-ttu-id="8e840-118">**Blazor** ([nové blazor dotnet](/dotnet/core/tools/dotnet-new)) &ndash; nasazený jako sada statické soubory.</span><span class="sxs-lookup"><span data-stu-id="8e840-118">**Blazor** ([dotnet new blazor](/dotnet/core/tools/dotnet-new)) &ndash; Deployed as a set of static files.</span></span>
* <span data-ttu-id="8e840-119">**Blazor (ASP.NET Core v prostředí)** ([nové blazorhosted dotnet](/dotnet/core/tools/dotnet-new)) &ndash; hostované serveru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8e840-119">**Blazor (ASP.NET Core Hosted)** ([dotnet new blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; Hosted by an ASP.NET Core server.</span></span> <span data-ttu-id="8e840-120">Aplikace ASP.NET Core obsluhuje Blazor aplikaci pro klienty.</span><span class="sxs-lookup"><span data-stu-id="8e840-120">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="8e840-121">Aplikace na straně klienta Blazor můžete spolupracovat se serverem přes síť pomocí volání webového rozhraní API nebo [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="8e840-121">The client-side Blazor app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="8e840-122">Šablony zahrnují *components.webassembly.js* skript, který zpracovává:</span><span class="sxs-lookup"><span data-stu-id="8e840-122">The templates include the *components.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="8e840-123">Stahuje se modul .NET runtime, aplikace a závislostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e840-123">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="8e840-124">Inicializace modulu runtime a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8e840-124">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="8e840-125">Model hostingu na straně klienta nabízí několik výhod.</span><span class="sxs-lookup"><span data-stu-id="8e840-125">The client-side hosting model offers several benefits.</span></span> <span data-ttu-id="8e840-126">Blazor na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="8e840-126">Client-side Blazor:</span></span>

* <span data-ttu-id="8e840-127">Nemá žádné závislosti .NET na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="8e840-127">Has no .NET server-side dependency.</span></span>
* <span data-ttu-id="8e840-128">Plně využívá prostředky klienta a možnosti.</span><span class="sxs-lookup"><span data-stu-id="8e840-128">Fully leverages client resources and capabilities.</span></span>
* <span data-ttu-id="8e840-129">Snižování zátěže pracovat ze serveru do klienta.</span><span class="sxs-lookup"><span data-stu-id="8e840-129">Offloads work from the server to the client.</span></span>
* <span data-ttu-id="8e840-130">Podporuje scénáře v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="8e840-130">Supports offline scenarios.</span></span>

<span data-ttu-id="8e840-131">Existují nevýhody hostování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="8e840-131">There are downsides to client-side hosting.</span></span> <span data-ttu-id="8e840-132">Blazor na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="8e840-132">Client-side Blazor:</span></span>

* <span data-ttu-id="8e840-133">Omezí aplikace funkcí v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="8e840-133">Restricts the app to the capabilities of the browser.</span></span>
* <span data-ttu-id="8e840-134">Vyžaduje klienta s podporou, hardware a software (třeba podporu WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="8e840-134">Requires capable client hardware and software (for example, WebAssembly support).</span></span>
* <span data-ttu-id="8e840-135">Má větší velikost ke stažení a delší dobu načítání aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e840-135">Has a larger download size and longer app load time.</span></span>
* <span data-ttu-id="8e840-136">Má menší modul .NET runtime a podpora nástrojů pro dospělé (například omezení [.NET Standard](/dotnet/standard/net-standard) podpory a ladění).</span><span class="sxs-lookup"><span data-stu-id="8e840-136">Has less mature .NET runtime and tooling support (for example, limitations in [.NET Standard](/dotnet/standard/net-standard) support and debugging).</span></span>

## <a name="server-side-hosting-model"></a><span data-ttu-id="8e840-137">Model hostingu na straně serveru</span><span class="sxs-lookup"><span data-stu-id="8e840-137">Server-side hosting model</span></span>

<span data-ttu-id="8e840-138">S model hostingu na straně serveru aplikace spuštěné na serveru z v rámci aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8e840-138">With the server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="8e840-139">Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="8e840-139">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![V prohlížeči komunikuje s aplikaci (už je hostovaná v rámci aplikace ASP.NET Core) na serveru pomocí připojení SignalR.](hosting-models/_static/server-side.png)

<span data-ttu-id="8e840-141">Pokud chcete vytvořit aplikaci Blazor používá model hostování na straně serveru, použijte technologii ASP.NET Core **Razor komponenty** šablony ([nové razorcomponents dotnet](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="8e840-141">To create a Blazor app using the server-side hosting model, use the ASP.NET Core **Razor Components** template ([dotnet new razorcomponents](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="8e840-142">Aplikace ASP.NET Core hostitelem aplikace Razor komponenty na straně serveru a nastaví koncových bodů SignalR, ve kterém se klienti připojují.</span><span class="sxs-lookup"><span data-stu-id="8e840-142">The ASP.NET Core app hosts the Razor Components server-side app and sets up the SignalR endpoint where clients connect.</span></span> <span data-ttu-id="8e840-143">Aplikace ASP.NET Core odkazuje aplikaci `Startup` třídy přidejte:</span><span class="sxs-lookup"><span data-stu-id="8e840-143">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="8e840-144">Služby Razor komponenty na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="8e840-144">Server-side Razor Components services.</span></span>
* <span data-ttu-id="8e840-145">Aplikace na žádost o zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="8e840-145">The app to the request handling pipeline.</span></span>

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

<span data-ttu-id="8e840-146">*Components.server.js* skript&dagger; naváže připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="8e840-146">The *components.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="8e840-147">Je zodpovědností aplikace k zachování a obnovení stavu aplikace podle potřeby (například v případě ztráty připojení).</span><span class="sxs-lookup"><span data-stu-id="8e840-147">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="8e840-148">Model hostingu na straně serveru nabízí několik výhod.</span><span class="sxs-lookup"><span data-stu-id="8e840-148">The server-side hosting model offers several benefits.</span></span> <span data-ttu-id="8e840-149">Komponenty na straně serveru Razor:</span><span class="sxs-lookup"><span data-stu-id="8e840-149">Server-side Razor Components:</span></span>

* <span data-ttu-id="8e840-150">Výrazného zmenšení velikosti aplikace než Blazor aplikace na straně klienta a načtou mnohem rychleji.</span><span class="sxs-lookup"><span data-stu-id="8e840-150">Have a significantly smaller app size than a client-side Blazor app and load much faster.</span></span>
* <span data-ttu-id="8e840-151">Můžete plně využít serverových funkcí, včetně použití libovolné rozhraní API .NET Core kompatibilní.</span><span class="sxs-lookup"><span data-stu-id="8e840-151">Can take full advantage of server capabilities, including using any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="8e840-152">Spusťte na .NET Core na serveru, takže existující .NET nástrojů, jako je ladění, funguje podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="8e840-152">Run on .NET Core on the server, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="8e840-153">Funguje s tencí klienti (například prohlížeče, které nepodporují WebAssembly a prostředků omezené zařízení).</span><span class="sxs-lookup"><span data-stu-id="8e840-153">Works with thin clients (for example, browsers that don't support WebAssembly and resource constrained devices).</span></span>
* <span data-ttu-id="8e840-154">.NET /C# základu kódu, včetně kódu komponenty aplikace, není poskytováni, na klienty.</span><span class="sxs-lookup"><span data-stu-id="8e840-154">.NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="8e840-155">Existují nevýhody hostování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="8e840-155">There are downsides to server-side hosting.</span></span> <span data-ttu-id="8e840-156">Komponenty na straně serveru Razor:</span><span class="sxs-lookup"><span data-stu-id="8e840-156">Server-side Razor Components:</span></span>

* <span data-ttu-id="8e840-157">Mají vyšší latence: Každá interakce uživatele zahrnuje směrování v síti.</span><span class="sxs-lookup"><span data-stu-id="8e840-157">Have higher latency: Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="8e840-158">Nabídky nepodporuje offline: Pokud klienta nepovede, aplikace přestane fungovat.</span><span class="sxs-lookup"><span data-stu-id="8e840-158">Offer no offline support: If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="8e840-159">Omezená škálovatelnost: Server musí spravovat připojení více klientů a zpracování stavu klienta.</span><span class="sxs-lookup"><span data-stu-id="8e840-159">Have reduced scalability: The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="8e840-160">Vyžadují ASP.NET Core server k obsluze aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e840-160">Require an ASP.NET Core server to serve the app.</span></span> <span data-ttu-id="8e840-161">Nasazení bez serveru (například ze sítě CDN) není možné.</span><span class="sxs-lookup"><span data-stu-id="8e840-161">Deployment without a server (for example, from a CDN) isn't possible.</span></span>

<span data-ttu-id="8e840-162">&dagger;*Components.server.js* do následujícího umístění je publikován skriptu: *bin / {ladění | Verze} / {CÍLOVÁ ARCHITEKTURA} /publish/ {název aplikace}. Aplikace/dist/_architektura*.</span><span class="sxs-lookup"><span data-stu-id="8e840-162">&dagger;The *components.server.js* script is published to the following path: *bin/{Debug|Release}/{TARGET FRAMEWORK}/publish/{APPLICATION NAME}.App/dist/_framework*.</span></span>
