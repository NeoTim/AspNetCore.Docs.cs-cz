---
title: Úvod do ASP.NET Core Blazor
author: guardrex
description: Prozkoumejte ASP.NET Core Blazor , způsob sestavení interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 09/25/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: 7ad374fdc7452664e3367d6ef214fd4cebdf3b08
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805518"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="c4c4d-103">Úvod do ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c4c4d-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="c4c4d-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c4c4d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c4c4d-105">*Vítá vás Blazor !*</span><span class="sxs-lookup"><span data-stu-id="c4c4d-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="c4c4d-106">Blazor je rozhraní pro sestavování interaktivního webového uživatelského rozhraní na straně klienta pomocí [.NET](/dotnet/standard/tour):</span><span class="sxs-lookup"><span data-stu-id="c4c4d-106">Blazor is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="c4c4d-107">Vytvářejte bohatá interaktivní uživatelská rozhraní pomocí [jazyka C#](/dotnet/csharp/) namísto [JavaScriptu](https://www.javascript.com).</span><span class="sxs-lookup"><span data-stu-id="c4c4d-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="c4c4d-108">Sdílejte logiku aplikace na straně serveru a klienta napsanou v .NET.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="c4c4d-109">Vykreslete uživatelské rozhraní jako HTML a CSS pro podporu pro rozsáhlou prohlížeč, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="c4c4d-110">Integrujte s moderními hostujícími platformami, jako je například [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="c4c4d-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="c4c4d-111">Použití rozhraní .NET pro vývoj webů na straně klienta nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="c4c4d-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="c4c4d-112">Psát kód v jazyce C# namísto JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="c4c4d-113">Využijte stávající ekosystém .NET [knihoven .NET](/dotnet/standard/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="c4c4d-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="c4c4d-114">Sdílejte logiku aplikace napříč serverem a klientem.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="c4c4d-115">Využijte výhod. Výkon, spolehlivost a zabezpečení sítě.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="c4c4d-116">[Aplikace Visual Studio](https://visualstudio.microsoft.com) v systémech Windows, Linux a MacOS vám zůstane produktivní.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="c4c4d-117">Sestavte se na společné sadě jazyků, platforem a nástrojů, které jsou stabilní, funkce s bohatou funkcí a snadno použitelné.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="c4c4d-118">Komponenty</span><span class="sxs-lookup"><span data-stu-id="c4c4d-118">Components</span></span>

<span data-ttu-id="c4c4d-119">Blazor aplikace jsou založené na *komponentách*.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="c4c4d-120">Komponenta v nástroji Blazor je prvek uživatelského rozhraní, jako je například stránka, dialog nebo formulář pro zadávání dat.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="c4c4d-121">Komponenty jsou třídy .NET C# sestavené do [sestavení .NET](/dotnet/standard/assembly/) , která:</span><span class="sxs-lookup"><span data-stu-id="c4c4d-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="c4c4d-122">Definujte flexibilní logiku vykreslování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="c4c4d-123">Zpracování událostí uživatele.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-123">Handle user events.</span></span>
* <span data-ttu-id="c4c4d-124">Může být vnořen a znovu použit.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-124">Can be nested and reused.</span></span>
* <span data-ttu-id="c4c4d-125">Lze sdílet a distribuovat jako [ Razor knihovny tříd](xref:razor-pages/ui-class) nebo [balíčky NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="c4c4d-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="c4c4d-126">Třída komponenty je obvykle napsána ve formě [Razor](xref:mvc/views/razor) stránky s označením s `.razor` příponou souboru.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="c4c4d-127">Komponenty v nástroji Blazor jsou formálně označovány jako \* Razor komponenty\*.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="c4c4d-128">Razor je syntaxe pro kombinování značek HTML pomocí kódu jazyka C# navrženého pro produktivitu vývojářů.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="c4c4d-129">Razor umožňuje přepínat mezi značkami HTML a C# ve stejném souboru s podporou programování [IntelliSense](/visualstudio/ide/using-intellisense) v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="c4c4d-130">Razor Stránky a MVC také používají Razor .</span><span class="sxs-lookup"><span data-stu-id="c4c4d-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="c4c4d-131">Na rozdíl od Razor stránek a MVC, které jsou vytvořeny kolem modelu požadavků a odpovědí, se komponenty používají konkrétně pro logiku a sestavení uživatelského rozhraní na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="c4c4d-132">Blazor používá přirozené značky HTML pro kompozici uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-132">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="c4c4d-133">Následující Razor kód ukazuje komponentu ( `Dialog.razor` ), která zobrazuje dialogové okno a zpracovává událost, když uživatel vybere tlačítko:</span><span class="sxs-lookup"><span data-stu-id="c4c4d-133">The following Razor markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

<span data-ttu-id="c4c4d-134">V předchozím příkladu `OnYes` je metoda C# aktivovaná `onclick` událostí tlačítka.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="c4c4d-135">Text ( `ChildContent` ) a title () v dialogovém okně `Title` jsou poskytovány následující komponentou, která tuto komponentu používá v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="c4c4d-136">`Dialog`Komponenta je vnořena do jiné komponenty pomocí značky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="c4c4d-137">V následujícím příkladu `Index` Komponenta ( `Pages/Index.razor` ) používá předchozí `Dialog` komponentu.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="c4c4d-138">`Title`Atribut tagu předává hodnotu pro název `Dialog` `Title` Vlastnosti komponenty.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="c4c4d-139">`Dialog`Text součásti ( `ChildContent` ) je nastaven podle obsahu `<Dialog>` elementu.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="c4c4d-140">Při `Dialog` Přidání komponenty do `Index` komponenty [IntelliSense v aplikaci Visual Studio](/visualstudio/ide/using-intellisense) urychlí vývoj pomocí syntaxe a dokončování parametrů.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="c4c4d-141">Dialogové okno se vykreslí, když `Index` se k součásti dostanete v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="c4c4d-142">Když uživatel vybere tlačítko, v konzole nástroje pro vývojáře v prohlížeči se zobrazí zpráva zapsaná `OnYes` metodou:</span><span class="sxs-lookup"><span data-stu-id="c4c4d-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![Komponenta dialogového okna vykreslená v prohlížeči, která je vnořená v rámci součásti indexu.](index/_static/dialog.png)

<span data-ttu-id="c4c4d-146">Komponenty vykreslí do reprezentace v paměti model DOM (Document Object Model) v prohlížeči [(DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) , která se nazývá *strom vykreslování*, který se používá k aktualizaci uživatelského rozhraní flexibilním a efektivním způsobem.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="c4c4d-147">Blazor WebAssembly je [Architektura jednostránkové aplikace (Spa)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) pro vytváření interaktivních webových aplikací na straně klienta s .NET.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-147">Blazor WebAssembly is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="c4c4d-148">Blazor WebAssembly používá otevřené webové standardy bez modulů plug-in nebo opětovné kompilování kódu do jiných jazyků.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-148">Blazor WebAssembly uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="c4c4d-149">Blazor WebAssembly funguje ve všech moderních webových prohlížečích, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-149">Blazor WebAssembly works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="c4c4d-150">Spuštění kódu .NET ve webových prohlížečích je umožněno webovým [sestavením](https://webassembly.org) (zkráceně `wasm` ).</span><span class="sxs-lookup"><span data-stu-id="c4c4d-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="c4c4d-151">WebAssembly je formát kompaktního bajtu optimalizovaného pro rychlé stažení a maximální rychlost spuštění.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="c4c4d-152">WebAssembly je otevřený webový standard a podporuje se ve webových prohlížečích bez modulů plug-in.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="c4c4d-153">Kód webového sestavení má přístup k úplným funkcím prohlížeče prostřednictvím JavaScriptu, který se označuje jako *interoperabilita JavaScriptu*, často se zkracuje na interoperabilitu *JavaScript* nebo *js*.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability*, often shortened to *JavaScript interop* or *JS interop*.</span></span> <span data-ttu-id="c4c4d-154">Kód .NET spuštěný pomocí webového sestavení v prohlížeči se spouští v izolovaném prostoru JavaScript v prohlížeči s ochranou, že izolovaný prostor poskytuje proti škodlivým akcím v klientském počítači.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![::: No-Loc (Blazor WebAssembly)::: spustí kód .NET v prohlížeči pomocí webového sestavení.](index/_static/blazor-webassembly.png)

<span data-ttu-id="c4c4d-156">Když Blazor WebAssembly je aplikace sestavená a spuštěná v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="c4c4d-156">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="c4c4d-157">Soubory a soubory kódu jazyka C# Razor jsou zkompilovány do sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-157">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="c4c4d-158">Sestavení a [modul runtime .NET](/dotnet/framework/get-started/overview) se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="c4c4d-159">Blazor WebAssembly spustí modul .NET runtime a nakonfiguruje modul runtime, aby načetl sestavení pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-159">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="c4c4d-160">Blazor WebAssemblyModul runtime používá zprostředkovatele komunikace s JavaScriptem pro zpracování volání DOM a volání rozhraní API prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-160">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="c4c4d-161">Velikost publikované aplikace, její *Velikost datové části*, je důležitým faktorem výkonu pro useability aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-161">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="c4c4d-162">Stažení velké aplikace může trvat poměrně dlouhou dobu, než se stáhne do prohlížeče, což snižuje činnost koncového uživatele.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="c4c4d-163">Blazor WebAssembly optimalizuje velikost datové části, aby se snížila doba stahování:</span><span class="sxs-lookup"><span data-stu-id="c4c4d-163">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="c4c4d-164">Nepoužitý kód se z aplikace vyčerpá, když se publikuje pomocí [oříznutí prostředního jazyka (IL)](xref:blazor/host-and-deploy/configure-trimmer).</span><span class="sxs-lookup"><span data-stu-id="c4c4d-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="c4c4d-165">Odpovědi HTTP jsou komprimovány.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="c4c4d-166">Modul runtime .NET a sestavení jsou ukládány do mezipaměti v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="c4c4d-167">Nepoužitý kód se z aplikace vyčerpá, když se publikuje pomocí [linkeru pro převodní jazyk (IL)](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="c4c4d-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="c4c4d-168">Odpovědi HTTP jsou komprimovány.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="c4c4d-169">Modul runtime .NET a sestavení jsou ukládány do mezipaměti v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="c4c4d-170">Blazor odpojí logiku vykreslování komponenty od způsobu použití aktualizací uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-170">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="c4c4d-171">*Blazor Server* poskytuje podporu pro hostování Razor komponent na serveru aplikace v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-171">*Blazor Server* provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="c4c4d-172">Aktualizace uživatelského rozhraní se zpracovávají přes [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="c4c4d-173">Modul runtime zpracovává:</span><span class="sxs-lookup"><span data-stu-id="c4c4d-173">The runtime handles:</span></span>

* <span data-ttu-id="c4c4d-174">Odesílání událostí uživatelského rozhraní z prohlížeče na server.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="c4c4d-175">Použití aktualizací uživatelského rozhraní pro vykreslenou komponentu, která je poslána zpět serverem.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="c4c4d-176">Připojení, které používá Blazor Server ke komunikaci s prohlížečem, slouží také ke zpracování volání interoperability JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-176">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![::: No-Loc (Blazor Server)::: spustí .NET kód na serveru a spolupracuje s model DOM (Document Object Model) na klientovi přes::: No-Loc (signál)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="c4c4d-178">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="c4c4d-178">JavaScript interop</span></span>

<span data-ttu-id="c4c4d-179">Pro aplikace, které vyžadují knihovny JavaScript třetích stran a přístup k rozhraním API prohlížeče, komponenty, které spolupracují s JavaScriptem.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="c4c4d-180">Komponenty umožňují použití libovolné knihovny nebo rozhraní API, které může JavaScript používat.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="c4c4d-181">Kód jazyka c# může [zavolat do kódu jazyka JavaScript](xref:blazor/call-javascript-from-dotnet)a kód JavaScriptu může [volat kód jazyka c#](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="c4c4d-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="c4c4d-182">Sdílení kódu a .NET Standard</span><span class="sxs-lookup"><span data-stu-id="c4c4d-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="c4c4d-183">Blazor implementuje [.NET Standard](/dotnet/standard/net-standard), který umožňuje Blazor projektům odkazovat na knihovny, které odpovídají specifikacím .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-183">Blazor implements the [.NET Standard](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="c4c4d-184">.NET Standard je formální specifikace rozhraní .NET API, která jsou společná pro implementace v rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="c4c4d-185">Knihovny tříd .NET Standard lze sdílet napříč různými platformami .NET, jako jsou například Blazor , .NET Framework, .NET Core, Xamarin, mono a Unity.</span><span class="sxs-lookup"><span data-stu-id="c4c4d-185">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="c4c4d-186">Rozhraní API, která nejsou platná v rámci webového prohlížeče (například přístup k systému souborů, otevření soketu a vlákna) vyvolávají výjimku <xref:System.PlatformNotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="c4c4d-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4c4d-187">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="c4c4d-187">Additional resources</span></span>

* [<span data-ttu-id="c4c4d-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c4c4d-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="c4c4d-189">C# Guide</span><span class="sxs-lookup"><span data-stu-id="c4c4d-189">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="c4c4d-190">HTML</span><span class="sxs-lookup"><span data-stu-id="c4c4d-190">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="c4c4d-191">[Super Blazor ](https://github.com/AdrienTorris/awesome-blazor) komunitní odkazy</span><span class="sxs-lookup"><span data-stu-id="c4c4d-191">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
