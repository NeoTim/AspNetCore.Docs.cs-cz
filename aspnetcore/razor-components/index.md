---
title: Úvod do komponent Razor
author: guardrex
description: Prozkoumejte službu ASP.NET Core Razor komponenty, pomocí rozhraní .NET web C#/Razor a HTML.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/09/2019
uid: razor-components/index
ms.openlocfilehash: e8d75a0647704f1ff05e70a96abbb2e448868165
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56102926"
---
# <a name="introduction-to-razor-components"></a><span data-ttu-id="b493d-103">Úvod do komponent Razor</span><span class="sxs-lookup"><span data-stu-id="b493d-103">Introduction to Razor Components</span></span>

<span data-ttu-id="b493d-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b493d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="b493d-105">*Součásti Razor* je nový způsob, jak vytvářet interaktivní webové uživatelské rozhraní s .NET na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="b493d-105">*Razor Components* is a new way to build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="b493d-106">Vytvářet bohaté interaktivní uživatelských rozhraní pomocí C# místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="b493d-106">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="b493d-107">Sdílet logiku na straně serveru a na straně klienta aplikací napsaných pomocí rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="b493d-107">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="b493d-108">Vykreslení uživatelského rozhraní jako HTML a CSS pro podporu široké prohlížeče, včetně mobilních.</span><span class="sxs-lookup"><span data-stu-id="b493d-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

## <a name="components"></a><span data-ttu-id="b493d-109">Komponenty</span><span class="sxs-lookup"><span data-stu-id="b493d-109">Components</span></span>

<span data-ttu-id="b493d-110">A *Razor komponenty* je část uživatelského rozhraní, jako je například formulář položka stránky, dialogové okno nebo data.</span><span class="sxs-lookup"><span data-stu-id="b493d-110">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="b493d-111">Součásti zpracovávat události uživatele a definovat flexibilní logiku pro vykreslení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b493d-111">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="b493d-112">Součásti můžete vnořit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="b493d-112">Components can be nested and reused.</span></span>

<span data-ttu-id="b493d-113">Komponenty jsou součástí sestavení .NET, které lze sdílet a distribuovat jako balíčky NuGet třídy rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="b493d-113">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="b493d-114">Třídu lze zapsat buď v podobě značek stránky Razor (*.cshtml*) nebo jako C# třídy (*.cs*).</span><span class="sxs-lookup"><span data-stu-id="b493d-114">The class can either be written in the form of a Razor markup page (*.cshtml*) or as a C# class (*.cs*).</span></span>

<span data-ttu-id="b493d-115">[Razor](xref:mvc/views/razor) je syntaxe pro kombinování značka jazyka HTML s C# kódu.</span><span class="sxs-lookup"><span data-stu-id="b493d-115">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="b493d-116">Razor je navržená pro produktivitu vývojářů, umožňuje vývojářům přepínat mezi značky a C# ve stejném souboru s [IntelliSense](/visualstudio/ide/using-intellisense) podporovat.</span><span class="sxs-lookup"><span data-stu-id="b493d-116">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="b493d-117">Stránky Razor a MVC zobrazení také používají syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="b493d-117">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="b493d-118">Na rozdíl od Razor Pages a zobrazení MVC, která jsou postavené na modelu žádost odpověď, používají součásti speciálně pro zpracování sestavení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b493d-118">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="b493d-119">Komponenty Razor můžete použít speciálně pro logika uživatelského rozhraní na straně klienta a skládání.</span><span class="sxs-lookup"><span data-stu-id="b493d-119">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="b493d-120">Následující kód je příklad vlastního dialogu komponenty v souboru Razor (*DialogComponent.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="b493d-120">The following markup is an example of a custom dialog component in a Razor file (*DialogComponent.cshtml*):</span></span>

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

<span data-ttu-id="b493d-121">Při této součásti se používá jinde v aplikaci, technologie IntelliSense urychluje vývoj aplikací pomocí syntaxe a parametrů dokončení.</span><span class="sxs-lookup"><span data-stu-id="b493d-121">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="b493d-122">Komponenty vykreslování do v paměti reprezentace prohlížeč DOM volána *vykreslení stromu* , který potom slouží k aktualizaci uživatelského rozhraní tak flexibilní a efektivní.</span><span class="sxs-lookup"><span data-stu-id="b493d-122">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="b493d-123">Součásti Razor podporují zařízení core vyžaduje většina aplikací, včetně:</span><span class="sxs-lookup"><span data-stu-id="b493d-123">Razor Components support core facilities required by most apps, including:</span></span>

* <span data-ttu-id="b493d-124">Parametry</span><span class="sxs-lookup"><span data-stu-id="b493d-124">Parameters</span></span>
* <span data-ttu-id="b493d-125">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="b493d-125">Event handling</span></span>
* <span data-ttu-id="b493d-126">Vytváření datových vazeb</span><span class="sxs-lookup"><span data-stu-id="b493d-126">Data binding</span></span>
* <span data-ttu-id="b493d-127">Směrování</span><span class="sxs-lookup"><span data-stu-id="b493d-127">Routing</span></span>
* <span data-ttu-id="b493d-128">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="b493d-128">Dependency injection</span></span>
* <span data-ttu-id="b493d-129">Rozložení</span><span class="sxs-lookup"><span data-stu-id="b493d-129">Layouts</span></span>
* <span data-ttu-id="b493d-130">Šablon</span><span class="sxs-lookup"><span data-stu-id="b493d-130">Templating</span></span>
* <span data-ttu-id="b493d-131">Kaskádové hodnoty</span><span class="sxs-lookup"><span data-stu-id="b493d-131">Cascading values</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="b493d-132">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="b493d-132">JavaScript interop</span></span>

<span data-ttu-id="b493d-133">Pro aplikace, které vyžadují knihovny jazyka JavaScript a prohlížeč rozhraní API třetích stran nastavení spolupráci komponenty Razor s použitím jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b493d-133">For apps that require third-party JavaScript libraries and browser APIs, Razor Components interoperate with JavaScript.</span></span> <span data-ttu-id="b493d-134">Razor komponenty jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít.</span><span class="sxs-lookup"><span data-stu-id="b493d-134">Razor Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="b493d-135">C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu.</span><span class="sxs-lookup"><span data-stu-id="b493d-135">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="b493d-136">Další informace najdete v tématu [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="b493d-136">For more information, see [JavaScript interop](xref:razor-components/javascript-interop).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="b493d-137">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="b493d-137">Hosting models</span></span>

### <a name="server-side-hosting-model"></a><span data-ttu-id="b493d-138">Model hostingu na straně serveru</span><span class="sxs-lookup"><span data-stu-id="b493d-138">Server-side hosting model</span></span>

<span data-ttu-id="b493d-139">Protože komponenty Razor oddělit komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní, existuje určitá flexibilita v jak se dají hostovat součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="b493d-139">Because Razor Components decouple a component's rendering logic from how UI updates are applied, there's flexibility in how Razor Components can be hosted.</span></span> <span data-ttu-id="b493d-140">ASP.NET Core Razor součásti v .NET Core 3.0 přidává podporu pro hostování součásti Razor na server v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b493d-140">ASP.NET Core Razor Components in .NET Core 3.0 adds support for hosting Razor Components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="b493d-141">Aktualizace uživatelského rozhraní jsou zpracovány prostřednictvím připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="b493d-141">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="b493d-142">Modul runtime:</span><span class="sxs-lookup"><span data-stu-id="b493d-142">The runtime:</span></span>

* <span data-ttu-id="b493d-143">Obslužné rutiny odeslání události uživatelského rozhraní z prohlížeče na server.</span><span class="sxs-lookup"><span data-stu-id="b493d-143">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="b493d-144">Platí uživatelského rozhraní aktualizací odeslané serverem zpět do prohlížeče po spuštění součásti.</span><span class="sxs-lookup"><span data-stu-id="b493d-144">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="b493d-145">Připojení používané komponenty Razor ke komunikaci s prohlížeči slouží také ke zpracování spolupráce volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="b493d-145">The connection used by Razor Components to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Razor komponenty běží kód .NET na serveru a komunikuje s modelu objektu dokumentu na straně klienta pomocí připojení SignalR](index/_static/aspnet-core-razor-components.png)

<span data-ttu-id="b493d-147">Další informace naleznete v tématu <xref:razor-components/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="b493d-147">For more information, see <xref:razor-components/hosting-models#server-side-hosting-model>.</span></span>

### <a name="client-side-hosting-model"></a><span data-ttu-id="b493d-148">Model hostingu na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b493d-148">Client-side hosting model</span></span>

<span data-ttu-id="b493d-149">*Blazor* je experimentální model hostingu na straně klienta součástí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="b493d-149">*Blazor* is the experimental client-side hosting model of Razor Components.</span></span> <span data-ttu-id="b493d-150">Blazor běží na rozhraní .NET v prohlížeči pomocí otevřených webových standardů bez kódu nebo moduly plug-in transpilation.</span><span class="sxs-lookup"><span data-stu-id="b493d-150">Blazor runs on .NET in the browser using open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="b493d-151">Blazor funguje v všechny moderní webové prohlížeče, včetně mobilních.</span><span class="sxs-lookup"><span data-stu-id="b493d-151">Blazor works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="b493d-152">Spouštění kódu .NET uvnitř webových prohlížečů je možné podle [WebAssembly](http://webassembly.org) (zkrácené *wasm*).</span><span class="sxs-lookup"><span data-stu-id="b493d-152">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="b493d-153">WebAssembly je otevřít web, standard a podporovaných webových prohlížečů bez modulů plug-in.</span><span class="sxs-lookup"><span data-stu-id="b493d-153">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="b493d-154">WebAssembly je formát compact bajtového kódu optimalizovaný pro rychlé stažení a spuštění maximální rychlost.</span><span class="sxs-lookup"><span data-stu-id="b493d-154">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="b493d-155">WebAssembly kód může přistupovat k úplné funkce aplikace v prohlížeči pomocí zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b493d-155">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="b493d-156">Ve stejnou dobu spuštění kódu WebAssembly v stejné důvěryhodné izolovaného prostoru jako jazyka JavaScript, aby se zabránilo škodlivé akce v klientském počítači.</span><span class="sxs-lookup"><span data-stu-id="b493d-156">At the same time, WebAssembly code runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![Blazor běží v prohlížeči s WebAssembly kód .NET.](index/_static/blazor.png)

<span data-ttu-id="b493d-158">Při Blazor aplikace je vytvořená a spustit v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="b493d-158">When a Blazor app is built and run in a browser:</span></span>

* <span data-ttu-id="b493d-159">C#soubory kódu a Razor soubory jsou zkompilovány do sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="b493d-159">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="b493d-160">Sestavení a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b493d-160">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="b493d-161">Blazor bootstrap modul .NET runtime pomocí jazyka JavaScript a konfiguruje modul runtime k načtení sestavení.</span><span class="sxs-lookup"><span data-stu-id="b493d-161">Blazor uses JavaScript to bootstrap the .NET runtime and configures the runtime to load assemblies.</span></span> <span data-ttu-id="b493d-162">Volání dokumentu Object Model (DOM) manipulaci a prohlížeč rozhraní API jsou zpracovány tímto modulem Blazor prostřednictvím zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b493d-162">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor runtime via JavaScript interop.</span></span>

<span data-ttu-id="b493d-163">Pro podporu starších prohlížečích, které nepodporují WebAssembly, použijte [model hostingu na straně serveru](#server-side-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="b493d-163">To support older browsers that don't support WebAssembly, use the [server-side hosting model](#server-side-hosting-model).</span></span>

<span data-ttu-id="b493d-164">Další informace naleznete v tématu <xref:razor-components/hosting-models#client-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="b493d-164">For more information, see <xref:razor-components/hosting-models#client-side-hosting-model>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b493d-165">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b493d-165">Additional resources</span></span>

* [<span data-ttu-id="b493d-166">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b493d-166">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="b493d-167">Průvodce jazykem C#</span><span class="sxs-lookup"><span data-stu-id="b493d-167">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="b493d-168">HTML</span><span class="sxs-lookup"><span data-stu-id="b493d-168">HTML</span></span>](https://www.w3.org/html/)
