---
title: Úvod do ASP.NET Core Blazor
author: guardrex
description: Prozkoumejte ASP.NET Core Blazor, způsob sestavení interaktivního webového uživatelského rozhraní na straně klienta s .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 9f43735fc02ba9f8da4ba27c8b5763d587b5429a
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159921"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a><span data-ttu-id="e0220-103">Úvod do ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e0220-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="e0220-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e0220-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e0220-105">*Vítá vás Blazor!*</span><span class="sxs-lookup"><span data-stu-id="e0220-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="e0220-106"> je architektura pro sestavování interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET:</span><span class="sxs-lookup"><span data-stu-id="e0220-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="e0220-107">Vytvářejte bohatě interaktivní uživatelská rozhraní C# pomocí místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e0220-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="e0220-108">Sdílejte logiku aplikace na straně serveru a klienta napsanou v .NET.</span><span class="sxs-lookup"><span data-stu-id="e0220-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="e0220-109">Vykreslete uživatelské rozhraní jako HTML a CSS pro podporu pro rozsáhlou prohlížeč, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="e0220-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="e0220-110">Použití rozhraní .NET pro vývoj webů na straně klienta nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="e0220-110">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="e0220-111">C# Místo JavaScriptu napište kód.</span><span class="sxs-lookup"><span data-stu-id="e0220-111">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="e0220-112">Využijte stávající ekosystém .NET knihoven .NET.</span><span class="sxs-lookup"><span data-stu-id="e0220-112">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="e0220-113">Sdílejte logiku aplikace napříč serverem a klientem.</span><span class="sxs-lookup"><span data-stu-id="e0220-113">Share app logic across server and client.</span></span>
* <span data-ttu-id="e0220-114">Využijte výhod. Výkon, spolehlivost a zabezpečení sítě.</span><span class="sxs-lookup"><span data-stu-id="e0220-114">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="e0220-115">Aplikace Visual Studio v systémech Windows, Linux a macOS vám zůstane produktivní.</span><span class="sxs-lookup"><span data-stu-id="e0220-115">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="e0220-116">Sestavte se na společné sadě jazyků, platforem a nástrojů, které jsou stabilní, funkce s bohatou funkcí a snadno použitelné.</span><span class="sxs-lookup"><span data-stu-id="e0220-116">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="e0220-117">Komponenty</span><span class="sxs-lookup"><span data-stu-id="e0220-117">Components</span></span>

<span data-ttu-id="e0220-118">aplikace Blazor jsou založené na *komponentách*.</span><span class="sxs-lookup"><span data-stu-id="e0220-118">Blazor apps are based on *components*.</span></span> <span data-ttu-id="e0220-119">Součást v Blazor je prvek uživatelského rozhraní, jako je například stránka, dialog nebo formulář pro zadávání dat.</span><span class="sxs-lookup"><span data-stu-id="e0220-119">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="e0220-120">Komponenty jsou třídy .NET integrované do sestavení .NET, která:</span><span class="sxs-lookup"><span data-stu-id="e0220-120">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="e0220-121">Definujte flexibilní logiku vykreslování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e0220-121">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="e0220-122">Zpracování událostí uživatele.</span><span class="sxs-lookup"><span data-stu-id="e0220-122">Handle user events.</span></span>
* <span data-ttu-id="e0220-123">Může být vnořen a znovu použit.</span><span class="sxs-lookup"><span data-stu-id="e0220-123">Can be nested and reused.</span></span>
* <span data-ttu-id="e0220-124">Lze sdílet a distribuovat jako [knihovny tříd Razor](xref:razor-pages/ui-class) nebo [balíčky NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="e0220-124">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="e0220-125">Třída komponenty je obvykle napsána ve formě stránky značek [Razor](xref:mvc/views/razor) s příponou souboru *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="e0220-125">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="e0220-126">Komponenty v Blazor jsou formálně označovány jako *komponenty Razor*.</span><span class="sxs-lookup"><span data-stu-id="e0220-126">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="e0220-127">Razor je syntaxe pro kombinování značek HTML s C# kódem navrženým pro produktivitu vývojářů.</span><span class="sxs-lookup"><span data-stu-id="e0220-127">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="e0220-128">Razor umožňuje přepínat mezi značkami HTML a C# ve stejném souboru s podporou [technologie IntelliSense](/visualstudio/ide/using-intellisense) .</span><span class="sxs-lookup"><span data-stu-id="e0220-128">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="e0220-129">Razor Pages a MVC také používají syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="e0220-129">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="e0220-130">Na rozdíl od Razor Pages a MVC, které jsou vytvořeny kolem modelu požadavků a odpovědí, se komponenty používají konkrétně pro logiku a sestavení uživatelského rozhraní na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e0220-130">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="e0220-131">Následující kód Razor znázorňuje komponentu (*dialog. Razor*), která může být vnořena do jiné komponenty:</span><span class="sxs-lookup"><span data-stu-id="e0220-131">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

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

<span data-ttu-id="e0220-132">Obsah těla dialogového okna (`ChildContent`) a název (`Title`) je součástí komponenty, která tuto komponentu používá v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e0220-132">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="e0220-133">`OnYes` je C# metoda aktivovaná v události `onclick` tlačítka.</span><span class="sxs-lookup"><span data-stu-id="e0220-133">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="e0220-134"> používá přirozené značky HTML pro kompozici uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e0220-134"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="e0220-135">Prvky HTML určují komponenty a atributy značky přecházejí hodnoty do vlastností součásti.</span><span class="sxs-lookup"><span data-stu-id="e0220-135">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="e0220-136">V následujícím příkladu `Index` komponenta používá komponentu `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="e0220-136">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="e0220-137">`ChildContent` a `Title` jsou nastaveny pomocí atributů a obsahu elementu `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="e0220-137">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="e0220-138">*Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="e0220-138">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="e0220-139">Dialog se vykreslí, když se k nadřazenému elementu (*index. Razor*) přistupoval v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="e0220-139">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Komponenta dialogového okna vykreslená v prohlížeči](index/_static/dialog.png)

<span data-ttu-id="e0220-141">Pokud se tato komponenta používá v aplikaci, IntelliSense v [aplikaci Visual Studio](/visualstudio/ide/using-intellisense) a [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) zrychluje vývoj pomocí syntaxe a dokončování parametrů.</span><span class="sxs-lookup"><span data-stu-id="e0220-141">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="e0220-142">Komponenty vykreslí do reprezentace v paměti model DOM (Document Object Model) v prohlížeči (DOM), která se nazývá *strom vykreslování*, který se používá k aktualizaci uživatelského rozhraní flexibilním a efektivním způsobem.</span><span class="sxs-lookup"><span data-stu-id="e0220-142">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="e0220-143"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e0220-143"> WebAssembly</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="e0220-144"> WebAssembly je jednostránková architektura aplikací pro vytváření interaktivních webových aplikací na straně klienta s .NET.</span><span class="sxs-lookup"><span data-stu-id="e0220-144"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="e0220-145"> webové sestavení používá open Web Standards bez modulů plug-in a transpilation kódu a funguje ve všech moderních webových prohlížečích, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="e0220-145"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="e0220-146">Spouštění kódu .NET ve webových prohlížečích je umožněno webovým [sestavením](https://webassembly.org) (zkráceným *wasm*).</span><span class="sxs-lookup"><span data-stu-id="e0220-146">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="e0220-147">WebAssembly je formát kompaktního bajtu optimalizovaného pro rychlé stažení a maximální rychlost spuštění.</span><span class="sxs-lookup"><span data-stu-id="e0220-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="e0220-148">WebAssembly je otevřený webový standard a podporuje se ve webových prohlížečích bez modulů plug-in.</span><span class="sxs-lookup"><span data-stu-id="e0220-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="e0220-149">Kód webového sestavení má přístup k úplným funkcím prohlížeče prostřednictvím JavaScriptu, označovanému jako *interoperabilita JavaScriptu* (nebo *zprostředkovatel komunikace s JavaScriptem*).</span><span class="sxs-lookup"><span data-stu-id="e0220-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="e0220-150">Kód .NET spuštěný pomocí webového sestavení v prohlížeči se spouští v izolovaném prostoru JavaScript v prohlížeči s ochranou, že izolovaný prostor poskytuje proti škodlivým akcím v klientském počítači.</span><span class="sxs-lookup"><span data-stu-id="e0220-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![[! Evřít. NO-LOC (Blazor)]<span data-ttu-id="e0220-151"> WebAssembly spouští kód .NET v prohlížeči pomocí WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="e0220-151"> WebAssembly runs .NET code in the browser with WebAssembly.</span></span>](index/_static/blazor-webassembly.png)

<span data-ttu-id="e0220-152">Při sestavení a spuštění aplikace Blazor WebAssembly v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="e0220-152">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="e0220-153">C#soubory kódu a soubory Razor jsou kompilovány do sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="e0220-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="e0220-154">Sestavení a modul runtime .NET se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e0220-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="e0220-155"> WebAssembly spustí modul runtime .NET a nakonfiguruje modul runtime, aby načetl sestavení pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e0220-155"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="e0220-156">Modul runtime Blazorho webového sestavení používá zprostředkovatele komunikace s JavaScriptem pro zpracování volání modelu DOM a volání rozhraní API prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e0220-156">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="e0220-157">Velikost publikované aplikace, její *Velikost datové části*, je důležitým faktorem výkonu pro useability aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0220-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="e0220-158">Stažení velké aplikace může trvat poměrně dlouhou dobu, než se stáhne do prohlížeče, což snižuje činnost koncového uživatele.</span><span class="sxs-lookup"><span data-stu-id="e0220-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="e0220-159"> WebAssembly optimalizuje velikost datové části, aby se snížila doba stahování:</span><span class="sxs-lookup"><span data-stu-id="e0220-159"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="e0220-160">Nepoužitý kód se z aplikace vyčerpá, když se publikuje pomocí [linkeru pro převodní jazyk (IL)](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="e0220-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="e0220-161">Odpovědi HTTP jsou komprimovány.</span><span class="sxs-lookup"><span data-stu-id="e0220-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="e0220-162">Modul runtime .NET a sestavení jsou ukládány do mezipaměti v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e0220-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="opno-locblazor-server"></a><span data-ttu-id="e0220-163">Server Blazor</span><span class="sxs-lookup"><span data-stu-id="e0220-163">Blazor Server</span></span>

Blazor<span data-ttu-id="e0220-164"> odpojí logiku vykreslování komponenty od způsobu použití aktualizací uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e0220-164"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="e0220-165"> Server poskytuje podporu pro hostování komponent Razor na serveru aplikace v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0220-165"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="e0220-166">Aktualizace uživatelského rozhraní jsou zpracovávány prostřednictvím [SignalRho](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="e0220-166">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="e0220-167">Modul runtime zpracovává odeslání událostí uživatelského rozhraní z prohlížeče na server a po spuštění komponent aplikuje aktualizace uživatelského rozhraní odeslané serverem zpátky do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e0220-167">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="e0220-168">Připojení, které používá Blazor Server ke komunikaci s prohlížečem, slouží také ke zpracování volání interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e0220-168">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![[! Evřít. NO-LOC (Blazor)]<span data-ttu-id="e0220-169"> Server spouští .NET kód na serveru a komunikuje s model DOM (Document Object Model) na klientovi přes [! Evřít. Připojení NO-LOC (Signal)]</span><span class="sxs-lookup"><span data-stu-id="e0220-169"> Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection</span></span>](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="e0220-170">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="e0220-170">JavaScript interop</span></span>

<span data-ttu-id="e0220-171">Pro aplikace, které vyžadují knihovny JavaScript třetích stran a přístup k rozhraním API prohlížeče, komponenty, které spolupracují s JavaScriptem.</span><span class="sxs-lookup"><span data-stu-id="e0220-171">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="e0220-172">Komponenty umožňují použití libovolné knihovny nebo rozhraní API, které může JavaScript používat.</span><span class="sxs-lookup"><span data-stu-id="e0220-172">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="e0220-173">C#kód může zavolat do kódu JavaScriptu a JavaScriptový kód může zavolat do C# kódu.</span><span class="sxs-lookup"><span data-stu-id="e0220-173">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="e0220-174">Další informace najdete v tématu <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="e0220-174">For more information, see <xref:blazor/javascript-interop>.</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="e0220-175">Sdílení kódu a .NET Standard</span><span class="sxs-lookup"><span data-stu-id="e0220-175">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="e0220-176"> implementuje [.NET Standard 2,0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="e0220-176"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="e0220-177">.NET Standard je formální specifikace rozhraní .NET API, která jsou společná pro implementace v rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="e0220-177">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="e0220-178">Knihovny tříd .NET Standard mohou být sdíleny napříč různými platformami .NET, například Blazor, .NET Framework, .NET Core, Xamarin, mono a Unity.</span><span class="sxs-lookup"><span data-stu-id="e0220-178">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="e0220-179">Rozhraní API, která nejsou platná v rámci webového prohlížeče (například přístup k systému souborů, otevření soketu a vlákna) vyvolávají <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="e0220-179">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0220-180">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="e0220-180">Additional resources</span></span>

* [<span data-ttu-id="e0220-181">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e0220-181">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* [<span data-ttu-id="e0220-182">Průvodce jazykem C#</span><span class="sxs-lookup"><span data-stu-id="e0220-182">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="e0220-183">HTML</span><span class="sxs-lookup"><span data-stu-id="e0220-183">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="e0220-184">[Super Blazor](https://github.com/AdrienTorris/awesome-blazor) komunitní odkazy</span><span class="sxs-lookup"><span data-stu-id="e0220-184">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
