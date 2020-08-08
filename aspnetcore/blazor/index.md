---
title: Úvod do ASP.NET CoreBlazor
author: guardrex
description: Prozkoumejte ASP.NET Core Blazor , způsob sestavení interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 06/19/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/index
ms.openlocfilehash: ad543087243658f09a23e4f6d957d0c6aa77b361
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014175"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="5049a-103">Úvod do ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="5049a-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="5049a-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5049a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5049a-105">*Vítá vás Blazor !*</span><span class="sxs-lookup"><span data-stu-id="5049a-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="5049a-106">Blazorje rozhraní pro sestavování interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET:</span><span class="sxs-lookup"><span data-stu-id="5049a-106">Blazor is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="5049a-107">Vytvářejte bohatá interaktivní uživatelská rozhraní pomocí jazyka C# namísto JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="5049a-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="5049a-108">Sdílejte logiku aplikace na straně serveru a klienta napsanou v .NET.</span><span class="sxs-lookup"><span data-stu-id="5049a-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="5049a-109">Vykreslete uživatelské rozhraní jako HTML a CSS pro podporu pro rozsáhlou prohlížeč, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="5049a-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="5049a-110">Integrujte s moderními hostujícími platformami, jako je například [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="5049a-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="5049a-111">Použití rozhraní .NET pro vývoj webů na straně klienta nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="5049a-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="5049a-112">Psát kód v jazyce C# namísto JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="5049a-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="5049a-113">Využijte stávající ekosystém .NET knihoven .NET.</span><span class="sxs-lookup"><span data-stu-id="5049a-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="5049a-114">Sdílejte logiku aplikace napříč serverem a klientem.</span><span class="sxs-lookup"><span data-stu-id="5049a-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="5049a-115">Využijte výhod. Výkon, spolehlivost a zabezpečení sítě.</span><span class="sxs-lookup"><span data-stu-id="5049a-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="5049a-116">Aplikace Visual Studio v systémech Windows, Linux a macOS vám zůstane produktivní.</span><span class="sxs-lookup"><span data-stu-id="5049a-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="5049a-117">Sestavte se na společné sadě jazyků, platforem a nástrojů, které jsou stabilní, funkce s bohatou funkcí a snadno použitelné.</span><span class="sxs-lookup"><span data-stu-id="5049a-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="5049a-118">Komponenty</span><span class="sxs-lookup"><span data-stu-id="5049a-118">Components</span></span>

<span data-ttu-id="5049a-119">Blazoraplikace jsou založené na *komponentách*.</span><span class="sxs-lookup"><span data-stu-id="5049a-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="5049a-120">Komponenta v nástroji Blazor je prvek uživatelského rozhraní, jako je například stránka, dialog nebo formulář pro zadávání dat.</span><span class="sxs-lookup"><span data-stu-id="5049a-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="5049a-121">Komponenty jsou třídy .NET integrované do sestavení .NET, která:</span><span class="sxs-lookup"><span data-stu-id="5049a-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="5049a-122">Definujte flexibilní logiku vykreslování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5049a-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="5049a-123">Zpracování událostí uživatele.</span><span class="sxs-lookup"><span data-stu-id="5049a-123">Handle user events.</span></span>
* <span data-ttu-id="5049a-124">Může být vnořen a znovu použit.</span><span class="sxs-lookup"><span data-stu-id="5049a-124">Can be nested and reused.</span></span>
* <span data-ttu-id="5049a-125">Lze sdílet a distribuovat jako [ Razor knihovny tříd](xref:razor-pages/ui-class) nebo [balíčky NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="5049a-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="5049a-126">Třída komponenty je obvykle napsána ve formě [Razor](xref:mvc/views/razor) stránky s označením s `.razor` příponou souboru.</span><span class="sxs-lookup"><span data-stu-id="5049a-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="5049a-127">Komponenty v nástroji Blazor jsou formálně označovány jako \* Razor komponenty\*.</span><span class="sxs-lookup"><span data-stu-id="5049a-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="5049a-128">Razorje syntaxe pro kombinování značek HTML pomocí kódu jazyka C# navrženého pro produktivitu vývojářů.</span><span class="sxs-lookup"><span data-stu-id="5049a-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="5049a-129">Razorumožňuje přepínat mezi značkami HTML a C# ve stejném souboru s podporou [technologie IntelliSense](/visualstudio/ide/using-intellisense) .</span><span class="sxs-lookup"><span data-stu-id="5049a-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="5049a-130">RazorStránky a MVC také používají Razor .</span><span class="sxs-lookup"><span data-stu-id="5049a-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="5049a-131">Na rozdíl od Razor stránek a MVC, které jsou vytvořeny kolem modelu požadavků a odpovědí, se komponenty používají konkrétně pro logiku a sestavení uživatelského rozhraní na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="5049a-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="5049a-132">Následující Razor kód ukazuje komponentu ( `Dialog.razor` ), která může být vnořena do jiné komponenty:</span><span class="sxs-lookup"><span data-stu-id="5049a-132">The following Razor markup demonstrates a component (`Dialog.razor`), which can be nested within another component:</span></span>

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="5049a-133">Obsah těla ( `ChildContent` ) a názvu () v dialogovém okně `Title` je součástí komponenty, která tuto komponentu používá v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5049a-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="5049a-134">`OnYes`je metoda C# aktivovaná `onclick` událostí tlačítka.</span><span class="sxs-lookup"><span data-stu-id="5049a-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

<span data-ttu-id="5049a-135">Blazorpoužívá přirozené značky HTML pro kompozici uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5049a-135">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="5049a-136">Prvky HTML určují komponenty a atributy značky přecházejí hodnoty do vlastností součásti.</span><span class="sxs-lookup"><span data-stu-id="5049a-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="5049a-137">V následujícím příkladu `Index` komponenta používá `Dialog` komponentu.</span><span class="sxs-lookup"><span data-stu-id="5049a-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="5049a-138">`ChildContent`a `Title` jsou nastaveny pomocí atributů a obsahu `<Dialog>` elementu.</span><span class="sxs-lookup"><span data-stu-id="5049a-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="5049a-139">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="5049a-139">`Pages/Index.razor`:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="5049a-140">Dialogové okno se vykreslí, když `Pages/Index.razor` je v prohlížeči přístupný nadřazený objekt ():</span><span class="sxs-lookup"><span data-stu-id="5049a-140">The dialog is rendered when the parent (`Pages/Index.razor`) is accessed in a browser:</span></span>

![Komponenta dialogového okna vykreslená v prohlížeči](index/_static/dialog.png)

<span data-ttu-id="5049a-142">Pokud se tato komponenta používá v aplikaci, IntelliSense v [aplikaci Visual Studio](/visualstudio/ide/using-intellisense) a [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) zrychluje vývoj pomocí syntaxe a dokončování parametrů.</span><span class="sxs-lookup"><span data-stu-id="5049a-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="5049a-143">Komponenty vykreslí do reprezentace v paměti model DOM (Document Object Model) v prohlížeči (DOM), která se nazývá *strom vykreslování*, který se používá k aktualizaci uživatelského rozhraní flexibilním a efektivním způsobem.</span><span class="sxs-lookup"><span data-stu-id="5049a-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="5049a-144">Blazor WebAssemblyje jediná stránka architektury aplikace pro vytváření interaktivních webových aplikací na straně klienta s .NET.</span><span class="sxs-lookup"><span data-stu-id="5049a-144">Blazor WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="5049a-145">Blazor WebAssemblypoužívá otevřené webové standardy bez modulů plug-in a transpilation kódu a funguje ve všech moderních webových prohlížečích, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="5049a-145">Blazor WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="5049a-146">Spuštění kódu .NET ve webových prohlížečích je umožněno webovým [sestavením](https://webassembly.org) (zkráceně `wasm` ).</span><span class="sxs-lookup"><span data-stu-id="5049a-146">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="5049a-147">WebAssembly je formát kompaktního bajtu optimalizovaného pro rychlé stažení a maximální rychlost spuštění.</span><span class="sxs-lookup"><span data-stu-id="5049a-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="5049a-148">WebAssembly je otevřený webový standard a podporuje se ve webových prohlížečích bez modulů plug-in.</span><span class="sxs-lookup"><span data-stu-id="5049a-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="5049a-149">Kód webového sestavení má přístup k úplným funkcím prohlížeče prostřednictvím JavaScriptu, označovanému jako *interoperabilita JavaScriptu* (nebo *zprostředkovatel komunikace s JavaScriptem*).</span><span class="sxs-lookup"><span data-stu-id="5049a-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="5049a-150">Kód .NET spuštěný pomocí webového sestavení v prohlížeči se spouští v izolovaném prostoru JavaScript v prohlížeči s ochranou, že izolovaný prostor poskytuje proti škodlivým akcím v klientském počítači.</span><span class="sxs-lookup"><span data-stu-id="5049a-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![::: No-Loc (Blazor WebAssembly)::: spustí kód .NET v prohlížeči pomocí webového sestavení.](index/_static/blazor-webassembly.png)

<span data-ttu-id="5049a-152">Když Blazor WebAssembly je aplikace sestavená a spuštěná v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="5049a-152">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="5049a-153">Soubory a soubory kódu jazyka C# Razor jsou zkompilovány do sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="5049a-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="5049a-154">Sestavení a modul runtime .NET se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="5049a-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="5049a-155">Blazor WebAssemblyspustí modul .NET runtime a nakonfiguruje modul runtime, aby načetl sestavení pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5049a-155">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="5049a-156">Blazor WebAssemblyModul runtime používá zprostředkovatele komunikace s JavaScriptem pro zpracování volání DOM a volání rozhraní API prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="5049a-156">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="5049a-157">Velikost publikované aplikace, její *Velikost datové části*, je důležitým faktorem výkonu pro useability aplikace.</span><span class="sxs-lookup"><span data-stu-id="5049a-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="5049a-158">Stažení velké aplikace může trvat poměrně dlouhou dobu, než se stáhne do prohlížeče, což snižuje činnost koncového uživatele.</span><span class="sxs-lookup"><span data-stu-id="5049a-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="5049a-159">Blazor WebAssemblyoptimalizuje velikost datové části, aby se snížila doba stahování:</span><span class="sxs-lookup"><span data-stu-id="5049a-159">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="5049a-160">Nepoužitý kód se z aplikace vyčerpá, když se publikuje pomocí [linkeru pro převodní jazyk (IL)](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="5049a-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="5049a-161">Odpovědi HTTP jsou komprimovány.</span><span class="sxs-lookup"><span data-stu-id="5049a-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="5049a-162">Modul runtime .NET a sestavení jsou ukládány do mezipaměti v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="5049a-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## Blazor Server

<span data-ttu-id="5049a-163">Blazorodpojí logiku vykreslování komponenty od způsobu použití aktualizací uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5049a-163">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="5049a-164">Blazor Serverposkytuje podporu pro hostování Razor komponent na serveru aplikace v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5049a-164">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="5049a-165">Aktualizace uživatelského rozhraní se zpracovávají přes [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="5049a-165">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="5049a-166">Modul runtime zpracovává odeslání událostí uživatelského rozhraní z prohlížeče na server a po spuštění komponent aplikuje aktualizace uživatelského rozhraní odeslané serverem zpátky do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="5049a-166">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="5049a-167">Připojení, které používá Blazor Server ke komunikaci s prohlížečem, slouží také ke zpracování volání interoperability JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5049a-167">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![::: No-Loc (Blazor Server)::: spustí .NET kód na serveru a spolupracuje s model DOM (Document Object Model) na klientovi přes::: No-Loc (signál)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="5049a-169">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="5049a-169">JavaScript interop</span></span>

<span data-ttu-id="5049a-170">Pro aplikace, které vyžadují knihovny JavaScript třetích stran a přístup k rozhraním API prohlížeče, komponenty, které spolupracují s JavaScriptem.</span><span class="sxs-lookup"><span data-stu-id="5049a-170">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="5049a-171">Komponenty umožňují použití libovolné knihovny nebo rozhraní API, které může JavaScript používat.</span><span class="sxs-lookup"><span data-stu-id="5049a-171">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="5049a-172">Kód jazyka c# může zavolat do kódu jazyka JavaScript a kód JavaScriptu může volat kód jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="5049a-172">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="5049a-173">Další informace najdete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="5049a-173">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="5049a-174">Sdílení kódu a .NET Standard</span><span class="sxs-lookup"><span data-stu-id="5049a-174">Code sharing and .NET Standard</span></span>

<span data-ttu-id="5049a-175">Blazorimplementuje [.NET Standard 2,1](/dotnet/standard/net-standard), což umožňuje Blazor projektům odkazovat na knihovny, které odpovídají .NET Standard 2,1 nebo předchozí specifikace.</span><span class="sxs-lookup"><span data-stu-id="5049a-175">Blazor implements [.NET Standard 2.1](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard 2.1 or earlier specifications.</span></span> <span data-ttu-id="5049a-176">.NET Standard je formální specifikace rozhraní .NET API, která jsou společná pro implementace v rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="5049a-176">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="5049a-177">Knihovny tříd .NET Standard lze sdílet napříč různými platformami .NET, jako jsou například Blazor , .NET Framework, .NET Core, Xamarin, mono a Unity.</span><span class="sxs-lookup"><span data-stu-id="5049a-177">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="5049a-178">Rozhraní API, která nejsou platná v rámci webového prohlížeče (například přístup k systému souborů, otevření soketu a vlákna) vyvolávají výjimku <xref:System.PlatformNotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="5049a-178">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5049a-179">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5049a-179">Additional resources</span></span>

* [<span data-ttu-id="5049a-180">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5049a-180">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="5049a-181">Průvodce C#</span><span class="sxs-lookup"><span data-stu-id="5049a-181">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="5049a-182">HTML</span><span class="sxs-lookup"><span data-stu-id="5049a-182">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="5049a-183">[Super Blazor ](https://github.com/AdrienTorris/awesome-blazor) komunitní odkazy</span><span class="sxs-lookup"><span data-stu-id="5049a-183">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
