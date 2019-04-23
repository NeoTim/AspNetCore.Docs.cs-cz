---
title: Úvod do Blazor v ASP.NET Core
author: guardrex
description: Prozkoumejte službu ASP.NET Core Blazor způsob, jak vytvářet interaktivní webové na straně klienta uživatelské rozhraní s využitím .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/18/2019
uid: blazor/index
ms.openlocfilehash: 74eeb003c249fc9ff8267ac855455f875806ccd9
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982994"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="97058-103">Úvod do Blazor</span><span class="sxs-lookup"><span data-stu-id="97058-103">Introduction to Blazor</span></span>

<span data-ttu-id="97058-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="97058-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="97058-105">Vítá vás Blazor!</span><span class="sxs-lookup"><span data-stu-id="97058-105">Welcome to Blazor!</span></span>

<span data-ttu-id="97058-106">Vytvářet interaktivní webové uživatelské rozhraní s .NET na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="97058-106">Build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="97058-107">Vytvářet bohaté interaktivní uživatelských rozhraní pomocí C# místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="97058-107">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="97058-108">Sdílet logiku na straně serveru a klientské aplikace napsané v rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="97058-108">Share server-side and client-side app logic written with .NET.</span></span>
* <span data-ttu-id="97058-109">Vykreslení uživatelského rozhraní jako HTML a CSS pro podporu široké prohlížeče, včetně mobilních.</span><span class="sxs-lookup"><span data-stu-id="97058-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="97058-110">Blazor podporuje většina aplikací vyžaduje základní scénáře:</span><span class="sxs-lookup"><span data-stu-id="97058-110">Blazor supports core scenarios required by most apps:</span></span>

* <span data-ttu-id="97058-111">Parametry</span><span class="sxs-lookup"><span data-stu-id="97058-111">Parameters</span></span>
* <span data-ttu-id="97058-112">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="97058-112">Event handling</span></span>
* <span data-ttu-id="97058-113">Vytváření datových vazeb</span><span class="sxs-lookup"><span data-stu-id="97058-113">Data binding</span></span>
* <span data-ttu-id="97058-114">Směrování</span><span class="sxs-lookup"><span data-stu-id="97058-114">Routing</span></span>
* <span data-ttu-id="97058-115">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="97058-115">Dependency injection</span></span>
* <span data-ttu-id="97058-116">Rozložení</span><span class="sxs-lookup"><span data-stu-id="97058-116">Layouts</span></span>
* <span data-ttu-id="97058-117">Šablony</span><span class="sxs-lookup"><span data-stu-id="97058-117">Templates</span></span>
* <span data-ttu-id="97058-118">Kaskádové hodnoty</span><span class="sxs-lookup"><span data-stu-id="97058-118">Cascading values</span></span>

## <a name="components"></a><span data-ttu-id="97058-119">Komponenty</span><span class="sxs-lookup"><span data-stu-id="97058-119">Components</span></span>

<span data-ttu-id="97058-120">A *komponenty* v Blazor je prvek uživatelského rozhraní, jako je například formulář položka stránky, dialogové okno nebo data.</span><span class="sxs-lookup"><span data-stu-id="97058-120">A *component* in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="97058-121">Součásti zpracovávat události uživatele a definovat flexibilní logiku pro vykreslení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="97058-121">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="97058-122">Součásti můžete vnořit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="97058-122">Components can be nested and reused.</span></span>

<span data-ttu-id="97058-123">Komponenty jsou součástí sestavení .NET, které lze sdílet a distribuovat jako balíčky NuGet třídy rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="97058-123">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="97058-124">Komponentní třída jsou obvykle zapsána ve formě značek stránky Razor s *.razor* příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="97058-124">The component class is usually written in the form of a Razor markup page with a *.razor* file extension.</span></span> <span data-ttu-id="97058-125">Součástí Blazor jsou někdy označovány jako součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="97058-125">Components in Blazor are sometimes referred to as Razor components.</span></span>

<span data-ttu-id="97058-126">[Razor](xref:mvc/views/razor) je syntaxe pro kombinování značka jazyka HTML s C# kódu.</span><span class="sxs-lookup"><span data-stu-id="97058-126">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="97058-127">Razor je navržená pro produktivitu vývojářů, umožňuje vývojářům přepínat mezi značky a C# ve stejném souboru s [IntelliSense](/visualstudio/ide/using-intellisense) podporovat.</span><span class="sxs-lookup"><span data-stu-id="97058-127">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="97058-128">Stránky Razor a MVC zobrazení také používají syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="97058-128">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="97058-129">Na rozdíl od Razor Pages a zobrazení MVC, která jsou postavené na modelu žádost odpověď, používají součásti speciálně pro zpracování sestavení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="97058-129">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="97058-130">Komponenty Razor můžete použít speciálně pro logika uživatelského rozhraní na straně klienta a skládání.</span><span class="sxs-lookup"><span data-stu-id="97058-130">Razor components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="97058-131">Následující kód je příklad vlastního dialogu součásti:</span><span class="sxs-lookup"><span data-stu-id="97058-131">The following markup is an example of a custom dialog component:</span></span>

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick="@OnOK">OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

<span data-ttu-id="97058-132">Pokud tuto součást používáte jinde v aplikaci, technologie IntelliSense v [sady Visual Studio](https://visualstudio.microsoft.com/vs/) urychluje vývoj aplikací pomocí syntaxe a parametrů dokončení.</span><span class="sxs-lookup"><span data-stu-id="97058-132">When this component is used elsewhere in the app, IntelliSense in [Visual Studio](https://visualstudio.microsoft.com/vs/) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="97058-133">Komponenty vykreslování do v paměti reprezentace prohlížeč DOM volána *vykreslení stromu* , který potom slouží k aktualizaci uživatelského rozhraní tak flexibilní a efektivní.</span><span class="sxs-lookup"><span data-stu-id="97058-133">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-server-side"></a><span data-ttu-id="97058-134">Blazor na straně serveru</span><span class="sxs-lookup"><span data-stu-id="97058-134">Blazor server-side</span></span>

<span data-ttu-id="97058-135">Blazor odděluje komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="97058-135">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="97058-136">Serverové Blazor poskytuje podporu pro hostování součásti Razor na serveru v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97058-136">Blazor server-side provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="97058-137">Aktualizace uživatelského rozhraní jsou zpracovány prostřednictvím připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="97058-137">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="97058-138">Modul runtime:</span><span class="sxs-lookup"><span data-stu-id="97058-138">The runtime:</span></span>

* <span data-ttu-id="97058-139">Obslužné rutiny odeslání události uživatelského rozhraní z prohlížeče na server.</span><span class="sxs-lookup"><span data-stu-id="97058-139">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="97058-140">Platí uživatelského rozhraní aktualizací odeslané serverem zpět do prohlížeče po spuštění součásti.</span><span class="sxs-lookup"><span data-stu-id="97058-140">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="97058-141">Připojení používané objektem Blazor serverové ke komunikaci s prohlížeči slouží také ke zpracování spolupráce volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="97058-141">The connection used by Blazor server-side to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Blazor serveru běží kód .NET na serveru a komunikuje s modelu objektu dokumentu na straně klienta pomocí připojení SignalR](index/_static/blazor-server-side.png)

<span data-ttu-id="97058-143">Další informace naleznete v tématu <xref:blazor/hosting-models#server-side>.</span><span class="sxs-lookup"><span data-stu-id="97058-143">For more information, see <xref:blazor/hosting-models#server-side>.</span></span>

## <a name="blazor-client-side"></a><span data-ttu-id="97058-144">Blazor na straně klienta</span><span class="sxs-lookup"><span data-stu-id="97058-144">Blazor client-side</span></span>

<span data-ttu-id="97058-145">Blazor na straně klienta je architektura jednostránkové aplikace pro vytváření interaktivních webových aplikací na straně klienta s .NET.</span><span class="sxs-lookup"><span data-stu-id="97058-145">Blazor client-side is a single-page app framework for building interactive client-side Web apps with .NET.</span></span> <span data-ttu-id="97058-146">Blazor na straně klienta používá otevřené webové standardy bez transpilation moduly plug-in nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="97058-146">Blazor client-side uses open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="97058-147">Na straně klienta funguje Blazor v všechny moderní webové prohlížeče, včetně mobilních.</span><span class="sxs-lookup"><span data-stu-id="97058-147">Blazor client-side works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="97058-148">Pomocí rozhraní .NET v prohlížeči pro vývoj webů na straně klienta nabízí celou řadu výhod:</span><span class="sxs-lookup"><span data-stu-id="97058-148">Using .NET in the browser for client-side web development offers many advantages:</span></span>

* <span data-ttu-id="97058-149">**C#jazyk**: Psaní kódu v C# místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="97058-149">**C# language**: Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="97058-150">**.NET Ecosystem**: Využijte stávající ekosystém knihoven .NET.</span><span class="sxs-lookup"><span data-stu-id="97058-150">**.NET Ecosystem**: Leverage the existing ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="97058-151">**Kompletní vývojové**: Sdílené složky serveru a na straně klienta logiku.</span><span class="sxs-lookup"><span data-stu-id="97058-151">**Full-stack development**: Share server and client-side logic.</span></span>
* <span data-ttu-id="97058-152">**Rychlost a škálovatelnost**: .NET byla vytvořena pro výkon, spolehlivost a zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="97058-152">**Speed and scalability**: .NET was built for performance, reliability, and security.</span></span>
* <span data-ttu-id="97058-153">**Špičkové nástroje**: Produktivní práci s aplikací Visual Studio ve Windows, Linuxu a macOS.</span><span class="sxs-lookup"><span data-stu-id="97058-153">**Industry-leading tools**: Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="97058-154">**Stabilitu a konzistence**:  Sestavení na společnou sadu jazyky, platformy a nástroje, které jsou stabilní, plně funkční a snadno se používá.</span><span class="sxs-lookup"><span data-stu-id="97058-154">**Stability and consistency**:  Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

<span data-ttu-id="97058-155">Spouštění kódu .NET uvnitř webových prohlížečů je možné podle [WebAssembly](http://webassembly.org) (zkrácené *wasm*).</span><span class="sxs-lookup"><span data-stu-id="97058-155">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="97058-156">WebAssembly je otevřít web, standard a podporovaných webových prohlížečů bez modulů plug-in.</span><span class="sxs-lookup"><span data-stu-id="97058-156">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="97058-157">WebAssembly je formát compact bajtového kódu optimalizovaný pro rychlé stažení a spuštění maximální rychlost.</span><span class="sxs-lookup"><span data-stu-id="97058-157">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="97058-158">WebAssembly kód může přistupovat k úplné funkce aplikace v prohlížeči pomocí zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="97058-158">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="97058-159">Ve stejnou dobu .NET kód proveden prostřednictvím WebAssembly běží v stejné důvěryhodné izolovaného prostoru jako jazyka JavaScript, aby se zabránilo škodlivé akce v klientském počítači.</span><span class="sxs-lookup"><span data-stu-id="97058-159">At the same time, .NET code executed via WebAssembly runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![Blazor na straně klienta běží v prohlížeči s WebAssembly kód .NET.](index/_static/blazor-client-side.png)

<span data-ttu-id="97058-161">Aplikace na straně klienta Blazor při vytvoření a spuštění v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="97058-161">When a Blazor client-side app is built and run in a browser:</span></span>

* <span data-ttu-id="97058-162">C#soubory kódu a Razor soubory jsou zkompilovány do sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="97058-162">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="97058-163">Sestavení a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="97058-163">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="97058-164">Blazor klientské bootstraps modul .NET runtime a konfiguruje modul runtime k načtení sestavení pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="97058-164">Blazor client-side bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="97058-165">Volání dokumentu Object Model (DOM) manipulaci a prohlížeč rozhraní API jsou zpracovány tímto modulem Blazor na straně klienta pomocí zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="97058-165">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor client-side runtime via JavaScript interop.</span></span>

<span data-ttu-id="97058-166">Ke snížení objemu stažených aplikací, nepoužitý kód je odebrána z aplikace když se publikuje [Intermediate Language (IL) Linkeru](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="97058-166">To reduce the size of the downloaded app, unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>

<span data-ttu-id="97058-167">Blazor na straně klienta je model hostingu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="97058-167">Blazor client-side is a client-side hosting model.</span></span> <span data-ttu-id="97058-168">Protože Blazor odděluje komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní, je v tom, jak se dají hostovat Blazor flexibilitu.</span><span class="sxs-lookup"><span data-stu-id="97058-168">Because Blazor decouples a component's rendering logic from how UI updates are applied, there's flexibility in how Blazor can be hosted.</span></span> <span data-ttu-id="97058-169">Použití [Blazor serverové](#blazor-server-side) Blazor hostitele na serveru v aplikaci ASP.NET Core, kde se zpracovávají aktualizace uživatelského rozhraní pomocí připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="97058-169">Use [Blazor server-side](#blazor-server-side) to host Blazor on the server in an ASP.NET Core app where UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="97058-170">Další informace naleznete v tématu <xref:blazor/hosting-models#server-side>.</span><span class="sxs-lookup"><span data-stu-id="97058-170">For more information, see <xref:blazor/hosting-models#server-side>.</span></span> 

<span data-ttu-id="97058-171">Velikost datové části je důležité výkonu koeficient pro použitelnost aplikace míra.</span><span class="sxs-lookup"><span data-stu-id="97058-171">Payload size is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="97058-172">Klientské Blazor optimalizuje velikost datové části snížit dobu stahování:</span><span class="sxs-lookup"><span data-stu-id="97058-172">Blazor client-side optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="97058-173">Nevyužité části sestavení .NET se odeberou během procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="97058-173">Unused parts of .NET assemblies are removed during the build process.</span></span>
* <span data-ttu-id="97058-174">Odpovědi protokolu HTTP jsou komprimované.</span><span class="sxs-lookup"><span data-stu-id="97058-174">HTTP responses are compressed.</span></span>
* <span data-ttu-id="97058-175">Modul runtime rozhraní .NET a sestavení jsou uložené v mezipaměti v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="97058-175">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="97058-176">[Serverové Blazor](#blazor-server-side) poskytuje menší velikost datové části než Blazor na straně klienta pomocí sestavení, sestavení aplikace a serverové modulu runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="97058-176">[Blazor server-side](#blazor-server-side) provides a smaller payload size than Blazor client-side by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="97058-177">Blazor serverové aplikace sloužit pouze soubory značek a statické prostředky klientům.</span><span class="sxs-lookup"><span data-stu-id="97058-177">Blazor server-side apps only serve markup files and static assets to clients.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="97058-178">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="97058-178">JavaScript interop</span></span>

<span data-ttu-id="97058-179">U aplikací, které vyžadují knihovny třetích stran jazyka JavaScript a prohlížeč rozhraní API pro součásti spolupracovat s použitím jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="97058-179">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="97058-180">Součásti jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít.</span><span class="sxs-lookup"><span data-stu-id="97058-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="97058-181">C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu.</span><span class="sxs-lookup"><span data-stu-id="97058-181">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="97058-182">Další informace najdete v tématu [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="97058-182">For more information, see [JavaScript interop](xref:blazor/javascript-interop).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="97058-183">Sdílení kódu a .NET Standard</span><span class="sxs-lookup"><span data-stu-id="97058-183">Code sharing and .NET Standard</span></span>

<span data-ttu-id="97058-184">Aplikace můžete odkazovat a využívat stávající [.NET Standard](/dotnet/standard/net-standard) knihovny.</span><span class="sxs-lookup"><span data-stu-id="97058-184">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="97058-185">.NET standard je formální specifikaci rozhraní API pro .NET, které jsou společné pro implementace .NET.</span><span class="sxs-lookup"><span data-stu-id="97058-185">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="97058-186">Blazor implementuje rozhraní .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="97058-186">Blazor implements .NET Standard 2.0.</span></span> <span data-ttu-id="97058-187">Rozhraní API, které nejsou použitelné ve webovém prohlížeči (například přístup k systému souborů, otevřeme soket, dělení na vlákna a další funkce) throw <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="97058-187">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw <xref:System.PlatformNotSupportedException>.</span></span> <span data-ttu-id="97058-188">Knihovny tříd .NET standard můžete sdílet na různých platformách .NET, jako je například Blazor, rozhraní .NET Framework, .NET Core, Xamarin, Mono a Unity.</span><span class="sxs-lookup"><span data-stu-id="97058-188">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97058-189">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="97058-189">Additional resources</span></span>

* [<span data-ttu-id="97058-190">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="97058-190">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="97058-191">Průvodce jazykem C#</span><span class="sxs-lookup"><span data-stu-id="97058-191">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="97058-192">HTML</span><span class="sxs-lookup"><span data-stu-id="97058-192">HTML</span></span>](https://www.w3.org/html/)
