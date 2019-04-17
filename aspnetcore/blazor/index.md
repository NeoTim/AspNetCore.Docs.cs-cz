---
title: Úvod do Blazor v ASP.NET Core
author: guardrex
description: Prozkoumejte službu ASP.NET Core Blazor způsob, jak vytvářet interaktivní webové na straně klienta uživatelské rozhraní s využitím .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/15/2019
uid: blazor/index
ms.openlocfilehash: a5b12a5c5c10a74ab192d0d67a2ba9a5617232c7
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614818"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="fe705-103">Úvod do Blazor</span><span class="sxs-lookup"><span data-stu-id="fe705-103">Introduction to Blazor</span></span>

<span data-ttu-id="fe705-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fe705-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="razor-components"></a><span data-ttu-id="fe705-105">Komponenty Razor</span><span class="sxs-lookup"><span data-stu-id="fe705-105">Razor Components</span></span>

<span data-ttu-id="fe705-106">Model hostingu na straně serveru z Blazor, *Razor komponenty*, je způsob, jak vytvářet interaktivní webové uživatelské rozhraní s .NET na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="fe705-106">The server-side hosting model of Blazor, *Razor Components*, is a way to build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="fe705-107">Vytvářet bohaté interaktivní uživatelských rozhraní pomocí C# místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="fe705-107">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="fe705-108">Sdílet logiku na straně serveru a na straně klienta aplikací napsaných pomocí rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="fe705-108">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="fe705-109">Vykreslení uživatelského rozhraní jako HTML a CSS pro podporu široké prohlížeče, včetně mobilních.</span><span class="sxs-lookup"><span data-stu-id="fe705-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="fe705-110">Součásti Razor podporuje zařízení core vyžaduje většina aplikací, včetně:</span><span class="sxs-lookup"><span data-stu-id="fe705-110">Razor Components supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="fe705-111">Parametry</span><span class="sxs-lookup"><span data-stu-id="fe705-111">Parameters</span></span>
* <span data-ttu-id="fe705-112">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="fe705-112">Event handling</span></span>
* <span data-ttu-id="fe705-113">Vytváření datových vazeb</span><span class="sxs-lookup"><span data-stu-id="fe705-113">Data binding</span></span>
* <span data-ttu-id="fe705-114">Směrování</span><span class="sxs-lookup"><span data-stu-id="fe705-114">Routing</span></span>
* <span data-ttu-id="fe705-115">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="fe705-115">Dependency injection</span></span>
* <span data-ttu-id="fe705-116">Rozložení</span><span class="sxs-lookup"><span data-stu-id="fe705-116">Layouts</span></span>
* <span data-ttu-id="fe705-117">Šablon</span><span class="sxs-lookup"><span data-stu-id="fe705-117">Templating</span></span>
* <span data-ttu-id="fe705-118">Kaskádové hodnoty</span><span class="sxs-lookup"><span data-stu-id="fe705-118">Cascading values</span></span>

<span data-ttu-id="fe705-119">Součásti Razor odděluje komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fe705-119">Razor Components decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="fe705-120">ASP.NET Core Razor součásti v .NET Core 3.0 přidává podporu pro hostování součásti Razor na server v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fe705-120">ASP.NET Core Razor Components in .NET Core 3.0 adds support for hosting Razor Components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="fe705-121">Aktualizace uživatelského rozhraní jsou zpracovány prostřednictvím připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="fe705-121">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="fe705-122">Modul runtime:</span><span class="sxs-lookup"><span data-stu-id="fe705-122">The runtime:</span></span>

* <span data-ttu-id="fe705-123">Obslužné rutiny odeslání události uživatelského rozhraní z prohlížeče na server.</span><span class="sxs-lookup"><span data-stu-id="fe705-123">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="fe705-124">Platí uživatelského rozhraní aktualizací odeslané serverem zpět do prohlížeče po spuštění součásti.</span><span class="sxs-lookup"><span data-stu-id="fe705-124">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="fe705-125">Připojení používané komponenty Razor ke komunikaci s prohlížeči slouží také ke zpracování spolupráce volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="fe705-125">The connection used by Razor Components to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Razor komponenty běží kód .NET na serveru a komunikuje s modelu objektu dokumentu na straně klienta pomocí připojení SignalR](index/_static/aspnet-core-razor-components.png)

<span data-ttu-id="fe705-127">Další informace naleznete v tématu <xref:blazor/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="fe705-127">For more information, see <xref:blazor/hosting-models#server-side-hosting-model>.</span></span>

## <a name="components"></a><span data-ttu-id="fe705-128">Komponenty</span><span class="sxs-lookup"><span data-stu-id="fe705-128">Components</span></span>

<span data-ttu-id="fe705-129">A *Razor komponenty* je část uživatelského rozhraní, jako je například formulář položka stránky, dialogové okno nebo data.</span><span class="sxs-lookup"><span data-stu-id="fe705-129">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="fe705-130">Součásti zpracovávat události uživatele a definovat flexibilní logiku pro vykreslení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fe705-130">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="fe705-131">Součásti můžete vnořit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="fe705-131">Components can be nested and reused.</span></span>

<span data-ttu-id="fe705-132">Komponenty jsou součástí sestavení .NET, které lze sdílet a distribuovat jako balíčky NuGet třídy rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="fe705-132">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="fe705-133">Třída je obvykle napsané v podobě značek stránky Razor s *.razor* příponu souboru (Razor součástí) nebo stránka značky Razor s *.cshtml* příponu (Blazor).</span><span class="sxs-lookup"><span data-stu-id="fe705-133">The class is normally written in the form of a Razor markup page with a *.razor* file extension (Razor Components) or Razor markup page with a *.cshtml* file extension (Blazor).</span></span>

<span data-ttu-id="fe705-134">[Razor](xref:mvc/views/razor) je syntaxe pro kombinování značka jazyka HTML s C# kódu.</span><span class="sxs-lookup"><span data-stu-id="fe705-134">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="fe705-135">Razor je navržená pro produktivitu vývojářů, umožňuje vývojářům přepínat mezi značky a C# ve stejném souboru s [IntelliSense](/visualstudio/ide/using-intellisense) podporovat.</span><span class="sxs-lookup"><span data-stu-id="fe705-135">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="fe705-136">Stránky Razor a MVC zobrazení také používají syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="fe705-136">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="fe705-137">Na rozdíl od Razor Pages a zobrazení MVC, která jsou postavené na modelu žádost odpověď, používají součásti speciálně pro zpracování sestavení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fe705-137">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="fe705-138">Komponenty Razor můžete použít speciálně pro logika uživatelského rozhraní na straně klienta a skládání.</span><span class="sxs-lookup"><span data-stu-id="fe705-138">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="fe705-139">Následující kód je příklad vlastního dialogu součásti:</span><span class="sxs-lookup"><span data-stu-id="fe705-139">The following markup is an example of a custom dialog component:</span></span>

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

<span data-ttu-id="fe705-140">Při této součásti se používá jinde v aplikaci, technologie IntelliSense urychluje vývoj aplikací pomocí syntaxe a parametrů dokončení.</span><span class="sxs-lookup"><span data-stu-id="fe705-140">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="fe705-141">Komponenty vykreslování do v paměti reprezentace prohlížeč DOM volána *vykreslení stromu* , který potom slouží k aktualizaci uživatelského rozhraní tak flexibilní a efektivní.</span><span class="sxs-lookup"><span data-stu-id="fe705-141">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="fe705-142">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="fe705-142">JavaScript interop</span></span>

<span data-ttu-id="fe705-143">U aplikací, které vyžadují knihovny třetích stran jazyka JavaScript a prohlížeč rozhraní API pro součásti spolupracovat s použitím jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fe705-143">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="fe705-144">Součásti jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít.</span><span class="sxs-lookup"><span data-stu-id="fe705-144">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="fe705-145">C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu.</span><span class="sxs-lookup"><span data-stu-id="fe705-145">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="fe705-146">Další informace najdete v tématu [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="fe705-146">For more information, see [JavaScript interop](xref:blazor/javascript-interop).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="fe705-147">Sdílení kódu a .NET Standard</span><span class="sxs-lookup"><span data-stu-id="fe705-147">Code sharing and .NET Standard</span></span>

<span data-ttu-id="fe705-148">Aplikace můžete odkazovat a využívat stávající [.NET Standard](/dotnet/standard/net-standard) knihovny.</span><span class="sxs-lookup"><span data-stu-id="fe705-148">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="fe705-149">.NET standard je formální specifikaci rozhraní API pro .NET, které jsou společné pro implementace .NET.</span><span class="sxs-lookup"><span data-stu-id="fe705-149">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="fe705-150">Blazor implementuje rozhraní .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="fe705-150">Blazor implements .NET Standard 2.0.</span></span> <span data-ttu-id="fe705-151">Rozhraní API, které nejsou použitelné ve webovém prohlížeči (například přístup k systému souborů, otevřeme soket, dělení na vlákna a další funkce) throw <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="fe705-151">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw <xref:System.PlatformNotSupportedException>.</span></span> <span data-ttu-id="fe705-152">Knihovny tříd .NET standard můžete sdílet na různých platformách .NET, jako jsou Blazor, rozhraní .NET Framework, .NET Core, Xamarin, Mono a Unity.</span><span class="sxs-lookup"><span data-stu-id="fe705-152">.NET Standard class libraries can be shared across different .NET platforms, like Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

## <a name="blazor"></a><span data-ttu-id="fe705-153">Blazor</span><span class="sxs-lookup"><span data-stu-id="fe705-153">Blazor</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="fe705-154">Blazor je architektura jednostránkové aplikace pro vytváření interaktivních webových aplikací na straně klienta s .NET.</span><span class="sxs-lookup"><span data-stu-id="fe705-154">Blazor is a single-page app framework for building interactive client-side Web apps with .NET.</span></span> <span data-ttu-id="fe705-155">Blazor používá otevřené webové standardy bez transpilation moduly plug-in nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="fe705-155">Blazor uses open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="fe705-156">Blazor funguje v všechny moderní webové prohlížeče, včetně mobilních.</span><span class="sxs-lookup"><span data-stu-id="fe705-156">Blazor works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="fe705-157">Pomocí rozhraní .NET v prohlížeči pro vývoj webů na straně klienta nabízí celou řadu výhod:</span><span class="sxs-lookup"><span data-stu-id="fe705-157">Using .NET in the browser for client-side web development offers many advantages:</span></span>

* <span data-ttu-id="fe705-158">**C#jazyk**: Psaní kódu v C# místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="fe705-158">**C# language**: Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="fe705-159">**.NET Ecosystem**: Využijte stávající ekosystém knihoven .NET.</span><span class="sxs-lookup"><span data-stu-id="fe705-159">**.NET Ecosystem**: Leverage the existing ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="fe705-160">**Kompletní vývojové**: Sdílené složky serveru a na straně klienta logiku.</span><span class="sxs-lookup"><span data-stu-id="fe705-160">**Full-stack development**: Share server and client-side logic.</span></span>
* <span data-ttu-id="fe705-161">**Rychlost a škálovatelnost**: .NET byla vytvořena pro výkon, spolehlivost a zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="fe705-161">**Speed and scalability**: .NET was built for performance, reliability, and security.</span></span>
* <span data-ttu-id="fe705-162">**Špičkové nástroje**: Produktivní práci s aplikací Visual Studio ve Windows, Linuxu a macOS.</span><span class="sxs-lookup"><span data-stu-id="fe705-162">**Industry-leading tools**: Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="fe705-163">**Stabilitu a konzistence**:  Sestavení na commonset jazyků, architektur a nástroje, které jsou stabilní, plně funkční a snadno se používá.</span><span class="sxs-lookup"><span data-stu-id="fe705-163">**Stability and consistency**:  Build on a commonset of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

<span data-ttu-id="fe705-164">Spouštění kódu .NET uvnitř webových prohlížečů je možné podle [WebAssembly](http://webassembly.org) (zkrácené *wasm*).</span><span class="sxs-lookup"><span data-stu-id="fe705-164">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="fe705-165">WebAssembly je otevřít web, standard a podporovaných webových prohlížečů bez modulů plug-in.</span><span class="sxs-lookup"><span data-stu-id="fe705-165">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="fe705-166">WebAssembly je formát compact bajtového kódu optimalizovaný pro rychlé stažení a spuštění maximální rychlost.</span><span class="sxs-lookup"><span data-stu-id="fe705-166">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="fe705-167">WebAssembly kód může přistupovat k úplné funkce aplikace v prohlížeči pomocí zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fe705-167">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="fe705-168">Ve stejnou dobu .NET kód proveden prostřednictvím WebAssembly běží v stejné důvěryhodné izolovaného prostoru jako jazyka JavaScript, aby se zabránilo škodlivé akce v klientském počítači.</span><span class="sxs-lookup"><span data-stu-id="fe705-168">At the same time, .NET code executed via WebAssembly runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![Blazor běží v prohlížeči s WebAssembly kód .NET.](index/_static/blazor.png)

<span data-ttu-id="fe705-170">Při Blazor aplikace je vytvořená a spustit v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="fe705-170">When a Blazor app is built and run in a browser:</span></span>

* <span data-ttu-id="fe705-171">C#soubory kódu a Razor soubory jsou zkompilovány do sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="fe705-171">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="fe705-172">Sestavení a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="fe705-172">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="fe705-173">Blazor bootstraps modul .NET runtime a konfiguruje modul runtime k načtení sestavení pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe705-173">Blazor bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="fe705-174">Volání dokumentu Object Model (DOM) manipulaci a prohlížeč rozhraní API jsou zpracovány tímto modulem Blazor prostřednictvím zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fe705-174">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor runtime via JavaScript interop.</span></span>

<span data-ttu-id="fe705-175">Blazor podporuje zařízení core vyžaduje většina aplikací, včetně:</span><span class="sxs-lookup"><span data-stu-id="fe705-175">Blazor supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="fe705-176">Parametry</span><span class="sxs-lookup"><span data-stu-id="fe705-176">Parameters</span></span>
* <span data-ttu-id="fe705-177">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="fe705-177">Event handling</span></span>
* <span data-ttu-id="fe705-178">Vytváření datových vazeb</span><span class="sxs-lookup"><span data-stu-id="fe705-178">Data binding</span></span>
* <span data-ttu-id="fe705-179">Směrování</span><span class="sxs-lookup"><span data-stu-id="fe705-179">Routing</span></span>
* <span data-ttu-id="fe705-180">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="fe705-180">Dependency injection</span></span>
* <span data-ttu-id="fe705-181">Rozložení</span><span class="sxs-lookup"><span data-stu-id="fe705-181">Layouts</span></span>
* <span data-ttu-id="fe705-182">Šablon</span><span class="sxs-lookup"><span data-stu-id="fe705-182">Templating</span></span>
* <span data-ttu-id="fe705-183">Kaskádové hodnoty</span><span class="sxs-lookup"><span data-stu-id="fe705-183">Cascading values</span></span>

<span data-ttu-id="fe705-184">Ke snížení objemu stažených aplikací, nepoužitý kód je odebrána z aplikace když se publikuje [Intermediate Language (IL) Linkeru](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="fe705-184">To reduce the size of the downloaded app, unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>

<span data-ttu-id="fe705-185">Blazor na straně klienta je model hostingu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="fe705-185">Blazor client-side is a client-side hosting model.</span></span> <span data-ttu-id="fe705-186">Protože Blazor odděluje komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní, je v tom, jak se dají hostovat Blazor flexibilitu.</span><span class="sxs-lookup"><span data-stu-id="fe705-186">Because Blazor decouples a component's rendering logic from how UI updates are applied, there's flexibility in how Blazor can be hosted.</span></span> <span data-ttu-id="fe705-187">Použití ASP.NET [Razor komponenty](#razor-components) k hostiteli Razor komponent na serveru v aplikaci ASP.NET Core, kde se zpracovávají aktualizace uživatelského rozhraní pomocí připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="fe705-187">Use ASP.NET Core [Razor Components](#razor-components) to host Razor Components on the server in an ASP.NET Core app where UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="fe705-188">Další informace naleznete v tématu <xref:blazor/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="fe705-188">For more information, see <xref:blazor/hosting-models#server-side-hosting-model>.</span></span> 

<span data-ttu-id="fe705-189">Velikost datové části je důležité výkonu koeficient pro použitelnost aplikace míra.</span><span class="sxs-lookup"><span data-stu-id="fe705-189">Payload size is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="fe705-190">Blazor optimalizuje velikost datové části snížit dobu stahování:</span><span class="sxs-lookup"><span data-stu-id="fe705-190">Blazor optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="fe705-191">Nevyužité části sestavení .NET se odeberou během procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="fe705-191">Unused parts of .NET assemblies are removed during the build process.</span></span>
* <span data-ttu-id="fe705-192">Odpovědi protokolu HTTP jsou komprimované.</span><span class="sxs-lookup"><span data-stu-id="fe705-192">HTTP responses are compressed.</span></span>
* <span data-ttu-id="fe705-193">Modul runtime rozhraní .NET a sestavení jsou uložené v mezipaměti v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="fe705-193">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="fe705-194">[Součásti Razor](#razor-components) poskytuje menší velikost datové části než Blazor pomocí sestavení, sestavení aplikace a serverové modulu runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="fe705-194">[Razor Components](#razor-components) provides a smaller payload size than Blazor by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="fe705-195">Razor součásti aplikace sloužit pouze značky soubory a statické prostředky klientům.</span><span class="sxs-lookup"><span data-stu-id="fe705-195">Razor Components apps only serve markup files and static assets to clients.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe705-196">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fe705-196">Additional resources</span></span>

* [<span data-ttu-id="fe705-197">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="fe705-197">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="fe705-198">Průvodce jazykem C#</span><span class="sxs-lookup"><span data-stu-id="fe705-198">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="fe705-199">HTML</span><span class="sxs-lookup"><span data-stu-id="fe705-199">HTML</span></span>](https://www.w3.org/html/)
