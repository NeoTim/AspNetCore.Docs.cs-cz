---
title: Úvod do Blazor
author: guardrex
description: 'Prozkoumejte Blazor ASP.NET Core, nový způsob, jak vytvářet interaktivní aplikace na straně klienta s .NET, na kterých běží v prohlížeči s WebAssembly.'
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
uid: spa/blazor/index
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="ed058-103">Úvod do Blazor</span><span class="sxs-lookup"><span data-stu-id="ed058-103">Introduction to Blazor</span></span>

<span data-ttu-id="ed058-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ed058-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="ed058-105">Blazor je architektura jednostránkové aplikace pro vytváření interaktivních webových aplikací na straně klienta s .NET.</span><span class="sxs-lookup"><span data-stu-id="ed058-105">Blazor is a single-page app framework for building interactive client-side Web apps with .NET.</span></span> <span data-ttu-id="ed058-106">Blazor používá otevřené webové standardy bez transpilation moduly plug-in nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="ed058-106">Blazor uses open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="ed058-107">Blazor funguje v všechny moderní webové prohlížeče, včetně mobilních.</span><span class="sxs-lookup"><span data-stu-id="ed058-107">Blazor works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="ed058-108">Pomocí rozhraní .NET v prohlížeči pro vývoj webů na straně klienta nabízí celou řadu výhod:</span><span class="sxs-lookup"><span data-stu-id="ed058-108">Using .NET in the browser for client-side web development offers many advantages:</span></span>

* <span data-ttu-id="ed058-109">**C#jazyk**: Psaní kódu v C# místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="ed058-109">**C# language**: Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="ed058-110">**.NET Ecosystem**: Využijte stávající ekosystém knihoven .NET.</span><span class="sxs-lookup"><span data-stu-id="ed058-110">**.NET Ecosystem**: Leverage the existing ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="ed058-111">**Kompletní vývojové**: Sdílené složky serveru a na straně klienta logiku.</span><span class="sxs-lookup"><span data-stu-id="ed058-111">**Full-stack development**: Share server and client-side logic.</span></span>
* <span data-ttu-id="ed058-112">**Rychlost a škálovatelnost**: .NET byla vytvořena pro výkon, spolehlivost a zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="ed058-112">**Speed and scalability**: .NET was built for performance, reliability, and security.</span></span>
* <span data-ttu-id="ed058-113">**Špičkové nástroje**: Produktivní práci s aplikací Visual Studio ve Windows, Linuxu a macOS.</span><span class="sxs-lookup"><span data-stu-id="ed058-113">**Industry-leading tools**: Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="ed058-114">**Stabilitu a konzistence**:  Sestavení na commonset jazyků, architektur a nástroje, které jsou stabilní, plně funkční a snadno se používá.</span><span class="sxs-lookup"><span data-stu-id="ed058-114">**Stability and consistency**:  Build on a commonset of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

<span data-ttu-id="ed058-115">Spouštění kódu .NET uvnitř webových prohlížečů je možné podle [WebAssembly](http://webassembly.org) (zkrácené *wasm*).</span><span class="sxs-lookup"><span data-stu-id="ed058-115">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="ed058-116">WebAssembly je otevřít web, standard a podporovaných webových prohlížečů bez modulů plug-in.</span><span class="sxs-lookup"><span data-stu-id="ed058-116">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="ed058-117">WebAssembly je formát compact bajtového kódu optimalizovaný pro rychlé stažení a spuštění maximální rychlost.</span><span class="sxs-lookup"><span data-stu-id="ed058-117">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="ed058-118">WebAssembly kód může přistupovat k úplné funkce aplikace v prohlížeči pomocí zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ed058-118">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="ed058-119">Ve stejnou dobu .NET kód proveden prostřednictvím WebAssembly běží v stejné důvěryhodné izolovaného prostoru jako jazyka JavaScript, aby se zabránilo škodlivé akce v klientském počítači.</span><span class="sxs-lookup"><span data-stu-id="ed058-119">At the same time, .NET code executed via WebAssembly runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![Blazor běží v prohlížeči s WebAssembly kód .NET.](index/_static/blazor.png)

<span data-ttu-id="ed058-121">Při Blazor aplikace je vytvořená a spustit v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="ed058-121">When a Blazor app is built and run in a browser:</span></span>

* <span data-ttu-id="ed058-122">C#soubory kódu a Razor soubory jsou zkompilovány do sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="ed058-122">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="ed058-123">Sestavení a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="ed058-123">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="ed058-124">Blazor bootstraps modul .NET runtime a konfiguruje modul runtime k načtení sestavení pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ed058-124">Blazor bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="ed058-125">Volání dokumentu Object Model (DOM) manipulaci a prohlížeč rozhraní API jsou zpracovány tímto modulem Blazor prostřednictvím zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ed058-125">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor runtime via JavaScript interop.</span></span>

<span data-ttu-id="ed058-126">Blazor podporuje zařízení core vyžaduje většina aplikací, včetně:</span><span class="sxs-lookup"><span data-stu-id="ed058-126">Blazor supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="ed058-127">Parametry</span><span class="sxs-lookup"><span data-stu-id="ed058-127">Parameters</span></span>
* <span data-ttu-id="ed058-128">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="ed058-128">Event handling</span></span>
* <span data-ttu-id="ed058-129">Vytváření datových vazeb</span><span class="sxs-lookup"><span data-stu-id="ed058-129">Data binding</span></span>
* <span data-ttu-id="ed058-130">Směrování</span><span class="sxs-lookup"><span data-stu-id="ed058-130">Routing</span></span>
* <span data-ttu-id="ed058-131">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="ed058-131">Dependency injection</span></span>
* <span data-ttu-id="ed058-132">Rozložení</span><span class="sxs-lookup"><span data-stu-id="ed058-132">Layouts</span></span>
* <span data-ttu-id="ed058-133">Šablon</span><span class="sxs-lookup"><span data-stu-id="ed058-133">Templating</span></span>
* <span data-ttu-id="ed058-134">Kaskádové hodnoty</span><span class="sxs-lookup"><span data-stu-id="ed058-134">Cascading values</span></span>

<span data-ttu-id="ed058-135">Ke snížení objemu stažených aplikací nepoužitý kód odebrána z aplikace když se publikuje [Intermediate Language (IL) Linkeru](xref:host-and-deploy/razor-components/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="ed058-135">To reduce the size of the downloaded app unused code stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/razor-components/configure-linker).</span></span>

<span data-ttu-id="ed058-136">Blazor je model hostingu na straně klienta pro součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="ed058-136">Blazor is the client-side hosting model for Razor Components.</span></span> <span data-ttu-id="ed058-137">Protože komponenty Razor oddělit komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní, existuje určitá flexibilita v jak se dají hostovat součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="ed058-137">Because Razor Components decouple a component's rendering logic from how UI updates are applied, there's flexibility in how Razor Components can be hosted.</span></span> <span data-ttu-id="ed058-138">Pomocí ASP.NET Core Razor součásti k hostiteli Razor komponent na serveru v aplikaci ASP.NET Core ve kterém se zpracovávají aktualizace uživatelského rozhraní pomocí připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="ed058-138">Use ASP.NET Core Razor Components to host Razor Components on the server in an ASP.NET Core app where UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="ed058-139">Další informace naleznete v tématu <xref:razor-components/index> a <xref:razor-components/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="ed058-139">For more information, see <xref:razor-components/index> and <xref:razor-components/hosting-models#server-side-hosting-model>.</span></span> 

## <a name="components"></a><span data-ttu-id="ed058-140">Komponenty</span><span class="sxs-lookup"><span data-stu-id="ed058-140">Components</span></span>

<span data-ttu-id="ed058-141">A *Razor komponenty* je část uživatelského rozhraní, jako je například formulář položka stránky, dialogové okno nebo data.</span><span class="sxs-lookup"><span data-stu-id="ed058-141">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="ed058-142">Součásti zpracovávat události uživatele a definovat flexibilní logiku pro vykreslení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ed058-142">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="ed058-143">Součásti můžete vnořit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="ed058-143">Components can be nested and reused.</span></span>

<span data-ttu-id="ed058-144">Komponenty jsou součástí sestavení .NET, které lze sdílet a distribuovat jako balíčky NuGet třídy rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="ed058-144">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="ed058-145">Třídu lze zapsat buď v podobě značek stránky Razor (*.cshtml*) nebo jako C# třídy (*.cs*).</span><span class="sxs-lookup"><span data-stu-id="ed058-145">The class can either be written in the form of a Razor markup page (*.cshtml*) or as a C# class (*.cs*).</span></span>

<span data-ttu-id="ed058-146">[Razor](xref:mvc/views/razor) je syntaxe pro kombinování značka jazyka HTML s C# kódu.</span><span class="sxs-lookup"><span data-stu-id="ed058-146">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="ed058-147">Razor je navržená pro produktivitu vývojářů, umožňuje vývojářům přepínat mezi značky a C# ve stejném souboru s [IntelliSense](/visualstudio/ide/using-intellisense) podporovat.</span><span class="sxs-lookup"><span data-stu-id="ed058-147">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="ed058-148">Stránky Razor a MVC zobrazení také používají syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="ed058-148">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="ed058-149">Na rozdíl od Razor Pages a zobrazení MVC, která jsou postavené na modelu žádost odpověď, používají součásti speciálně pro zpracování sestavení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ed058-149">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="ed058-150">Komponenty Razor můžete použít speciálně pro logika uživatelského rozhraní na straně klienta a skládání.</span><span class="sxs-lookup"><span data-stu-id="ed058-150">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="ed058-151">Následující kód je příklad vlastního dialogu komponenty v souboru Razor (*DialogComponent.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="ed058-151">The following markup is an example of a custom dialog component in a Razor file (*DialogComponent.cshtml*):</span></span>

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick=@OnOK>OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

<span data-ttu-id="ed058-152">Při této součásti se používá jinde v aplikaci, technologie IntelliSense urychluje vývoj aplikací pomocí syntaxe a parametrů dokončení.</span><span class="sxs-lookup"><span data-stu-id="ed058-152">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="ed058-153">Komponenty vykreslování do v paměti reprezentace prohlížeč DOM volána *vykreslení stromu* , který potom slouží k aktualizaci uživatelského rozhraní tak flexibilní a efektivní.</span><span class="sxs-lookup"><span data-stu-id="ed058-153">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="ed058-154">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="ed058-154">JavaScript interop</span></span>

<span data-ttu-id="ed058-155">U aplikací, které vyžadují knihovny třetích stran jazyka JavaScript a prohlížeč rozhraní API Blazor spolupracuje s použitím jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ed058-155">For apps that require third-party JavaScript libraries and browser APIs, Blazor interoperates with JavaScript.</span></span> <span data-ttu-id="ed058-156">Součásti jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít.</span><span class="sxs-lookup"><span data-stu-id="ed058-156">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="ed058-157">C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu.</span><span class="sxs-lookup"><span data-stu-id="ed058-157">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="ed058-158">Další informace najdete v tématu [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="ed058-158">For more information, see [JavaScript interop](xref:razor-components/javascript-interop).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="ed058-159">Sdílení kódu a .NET Standard</span><span class="sxs-lookup"><span data-stu-id="ed058-159">Code sharing and .NET Standard</span></span>

<span data-ttu-id="ed058-160">Aplikace můžete odkazovat a využívat stávající [.NET Standard](/dotnet/standard/net-standard) knihovny.</span><span class="sxs-lookup"><span data-stu-id="ed058-160">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="ed058-161">.NET standard je formální specifikaci rozhraní API pro .NET, které jsou společné pro implementace .NET.</span><span class="sxs-lookup"><span data-stu-id="ed058-161">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="ed058-162">Se podporuje .NET standard 2.0 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="ed058-162">.NET Standard 2.0 or higher is supported.</span></span> <span data-ttu-id="ed058-163">Rozhraní API, které nejsou použitelné ve webovém prohlížeči (například přístup k systému souborů, otevřeme soket, dělení na vlákna a další funkce) throw <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="ed058-163">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw <xref:System.PlatformNotSupportedException>.</span></span> <span data-ttu-id="ed058-164">Knihovny tříd .NET standard je možné sdílet napříč kódu serveru a v aplikacích založených na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="ed058-164">.NET Standard class libraries can be shared across server code and in browser-based apps.</span></span>

## <a name="optimization"></a><span data-ttu-id="ed058-165">Optimalizace</span><span class="sxs-lookup"><span data-stu-id="ed058-165">Optimization</span></span>

<span data-ttu-id="ed058-166">Velikost datové části je důležité výkonu koeficient pro použitelnost aplikace míra.</span><span class="sxs-lookup"><span data-stu-id="ed058-166">Payload size is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="ed058-167">Blazor optimalizuje velikost datové části snížit dobu stahování:</span><span class="sxs-lookup"><span data-stu-id="ed058-167">Blazor optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="ed058-168">Nevyužité části sestavení .NET se odeberou během procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="ed058-168">Unused parts of .NET assemblies are removed during the build process.</span></span>
* <span data-ttu-id="ed058-169">Odpovědi protokolu HTTP jsou komprimované.</span><span class="sxs-lookup"><span data-stu-id="ed058-169">HTTP responses are compressed.</span></span>
* <span data-ttu-id="ed058-170">Modul runtime rozhraní .NET a sestavení jsou uložené v mezipaměti v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="ed058-170">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="ed058-171">[Serverové komponenty Razor](xref:razor-components/index) poskytuje ještě menší velikost datové části než Blazor pomocí sestavení, sestavení aplikace a serverové modulu runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="ed058-171">[Server-side Razor Components](xref:razor-components/index) provides an even smaller payload size than Blazor by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="ed058-172">Razor součásti aplikace sloužit pouze značky soubory a statické prostředky klientům.</span><span class="sxs-lookup"><span data-stu-id="ed058-172">Razor Components apps only serve markup files and static assets to clients.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ed058-173">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ed058-173">Additional resources</span></span>

* <xref:razor-components/index>
* [<span data-ttu-id="ed058-174">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ed058-174">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="ed058-175">Průvodce jazykem C#</span><span class="sxs-lookup"><span data-stu-id="ed058-175">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="ed058-176">HTML</span><span class="sxs-lookup"><span data-stu-id="ed058-176">HTML</span></span>](https://www.w3.org/html/)
