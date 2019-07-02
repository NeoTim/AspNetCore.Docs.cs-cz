---
title: Úvod do Blazor v ASP.NET Core
author: guardrex
description: Prozkoumejte službu ASP.NET Core Blazor způsob, jak vytvářet interaktivní webové na straně klienta uživatelské rozhraní s využitím .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 07/01/2019
uid: blazor/index
ms.openlocfilehash: dbfadf9481cf16279e2a491bc04f51058e1d8a9c
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500418"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="e63eb-103">Úvod do Blazor</span><span class="sxs-lookup"><span data-stu-id="e63eb-103">Introduction to Blazor</span></span>

<span data-ttu-id="e63eb-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e63eb-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e63eb-105">*Vítá vás Blazor!*</span><span class="sxs-lookup"><span data-stu-id="e63eb-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="e63eb-106">Blazor je architektura určená k vytváření interaktivní webové uživatelské rozhraní s .NET na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="e63eb-106">Blazor is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="e63eb-107">Vytvářet bohaté interaktivní uživatelské rozhraní pomocí C# místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e63eb-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="e63eb-108">Sdílet logiku na straně serveru a klientské aplikace napsané v rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="e63eb-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="e63eb-109">Vykreslení uživatelského rozhraní jako HTML a CSS pro podporu široké prohlížeče, včetně mobilních.</span><span class="sxs-lookup"><span data-stu-id="e63eb-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="e63eb-110">Pomocí rozhraní .NET pro vývoj webů na straně klienta nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="e63eb-110">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="e63eb-111">Psaní kódu v C# místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e63eb-111">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="e63eb-112">Využijte stávající ekosystému .NET knihoven .NET.</span><span class="sxs-lookup"><span data-stu-id="e63eb-112">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="e63eb-113">Aplikace logiky sdílet mezi serverem a klientem.</span><span class="sxs-lookup"><span data-stu-id="e63eb-113">Share app logic across server and client.</span></span>
* <span data-ttu-id="e63eb-114">Výhody. NET jeho výkonu, spolehlivosti a zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e63eb-114">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="e63eb-115">Produktivní práci s aplikací Visual Studio ve Windows, Linuxu a macOS.</span><span class="sxs-lookup"><span data-stu-id="e63eb-115">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="e63eb-116">Sestavení na společnou sadu jazyky, platformy a nástroje, které jsou stabilní, plně funkční a snadno se používá.</span><span class="sxs-lookup"><span data-stu-id="e63eb-116">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="e63eb-117">Komponenty</span><span class="sxs-lookup"><span data-stu-id="e63eb-117">Components</span></span>

<span data-ttu-id="e63eb-118">Blazor aplikací jsou založené na *komponenty*.</span><span class="sxs-lookup"><span data-stu-id="e63eb-118">Blazor apps are based on *components*.</span></span> <span data-ttu-id="e63eb-119">Komponenta v Blazor je prvek uživatelského rozhraní, jako je například formulář položka stránky, dialogové okno nebo data.</span><span class="sxs-lookup"><span data-stu-id="e63eb-119">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="e63eb-120">Komponenty jsou součástí sestavení .NET třídy rozhraní .NET, který:</span><span class="sxs-lookup"><span data-stu-id="e63eb-120">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="e63eb-121">Definujte flexibilní logiku pro vykreslení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e63eb-121">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="e63eb-122">Zpracování událostí uživatele.</span><span class="sxs-lookup"><span data-stu-id="e63eb-122">Handle user events.</span></span>
* <span data-ttu-id="e63eb-123">Mohou být vnořené a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="e63eb-123">Can be nested and reused.</span></span>
* <span data-ttu-id="e63eb-124">Lze sdílet a distribuovat jako [knihovny tříd Razor](xref:razor-pages/ui-class) nebo [balíčky NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="e63eb-124">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="e63eb-125">Komponentní třída jsou obvykle zapsána ve formě [Razor](xref:mvc/views/razor) stránku značek *.razor* příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="e63eb-125">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="e63eb-126">Součástí Blazor formálně označují jako *Razor komponenty*.</span><span class="sxs-lookup"><span data-stu-id="e63eb-126">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="e63eb-127">Razor je syntaxe pro kombinování značka jazyka HTML s C# kód určený pro produktivitu vývojářů.</span><span class="sxs-lookup"><span data-stu-id="e63eb-127">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="e63eb-128">Razor umožňuje přepínat mezi značka jazyka HTML a C# ve stejném souboru s [IntelliSense](/visualstudio/ide/using-intellisense) podporovat.</span><span class="sxs-lookup"><span data-stu-id="e63eb-128">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="e63eb-129">Stránky Razor a MVC také použít syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="e63eb-129">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="e63eb-130">Na rozdíl od Razor Pages a MVC, které jsou postavené na modelu žádost odpověď, používají součásti speciálně pro logika uživatelského rozhraní na straně klienta a skládání.</span><span class="sxs-lookup"><span data-stu-id="e63eb-130">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="e63eb-131">Následující kód Razor ukazuje komponentu (*Dialog.razor*), který může být vnořena do jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="e63eb-131">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

```cshtml
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="@OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    private string Title { get; set; }

    [Parameter]
    private RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="e63eb-132">Dialogové okno textový obsah (`ChildContent`) a název (`Title`) jsou poskytovány komponenta, která používá tuto součást ve svém uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e63eb-132">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="e63eb-133">`OnYes` je C# metoda aktivované pomocí tlačítka `onclick` událostí.</span><span class="sxs-lookup"><span data-stu-id="e63eb-133">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

<span data-ttu-id="e63eb-134">Blazor používá přirozené značky HTML pro kompozici uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e63eb-134">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="e63eb-135">Určete součásti elementů HTML a atributy značky předat hodnoty vlastnosti komponenty.</span><span class="sxs-lookup"><span data-stu-id="e63eb-135">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="e63eb-136">V následujícím příkladu `Index` komponenta používá `Dialog` komponenty.</span><span class="sxs-lookup"><span data-stu-id="e63eb-136">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="e63eb-137">`ChildContent` a `Title` jsou nastavené tak, že atributy a obsah `<Dialog>` elementu.</span><span class="sxs-lookup"><span data-stu-id="e63eb-137">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="e63eb-138">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="e63eb-138">*Index.razor*:</span></span>

```cshtml
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="e63eb-139">Dialogového okna je vykreslen během nadřazeného (*Index.razor*) je přístupná v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="e63eb-139">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Komponenty dialogového okna vykreslení v prohlížeči](index/_static/dialog.png)

<span data-ttu-id="e63eb-141">Při použití této součásti v aplikaci, technologie IntelliSense v [sady Visual Studio](/visualstudio/ide/using-intellisense) a [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) urychluje vývoj aplikací pomocí syntaxe a parametrů dokončení.</span><span class="sxs-lookup"><span data-stu-id="e63eb-141">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="e63eb-142">Komponenty vykreslování do reprezentaci v paměti z prohlížeče Document Object Model (DOM) volá *vykreslení stromu*, který se používá k aktualizaci uživatelského rozhraní tak flexibilní a efektivní.</span><span class="sxs-lookup"><span data-stu-id="e63eb-142">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-client-side"></a><span data-ttu-id="e63eb-143">Blazor na straně klienta</span><span class="sxs-lookup"><span data-stu-id="e63eb-143">Blazor client-side</span></span>

<span data-ttu-id="e63eb-144">Blazor na straně klienta je jednostránkové aplikace rozhraní pro vytváření aplikací pro interaktivní webové na straně klienta s .NET.</span><span class="sxs-lookup"><span data-stu-id="e63eb-144">Blazor client-side is a single-page app framework for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="e63eb-145">Blazor na straně klienta používá otevřené webové standardy bez transpilation moduly plug-in nebo kódu a pracuje ve všechny moderní webové prohlížeče, včetně mobilních.</span><span class="sxs-lookup"><span data-stu-id="e63eb-145">Blazor client-side uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="e63eb-146">Spouštění kódu .NET uvnitř webových prohlížečů je možné podle [WebAssembly](http://webassembly.org) (zkrácené *wasm*).</span><span class="sxs-lookup"><span data-stu-id="e63eb-146">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="e63eb-147">WebAssembly je formát compact bajtového kódu optimalizovaný pro rychlé stažení a spuštění maximální rychlost.</span><span class="sxs-lookup"><span data-stu-id="e63eb-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="e63eb-148">WebAssembly je otevřít web, standard a podporovaných webových prohlížečů bez modulů plug-in.</span><span class="sxs-lookup"><span data-stu-id="e63eb-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="e63eb-149">WebAssembly kód může přistupovat k úplné funkce aplikace prohlížeče prostřednictvím JavaScriptu, volá *vzájemná funkční spolupráce jazyka JavaScript* (nebo *zprostředkovatele komunikace s objekty jazyka JavaScript*).</span><span class="sxs-lookup"><span data-stu-id="e63eb-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="e63eb-150">.NET kód proveden prostřednictvím WebAssembly v prohlížeči se spustí v sandboxu jazyka JavaScript v prohlížeči pomocí ochrany, které poskytuje izolovaný prostor proti škodlivé akce v klientském počítači.</span><span class="sxs-lookup"><span data-stu-id="e63eb-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![Blazor na straně klienta běží v prohlížeči s WebAssembly kód .NET.](index/_static/blazor-client-side.png)

<span data-ttu-id="e63eb-152">Aplikace na straně klienta Blazor při vytvoření a spuštění v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="e63eb-152">When a Blazor client-side app is built and run in a browser:</span></span>

* <span data-ttu-id="e63eb-153">C#soubory kódu a Razor soubory jsou zkompilovány do sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="e63eb-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="e63eb-154">Sestavení a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e63eb-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="e63eb-155">Blazor klientské bootstraps modul .NET runtime a konfiguruje modul runtime k načtení sestavení pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e63eb-155">Blazor client-side bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="e63eb-156">Modul runtime Blazor na straně klienta používá zprostředkovatele komunikace s objekty jazyka JavaScript ke zpracování modelu DOM manipulaci a prohlížeče volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e63eb-156">The Blazor client-side runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="e63eb-157">Velikost publikované aplikace její *velikost datové části*, je důležité výkonu koeficient pro použitelnost aplikace míra.</span><span class="sxs-lookup"><span data-stu-id="e63eb-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="e63eb-158">Velké aplikace trvá poměrně dlouhou dobu stahování v prohlížeči, který snižuje činnost koncového uživatele.</span><span class="sxs-lookup"><span data-stu-id="e63eb-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="e63eb-159">Klientské Blazor optimalizuje velikost datové části snížit dobu stahování:</span><span class="sxs-lookup"><span data-stu-id="e63eb-159">Blazor client-side optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="e63eb-160">Nepoužitý kód je odebrána z aplikace, když se publikuje [Intermediate Language (IL) Linkeru](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="e63eb-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="e63eb-161">Odpovědi protokolu HTTP jsou komprimované.</span><span class="sxs-lookup"><span data-stu-id="e63eb-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="e63eb-162">Modul runtime rozhraní .NET a sestavení jsou uložené v mezipaměti v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e63eb-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="blazor-server-side"></a><span data-ttu-id="e63eb-163">Blazor na straně serveru</span><span class="sxs-lookup"><span data-stu-id="e63eb-163">Blazor server-side</span></span>

<span data-ttu-id="e63eb-164">Blazor odděluje komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e63eb-164">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="e63eb-165">Serverové Blazor poskytuje podporu pro hostování součásti Razor na serveru v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e63eb-165">Blazor server-side provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="e63eb-166">Aktualizace uživatelského rozhraní jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.</span><span class="sxs-lookup"><span data-stu-id="e63eb-166">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="e63eb-167">Modul runtime zpracovává odeslání události uživatelského rozhraní z prohlížeče na server a aplikuje aktualizace uživatelského rozhraní odeslané serverem zpět do prohlížeče po spuštění součásti.</span><span class="sxs-lookup"><span data-stu-id="e63eb-167">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="e63eb-168">Připojení používané objektem Blazor serverové ke komunikaci s prohlížeči slouží také ke zpracování spolupráce volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e63eb-168">The connection used by Blazor server-side to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Blazor serveru běží kód .NET na serveru a komunikuje s modelu objektu dokumentu na straně klienta pomocí připojení SignalR](index/_static/blazor-server-side.png)

## <a name="javascript-interop"></a><span data-ttu-id="e63eb-170">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="e63eb-170">JavaScript interop</span></span>

<span data-ttu-id="e63eb-171">U aplikací, které vyžadují knihovny JavaScript třetích stran a přístup k prohlížeči rozhraní API pro součásti spolupracovat s použitím jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e63eb-171">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="e63eb-172">Součásti jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít.</span><span class="sxs-lookup"><span data-stu-id="e63eb-172">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="e63eb-173">C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu.</span><span class="sxs-lookup"><span data-stu-id="e63eb-173">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="e63eb-174">Další informace naleznete v tématu <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="e63eb-174">For more information, see <xref:blazor/javascript-interop>.</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="e63eb-175">Sdílení kódu a .NET Standard</span><span class="sxs-lookup"><span data-stu-id="e63eb-175">Code sharing and .NET Standard</span></span>

<span data-ttu-id="e63eb-176">Implementuje Blazor [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="e63eb-176">Blazor implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="e63eb-177">.NET standard je formální specifikaci rozhraní API pro .NET, které jsou společné pro implementace .NET.</span><span class="sxs-lookup"><span data-stu-id="e63eb-177">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="e63eb-178">Knihovny tříd .NET standard můžete sdílet na různých platformách .NET, jako je například Blazor, rozhraní .NET Framework, .NET Core, Xamarin, Mono a Unity.</span><span class="sxs-lookup"><span data-stu-id="e63eb-178">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="e63eb-179">Vyvolat rozhraní API, které nejsou použitelné v rámci webového prohlížeče (například přístup k systému souborů, otevřeme soket a práce s vlákny) <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="e63eb-179">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e63eb-180">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e63eb-180">Additional resources</span></span>

* [<span data-ttu-id="e63eb-181">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e63eb-181">WebAssembly</span></span>](http://webassembly.org/)
* <xref:blazor/hosting-models>
* [<span data-ttu-id="e63eb-182">Průvodce jazykem C#</span><span class="sxs-lookup"><span data-stu-id="e63eb-182">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="e63eb-183">HTML</span><span class="sxs-lookup"><span data-stu-id="e63eb-183">HTML</span></span>](https://www.w3.org/html/)
