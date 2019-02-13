---
title: Úvod do komponent Razor
author: guardrex
description: Prozkoumejte službu ASP.NET Core Razor komponenty, způsob, jak vytvářet interaktivní webové na straně klienta uživatelské rozhraní s využitím .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: razor-components/index
ms.openlocfilehash: 04a73d33cee0deedaf3dc97395836a936b580fbd
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159523"
---
# <a name="introduction-to-razor-components"></a><span data-ttu-id="d7752-103">Úvod do komponent Razor</span><span class="sxs-lookup"><span data-stu-id="d7752-103">Introduction to Razor Components</span></span>

<span data-ttu-id="d7752-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d7752-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d7752-105">*Součásti Razor* je způsob, jak vytvářet interaktivní webové uživatelské rozhraní s .NET na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="d7752-105">*Razor Components* is a way to build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="d7752-106">Vytvářet bohaté interaktivní uživatelských rozhraní pomocí C# místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="d7752-106">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="d7752-107">Sdílet logiku na straně serveru a na straně klienta aplikací napsaných pomocí rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="d7752-107">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="d7752-108">Vykreslení uživatelského rozhraní jako HTML a CSS pro podporu široké prohlížeče, včetně mobilních.</span><span class="sxs-lookup"><span data-stu-id="d7752-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="d7752-109">Součásti Razor podporuje zařízení core vyžaduje většina aplikací, včetně:</span><span class="sxs-lookup"><span data-stu-id="d7752-109">Razor Components supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="d7752-110">Parametry</span><span class="sxs-lookup"><span data-stu-id="d7752-110">Parameters</span></span>
* <span data-ttu-id="d7752-111">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="d7752-111">Event handling</span></span>
* <span data-ttu-id="d7752-112">Vytváření datových vazeb</span><span class="sxs-lookup"><span data-stu-id="d7752-112">Data binding</span></span>
* <span data-ttu-id="d7752-113">Směrování</span><span class="sxs-lookup"><span data-stu-id="d7752-113">Routing</span></span>
* <span data-ttu-id="d7752-114">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="d7752-114">Dependency injection</span></span>
* <span data-ttu-id="d7752-115">Rozložení</span><span class="sxs-lookup"><span data-stu-id="d7752-115">Layouts</span></span>
* <span data-ttu-id="d7752-116">Šablon</span><span class="sxs-lookup"><span data-stu-id="d7752-116">Templating</span></span>
* <span data-ttu-id="d7752-117">Kaskádové hodnoty</span><span class="sxs-lookup"><span data-stu-id="d7752-117">Cascading values</span></span>

<span data-ttu-id="d7752-118">Součásti Razor odděluje komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d7752-118">Razor Components decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="d7752-119">ASP.NET Core Razor součásti v .NET Core 3.0 přidává podporu pro hostování součásti Razor na server v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7752-119">ASP.NET Core Razor Components in .NET Core 3.0 adds support for hosting Razor Components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="d7752-120">Aktualizace uživatelského rozhraní jsou zpracovány prostřednictvím připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="d7752-120">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="d7752-121">Modul runtime:</span><span class="sxs-lookup"><span data-stu-id="d7752-121">The runtime:</span></span>

* <span data-ttu-id="d7752-122">Obslužné rutiny odeslání události uživatelského rozhraní z prohlížeče na server.</span><span class="sxs-lookup"><span data-stu-id="d7752-122">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="d7752-123">Platí uživatelského rozhraní aktualizací odeslané serverem zpět do prohlížeče po spuštění součásti.</span><span class="sxs-lookup"><span data-stu-id="d7752-123">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="d7752-124">Připojení používané komponenty Razor ke komunikaci s prohlížeči slouží také ke zpracování spolupráce volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="d7752-124">The connection used by Razor Components to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Razor komponenty běží kód .NET na serveru a komunikuje s modelu objektu dokumentu na straně klienta pomocí připojení SignalR](index/_static/aspnet-core-razor-components.png)

<span data-ttu-id="d7752-126">Další informace naleznete v tématu <xref:razor-components/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="d7752-126">For more information, see <xref:razor-components/hosting-models#server-side-hosting-model>.</span></span>

<span data-ttu-id="d7752-127">*Blazor* je experimentální model hostingu na straně klienta součástí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="d7752-127">*Blazor* is the experimental client-side hosting model of Razor Components.</span></span> <span data-ttu-id="d7752-128">Blazor běží na rozhraní .NET v prohlížeči pomocí otevřených webových standardů bez kódu nebo moduly plug-in transpilation.</span><span class="sxs-lookup"><span data-stu-id="d7752-128">Blazor runs on .NET in the browser using open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="d7752-129">Další informace naleznete v tématu <xref:razor-components/hosting-models#client-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="d7752-129">For more information, see <xref:razor-components/hosting-models#client-side-hosting-model>.</span></span>

## <a name="components"></a><span data-ttu-id="d7752-130">Komponenty</span><span class="sxs-lookup"><span data-stu-id="d7752-130">Components</span></span>

<span data-ttu-id="d7752-131">A *Razor komponenty* je část uživatelského rozhraní, jako je například formulář položka stránky, dialogové okno nebo data.</span><span class="sxs-lookup"><span data-stu-id="d7752-131">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="d7752-132">Součásti zpracovávat události uživatele a definovat flexibilní logiku pro vykreslení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d7752-132">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="d7752-133">Součásti můžete vnořit a znovu použít.</span><span class="sxs-lookup"><span data-stu-id="d7752-133">Components can be nested and reused.</span></span>

<span data-ttu-id="d7752-134">Komponenty jsou součástí sestavení .NET, které lze sdílet a distribuovat jako balíčky NuGet třídy rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="d7752-134">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="d7752-135">Třídu lze zapsat buď v podobě značek stránky Razor (*.cshtml*) nebo jako C# třídy (*.cs*).</span><span class="sxs-lookup"><span data-stu-id="d7752-135">The class can either be written in the form of a Razor markup page (*.cshtml*) or as a C# class (*.cs*).</span></span>

<span data-ttu-id="d7752-136">[Razor](xref:mvc/views/razor) je syntaxe pro kombinování značka jazyka HTML s C# kódu.</span><span class="sxs-lookup"><span data-stu-id="d7752-136">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="d7752-137">Razor je navržená pro produktivitu vývojářů, umožňuje vývojářům přepínat mezi značky a C# ve stejném souboru s [IntelliSense](/visualstudio/ide/using-intellisense) podporovat.</span><span class="sxs-lookup"><span data-stu-id="d7752-137">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="d7752-138">Stránky Razor a MVC zobrazení také používají syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="d7752-138">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="d7752-139">Na rozdíl od Razor Pages a zobrazení MVC, která jsou postavené na modelu žádost odpověď, používají součásti speciálně pro zpracování sestavení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d7752-139">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="d7752-140">Komponenty Razor můžete použít speciálně pro logika uživatelského rozhraní na straně klienta a skládání.</span><span class="sxs-lookup"><span data-stu-id="d7752-140">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="d7752-141">Následující kód je příklad vlastního dialogu komponenty v souboru Razor (*DialogComponent.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="d7752-141">The following markup is an example of a custom dialog component in a Razor file (*DialogComponent.cshtml*):</span></span>

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

<span data-ttu-id="d7752-142">Při této součásti se používá jinde v aplikaci, technologie IntelliSense urychluje vývoj aplikací pomocí syntaxe a parametrů dokončení.</span><span class="sxs-lookup"><span data-stu-id="d7752-142">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="d7752-143">Komponenty vykreslování do v paměti reprezentace prohlížeč DOM volána *vykreslení stromu* , který potom slouží k aktualizaci uživatelského rozhraní tak flexibilní a efektivní.</span><span class="sxs-lookup"><span data-stu-id="d7752-143">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="d7752-144">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="d7752-144">JavaScript interop</span></span>

<span data-ttu-id="d7752-145">U aplikací, které vyžadují knihovny třetích stran jazyka JavaScript a prohlížeč rozhraní API pro součásti spolupracovat s použitím jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d7752-145">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="d7752-146">Součásti jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít.</span><span class="sxs-lookup"><span data-stu-id="d7752-146">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="d7752-147">C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu.</span><span class="sxs-lookup"><span data-stu-id="d7752-147">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="d7752-148">Další informace najdete v tématu [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="d7752-148">For more information, see [JavaScript interop](xref:razor-components/javascript-interop).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d7752-149">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d7752-149">Additional resources</span></span>

* <xref:spa/blazor/index>
* [<span data-ttu-id="d7752-150">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d7752-150">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="d7752-151">Průvodce jazykem C#</span><span class="sxs-lookup"><span data-stu-id="d7752-151">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="d7752-152">HTML</span><span class="sxs-lookup"><span data-stu-id="d7752-152">HTML</span></span>](https://www.w3.org/html/)
