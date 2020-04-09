---
title: Úvod do ASP.NET jádraBlazor
author: guardrex
description: Prozkoumejte BlazorASP.NET Core , způsob, jak vytvořit interaktivní webové uživatelské rozhraní na straně klienta s rozhraním .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 6d2e95cd2ec92f97a97cb558fb39e4540450c766
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405956"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a><span data-ttu-id="c448a-103">Úvod do ASP.NET jádraBlazor</span><span class="sxs-lookup"><span data-stu-id="c448a-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="c448a-104">[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c448a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c448a-105">*Vítejte! Blazor*</span><span class="sxs-lookup"><span data-stu-id="c448a-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="c448a-106">je rámec pro vytváření interaktivního webového uživatelského rozhraní na straně klienta s rozhraním .NET:</span><span class="sxs-lookup"><span data-stu-id="c448a-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="c448a-107">Vytvořte bohaté interaktivní umělá použití jazyka C# místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="c448a-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="c448a-108">Sdílejte logiku aplikace na straně serveru a klienta napsanou v rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="c448a-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="c448a-109">Vykreslete ui jako HTML a CSS pro širokou podporu prohlížeče, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="c448a-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="c448a-110">Integrujte se s moderními hostingovými platformami, jako je [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="c448a-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="c448a-111">Použití rozhraní .NET pro vývoj webu na straně klienta nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="c448a-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="c448a-112">Místo JavaScriptu napište kód v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="c448a-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="c448a-113">Využijte existující ekosystém .NET knihoven .NET.</span><span class="sxs-lookup"><span data-stu-id="c448a-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="c448a-114">Sdílejte logiku aplikace napříč serverem a klientem.</span><span class="sxs-lookup"><span data-stu-id="c448a-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="c448a-115">Využijte výhod . net výkon, spolehlivost a zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="c448a-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="c448a-116">Zůstaňte produktivní díky Visual Studiu ve Windows, Linuxu a macOS.</span><span class="sxs-lookup"><span data-stu-id="c448a-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="c448a-117">Nazákladě společné sady jazyků, architektur a nástrojů, které jsou stabilní, bohaté na funkce a snadno použitelné.</span><span class="sxs-lookup"><span data-stu-id="c448a-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="c448a-118">Komponenty</span><span class="sxs-lookup"><span data-stu-id="c448a-118">Components</span></span>

Blazor<span data-ttu-id="c448a-119">aplikace jsou *založeny*na komponentách .</span><span class="sxs-lookup"><span data-stu-id="c448a-119"> apps are based on *components*.</span></span> <span data-ttu-id="c448a-120">Komponenta Blazor v aplikaci je prvek ui, například stránka, dialog nebo formulář pro zadávání dat.</span><span class="sxs-lookup"><span data-stu-id="c448a-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="c448a-121">Součásti jsou třídy .NET integrované do sestavení .NET, které:</span><span class="sxs-lookup"><span data-stu-id="c448a-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="c448a-122">Definujte flexibilní logiku vykreslování ui.</span><span class="sxs-lookup"><span data-stu-id="c448a-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="c448a-123">Zpracování uživatelských událostí.</span><span class="sxs-lookup"><span data-stu-id="c448a-123">Handle user events.</span></span>
* <span data-ttu-id="c448a-124">Může být vnořen a znovu použit.</span><span class="sxs-lookup"><span data-stu-id="c448a-124">Can be nested and reused.</span></span>
* <span data-ttu-id="c448a-125">Lze sdílet a distribuovat jako [knihovny třídrazor](xref:razor-pages/ui-class) nebo [balíčky NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="c448a-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="c448a-126">Třída komponenty je obvykle napsána ve formě stránky [značek Razor](xref:mvc/views/razor) s *příponou .razor.*</span><span class="sxs-lookup"><span data-stu-id="c448a-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="c448a-127">Komponenty Blazor v jsou formálně *označovány*jako komponenty Razor .</span><span class="sxs-lookup"><span data-stu-id="c448a-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="c448a-128">Razor je syntaxe pro kombinaci značek HTML s kódem Jazyka C# určeným pro produktivitu vývojářů.</span><span class="sxs-lookup"><span data-stu-id="c448a-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="c448a-129">Razor umožňuje přepínat mezi značkami HTML a C# ve stejném souboru s podporou [Technologie IntelliSense.](/visualstudio/ide/using-intellisense)</span><span class="sxs-lookup"><span data-stu-id="c448a-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="c448a-130">Razor Pages a MVC také používají Razor.</span><span class="sxs-lookup"><span data-stu-id="c448a-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="c448a-131">Na rozdíl od Razor Pages a MVC, které jsou postaveny na modelu požadavku a odpovědi, komponenty se používají speciálně pro logiku a složení uživatelského panelu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="c448a-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="c448a-132">Následující značka Razor ukazuje komponentu (*Dialog.razor*), kterou lze vnořit do jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="c448a-132">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

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

<span data-ttu-id="c448a-133">Obsah těla dialogového`ChildContent`okna (`Title`) a název ( ) jsou poskytovány komponentou, která používá tuto komponentu v jejím unovém uzly.</span><span class="sxs-lookup"><span data-stu-id="c448a-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="c448a-134">`OnYes`je metoda Jazyka C# svolaná `onclick` událostí tlačítka.</span><span class="sxs-lookup"><span data-stu-id="c448a-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="c448a-135">používá přirozené značky HTML pro kompozici ui.</span><span class="sxs-lookup"><span data-stu-id="c448a-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="c448a-136">Elementy HTML určují komponenty a atributy značky předávají hodnoty vlastnostem komponenty.</span><span class="sxs-lookup"><span data-stu-id="c448a-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="c448a-137">V následujícím příkladu `Index` komponenta `Dialog` používá komponentu.</span><span class="sxs-lookup"><span data-stu-id="c448a-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="c448a-138">`ChildContent`a `Title` jsou nastaveny atributy a `<Dialog>` obsah prvku.</span><span class="sxs-lookup"><span data-stu-id="c448a-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="c448a-139">*Index.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="c448a-139">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="c448a-140">Dialogové okno se vykreslí, když je rodič (*Index.razor*) přístupný v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="c448a-140">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Komponenta dialogového okna vykreslená v prohlížeči](index/_static/dialog.png)

<span data-ttu-id="c448a-142">Při použití této součásti v aplikaci intelliSense v [sadě Visual Studio](/visualstudio/ide/using-intellisense) a Visual Studio [Code](https://code.visualstudio.com/docs/editor/intellisense) urychluje vývoj pomocí syntaxe a dokončení parametrů.</span><span class="sxs-lookup"><span data-stu-id="c448a-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="c448a-143">Součásti vykreslují do reprezentace v paměti prohlížeče Document Object Model (DOM) nazývaného *strom vykreslení*, který se používá k aktualizaci ui flexibilním a efektivním způsobem.</span><span class="sxs-lookup"><span data-stu-id="c448a-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="c448a-144">Webová sestava</span><span class="sxs-lookup"><span data-stu-id="c448a-144"> WebAssembly</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="c448a-145">WebAssembly je jednostránkový rámec aplikací pro vytváření interaktivních webových aplikací na straně klienta s rozhraním .NET.</span><span class="sxs-lookup"><span data-stu-id="c448a-145"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="c448a-146">WebAssembly používá otevřené webové standardy bez pluginů nebo transpilace kódu a funguje ve všech moderních webových prohlížečích, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="c448a-146"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="c448a-147">Spuštění kódu .NET uvnitř webových prohlížečů je možné pomocí [WebAssembly](https://webassembly.org) (zkráceně *wasm*).</span><span class="sxs-lookup"><span data-stu-id="c448a-147">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="c448a-148">WebAssembly je kompaktní formát bytecode optimalizovaný pro rychlé stahování a maximální rychlost provádění.</span><span class="sxs-lookup"><span data-stu-id="c448a-148">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="c448a-149">WebAssembly je otevřený webový standard a je podporován ve webových prohlížečích bez pluginů.</span><span class="sxs-lookup"><span data-stu-id="c448a-149">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="c448a-150">WebAssembly kód může přistupovat k plné funkčnosti prohlížeče přes JavaScript, volal *JavaScript interop* ( nebo *JavaScript interop*).</span><span class="sxs-lookup"><span data-stu-id="c448a-150">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="c448a-151">Kód .NET spuštěný prostřednictvím webové sestavy v prohlížeči běží v izolovaném prostoru JavaScriptu prohlížeče s ochranou, kterou izolovaného prostoru poskytuje proti škodlivým akcím v klientském počítači.</span><span class="sxs-lookup"><span data-stu-id="c448a-151">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="c448a-152">![BlazorWebAssembly spustí kód .NET v prohlížeči s WebAssembly.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="c448a-152">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="c448a-153">Když Blazor je aplikace WebAssembly vytvořena a spuštěna v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="c448a-153">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="c448a-154">Soubory kódu Jazyka C# a Razor jsou kompilovány do sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="c448a-154">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="c448a-155">Sestavení a za běhu .NET jsou staženy do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c448a-155">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="c448a-156">WebAssembly zavede zaběhovou dobu .NET a nakonfiguruje dobu runtime tak, aby načítaná sestavení pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c448a-156"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="c448a-157">Runtime Blazor WebAssembly používá javascriptový interop ke zpracování manipulace s dom a volání rozhraní API prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c448a-157">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="c448a-158">Velikost publikované aplikace, její *velikost datové části*, je kritickým faktorem výkonu pro použití aplikace.</span><span class="sxs-lookup"><span data-stu-id="c448a-158">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="c448a-159">Vytvoření velké aplikace trvá poměrně dlouho, než se stáhne do prohlížeče, což snižuje uživatelské prostředí.</span><span class="sxs-lookup"><span data-stu-id="c448a-159">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="c448a-160">WebAssembly optimalizuje velikost datové části, aby se zkrátila doba stahování:</span><span class="sxs-lookup"><span data-stu-id="c448a-160"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="c448a-161">Nepoužívaný kód je odebrán z aplikace, když je publikován [linkerem zprostředkující jazyk (IL).](xref:host-and-deploy/blazor/configure-linker)</span><span class="sxs-lookup"><span data-stu-id="c448a-161">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="c448a-162">Odpovědi HTTP jsou komprimovány.</span><span class="sxs-lookup"><span data-stu-id="c448a-162">HTTP responses are compressed.</span></span>
* <span data-ttu-id="c448a-163">Doba běhu rozhraní .NET a sestavení jsou ukládány do mezipaměti v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="c448a-163">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="c448a-164">Server</span><span class="sxs-lookup"><span data-stu-id="c448a-164"> Server</span></span>

Blazor<span data-ttu-id="c448a-165">odděluje logiku vykreslování komponent od způsobu použití aktualizací ui.</span><span class="sxs-lookup"><span data-stu-id="c448a-165"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="c448a-166">Server poskytuje podporu pro hostování komponent Razor na serveru v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c448a-166"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="c448a-167">Aktualizace ui jsou [SignalR](xref:signalr/introduction) zpracovávány přes připojení.</span><span class="sxs-lookup"><span data-stu-id="c448a-167">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="c448a-168">Runtime zpracovává odesílání událostí uznacího z prohlížeče na server a použije aktualizace ui odeslané serverem zpět do prohlížeče po spuštění komponent.</span><span class="sxs-lookup"><span data-stu-id="c448a-168">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="c448a-169">Připojení používané Blazor serverem ke komunikaci s prohlížečem se také používá ke zpracování volání interop jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c448a-169">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="c448a-170">![BlazorServer spouští kód .NET na serveru a spolupracuje s objektovým modelem dokumentu na straně klienta SignalR přes připojení](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="c448a-170">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="c448a-171">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="c448a-171">JavaScript interop</span></span>

<span data-ttu-id="c448a-172">U aplikací, které vyžadují javascriptové knihovny třetích stran a přístup k apim prohlížeče, komponenty spolupracují s JavaScriptem.</span><span class="sxs-lookup"><span data-stu-id="c448a-172">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="c448a-173">Komponenty jsou schopny používat libovolnou knihovnu nebo rozhraní API, které je JavaScript schopen používat.</span><span class="sxs-lookup"><span data-stu-id="c448a-173">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="c448a-174">Kód Jazyka C# může volat do kódu JavaScript u a kód JavaScriptu může volat do kódu Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="c448a-174">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="c448a-175">Další informace najdete v těchto článcích:</span><span class="sxs-lookup"><span data-stu-id="c448a-175">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="c448a-176">Sdílení kódu a standard .NET</span><span class="sxs-lookup"><span data-stu-id="c448a-176">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="c448a-177">implementuje [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="c448a-177"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="c448a-178">.NET Standard je formální specifikace rozhraní API ROZHRANÍ .NET, která jsou společná v implementacích rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="c448a-178">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="c448a-179">Knihovny tříd .NET Standard lze sdílet na různých Blazorplatformách .NET, například , .NET Framework, .NET Core, Xamarin, Mono a Unity.</span><span class="sxs-lookup"><span data-stu-id="c448a-179">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="c448a-180">Rozhraní API, která nejsou použitelná uvnitř webového prohlížeče (například přístup k systému souborů, <xref:System.PlatformNotSupportedException>otevření soketu a zřetězení) vyvolat .</span><span class="sxs-lookup"><span data-stu-id="c448a-180">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c448a-181">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c448a-181">Additional resources</span></span>

* [<span data-ttu-id="c448a-182">Webová sestava</span><span class="sxs-lookup"><span data-stu-id="c448a-182">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="c448a-183">Průvodce C#</span><span class="sxs-lookup"><span data-stu-id="c448a-183">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="c448a-184">HTML</span><span class="sxs-lookup"><span data-stu-id="c448a-184">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="c448a-185">[Úžasné Blazor ](https://github.com/AdrienTorris/awesome-blazor) komunitní odkazy</span><span class="sxs-lookup"><span data-stu-id="c448a-185">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
