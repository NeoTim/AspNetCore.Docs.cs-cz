---
title: Sestavení první Blazor aplikace
author: guardrex
description: Sestavte Blazor aplikaci krok za krokem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 0b9854b3848a204b28d0427bef08364be0139069
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102847"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="92eac-103">Sestavení první Blazor aplikace</span><span class="sxs-lookup"><span data-stu-id="92eac-103">Build your first Blazor app</span></span>

<span data-ttu-id="92eac-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="92eac-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="92eac-105">V tomto kurzu se dozvíte, jak vytvořit a upravit Blazor aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92eac-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="92eac-106">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="92eac-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="92eac-107">Vytvoření Blazor projektu aplikace seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="92eac-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="92eac-108">Upravit Razor součásti</span><span class="sxs-lookup"><span data-stu-id="92eac-108">Modify Razor components</span></span>
> * <span data-ttu-id="92eac-109">Použití zpracování událostí a datové vazby v součástech</span><span class="sxs-lookup"><span data-stu-id="92eac-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="92eac-110">Použití vkládání závislostí (DI) a směrování v Blazor aplikaci</span><span class="sxs-lookup"><span data-stu-id="92eac-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="92eac-111">Na konci tohoto kurzu budete mít funkční aplikaci seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="92eac-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="92eac-112">Komponenty sestavení</span><span class="sxs-lookup"><span data-stu-id="92eac-112">Build components</span></span>

1. <span data-ttu-id="92eac-113">Pokud <xref:blazor/get-started> chcete vytvořit Blazor projekt pro tento kurz, postupujte podle pokynů v článku.</span><span class="sxs-lookup"><span data-stu-id="92eac-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="92eac-114">Pojmenujte projekt *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="92eac-114">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="92eac-115">Ve složce *Pages (stránky* ) přejděte na jednotlivé tři stránky aplikace: domů, čítač a načíst data.</span><span class="sxs-lookup"><span data-stu-id="92eac-115">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="92eac-116">Tyto stránky jsou implementovány pomocí Razor indexu souborů komponenty *index. Razor*, *Counter. Razor*a *FetchData. Razor*.</span><span class="sxs-lookup"><span data-stu-id="92eac-116">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="92eac-117">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="92eac-117">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="92eac-118">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="92eac-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="92eac-119">Pomocí Blazor můžete místo toho napsat C#.</span><span class="sxs-lookup"><span data-stu-id="92eac-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="92eac-120">Projděte si implementaci `Counter` komponenty v souboru *Counter. Razor* .</span><span class="sxs-lookup"><span data-stu-id="92eac-120">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="92eac-121">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="92eac-121">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="92eac-122">Uživatelské rozhraní `Counter` komponenty je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="92eac-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="92eac-123">Dynamická logika vykreslování (například cykly, podmíněné výrazy, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="92eac-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="92eac-124">Značky HTML značek a vykreslování v jazyce C# jsou převedeny na třídu komponenty v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="92eac-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="92eac-125">Název generované třídy .NET se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="92eac-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="92eac-126">Členy třídy komponenty jsou definovány v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="92eac-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="92eac-127">V `@code` bloku jsou pro zpracování událostí nebo pro definování jiné logiky komponent určeny stav součásti (vlastnosti, pole) a metody.</span><span class="sxs-lookup"><span data-stu-id="92eac-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="92eac-128">Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="92eac-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="92eac-129">Když je vybrané tlačítko pro **kliknutí na mou adresu** :</span><span class="sxs-lookup"><span data-stu-id="92eac-129">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="92eac-130">`Counter`Registrovaná `onclick` obslužná rutina komponenty je volána ( `IncrementCount` Metoda).</span><span class="sxs-lookup"><span data-stu-id="92eac-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="92eac-131">`Counter`Komponenta znovu vygeneruje svůj strom vykreslování.</span><span class="sxs-lookup"><span data-stu-id="92eac-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="92eac-132">Nový strom vykreslování je porovnán s předchozím.</span><span class="sxs-lookup"><span data-stu-id="92eac-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="92eac-133">Jsou aplikovány pouze změny model DOM (Document Object Model) (DOM).</span><span class="sxs-lookup"><span data-stu-id="92eac-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="92eac-134">Zobrazený počet je aktualizovaný.</span><span class="sxs-lookup"><span data-stu-id="92eac-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="92eac-135">Upravte logiku komponenty jazyka C# `Counter` tak, aby byl přírůstek počtu vynásoben dvěma místomi jednoho.</span><span class="sxs-lookup"><span data-stu-id="92eac-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="92eac-136">Pokud chcete zobrazit změny, sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92eac-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="92eac-137">Vyberte tlačítko pro **kliknutí na tlačítko** .</span><span class="sxs-lookup"><span data-stu-id="92eac-137">Select the **Click me** button.</span></span> <span data-ttu-id="92eac-138">Čítače se zvýší o dva.</span><span class="sxs-lookup"><span data-stu-id="92eac-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="92eac-139">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="92eac-139">Use components</span></span>

<span data-ttu-id="92eac-140">Zahrnutí komponenty do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="92eac-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="92eac-141">Přidejte `Counter` komponentu do `Index` komponenty aplikace přidáním `<Counter />` prvku do `Index` komponenty (*index. Razor*).</span><span class="sxs-lookup"><span data-stu-id="92eac-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="92eac-142">Pokud Blazor pro toto prostředí používáte WebAssembly, `SurveyPrompt` Komponenta je používána komponentou `Index` .</span><span class="sxs-lookup"><span data-stu-id="92eac-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="92eac-143">Nahraďte `<SurveyPrompt>` element elementem `<Counter />` .</span><span class="sxs-lookup"><span data-stu-id="92eac-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="92eac-144">Pokud Blazor pro toto prostředí používáte serverovou aplikaci, přidejte `<Counter />` element do `Index` komponenty:</span><span class="sxs-lookup"><span data-stu-id="92eac-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="92eac-145">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="92eac-145">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="92eac-146">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92eac-146">Rebuild and run the app.</span></span> <span data-ttu-id="92eac-147">`Index`Komponenta má vlastní čítač.</span><span class="sxs-lookup"><span data-stu-id="92eac-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="92eac-148">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="92eac-148">Component parameters</span></span>

<span data-ttu-id="92eac-149">Komponenty mohou mít také parametry.</span><span class="sxs-lookup"><span data-stu-id="92eac-149">Components can also have parameters.</span></span> <span data-ttu-id="92eac-150">Parametry komponenty jsou definovány pomocí veřejných vlastností třídy Component s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="92eac-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="92eac-151">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="92eac-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="92eac-152">Aktualizujte `@code` kód C# komponenty následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="92eac-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="92eac-153">Přidejte veřejnou `IncrementAmount` vlastnost s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="92eac-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="92eac-154">Změňte `IncrementCount` metodu na použití `IncrementAmount` vlastnosti při zvyšování hodnoty `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="92eac-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="92eac-155">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="92eac-155">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="92eac-156">Zadejte `IncrementAmount` parametr v `Index` `<Counter>` elementu komponenty pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="92eac-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="92eac-157">Nastavte hodnotu pro zvýšení čítače o deset.</span><span class="sxs-lookup"><span data-stu-id="92eac-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="92eac-158">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="92eac-158">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="92eac-159">Znovu načtěte `Index` komponentu.</span><span class="sxs-lookup"><span data-stu-id="92eac-159">Reload the `Index` component.</span></span> <span data-ttu-id="92eac-160">Čítač se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** .</span><span class="sxs-lookup"><span data-stu-id="92eac-160">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="92eac-161">Čítač v součásti se `Counter` stále zvyšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="92eac-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="92eac-162">Směrování na součásti</span><span class="sxs-lookup"><span data-stu-id="92eac-162">Route to components</span></span>

<span data-ttu-id="92eac-163">`@page`Direktiva v horní části souboru *Counter. Razor* určuje, že `Counter` Komponenta je koncový bod směrování.</span><span class="sxs-lookup"><span data-stu-id="92eac-163">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="92eac-164">`Counter`Komponenta zpracovává požadavky odeslané na `/counter` .</span><span class="sxs-lookup"><span data-stu-id="92eac-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="92eac-165">Bez `@page` direktivy nezpracovávají součásti směrované požadavky, ale komponentu mohou i nadále používat jiné komponenty.</span><span class="sxs-lookup"><span data-stu-id="92eac-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="92eac-166">Injektáž závislosti</span><span class="sxs-lookup"><span data-stu-id="92eac-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="92eac-167">Prostředí serveru</span><span class="sxs-lookup"><span data-stu-id="92eac-167"> Server experience</span></span>

<span data-ttu-id="92eac-168">Pokud pracujete se Blazor serverovou aplikací, `WeatherForecastService` služba je v nástroji registrována jako typ [singleton](xref:fundamentals/dependency-injection#service-lifetimes) `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="92eac-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="92eac-169">Instance služby je k dispozici v celé aplikaci prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="92eac-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="92eac-170">[`@inject`](xref:mvc/views/razor#inject)Direktiva slouží k vložení instance `WeatherForecastService` služby do `FetchData` komponenty.</span><span class="sxs-lookup"><span data-stu-id="92eac-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="92eac-171">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="92eac-171">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="92eac-172">`FetchData`Komponenta používá vloženou službu jako `ForecastService` k načtení pole `WeatherForecast` objektů:</span><span class="sxs-lookup"><span data-stu-id="92eac-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="92eac-173">Prostředí WebAssembly</span><span class="sxs-lookup"><span data-stu-id="92eac-173"> WebAssembly experience</span></span>

<span data-ttu-id="92eac-174">Při práci s Blazor aplikací WebAssembly <xref:System.Net.Http.HttpClient> je vloženo pro získání dat předpovědi počasí z *weather.jsv* souboru ve složce *wwwroot/Sample-data* .</span><span class="sxs-lookup"><span data-stu-id="92eac-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="92eac-175">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="92eac-175">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="92eac-176">[`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)Smyčka se používá k vykreslení každé instance prognózy jako řádku v tabulce dat o počasí:</span><span class="sxs-lookup"><span data-stu-id="92eac-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="92eac-177">Sestavení seznamu TODO</span><span class="sxs-lookup"><span data-stu-id="92eac-177">Build a todo list</span></span>

<span data-ttu-id="92eac-178">Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="92eac-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="92eac-179">Přidejte `Todo` Razor do aplikace novou komponentu ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="92eac-179">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="92eac-180">Pokud používáte aplikaci Visual Studio, klikněte pravým tlačítkem myši na složku **stránky** a vyberte možnost **Přidat**  >  **novou položku**  >  \*\* Razor Komponenta\*\*.</span><span class="sxs-lookup"><span data-stu-id="92eac-180">If you're using Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="92eac-181">Pojmenujte soubor komponenty *todo. Razor*.</span><span class="sxs-lookup"><span data-stu-id="92eac-181">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="92eac-182">V jiných vývojových prostředích přidejte prázdný soubor do složky **Pages** s názvem *todo. Razor*.</span><span class="sxs-lookup"><span data-stu-id="92eac-182">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="92eac-183">Zadejte počáteční označení pro komponentu:</span><span class="sxs-lookup"><span data-stu-id="92eac-183">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="92eac-184">Přidejte `Todo` komponentu do navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="92eac-184">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="92eac-185">`NavMenu`Komponenta (*Shared/NavMenu. Razor*) se používá v rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="92eac-185">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="92eac-186">Rozložení jsou komponenty, které umožňují vyhnout se duplikaci obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92eac-186">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="92eac-187">Přidejte `<NavLink>` element pro komponentu přidáním `Todo` následujícího označení položky seznamu pod existující položky seznamu v souboru *Shared/NavMenu. Razor* :</span><span class="sxs-lookup"><span data-stu-id="92eac-187">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="92eac-188">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92eac-188">Rebuild and run the app.</span></span> <span data-ttu-id="92eac-189">Přejděte na stránku Nová TODO a potvrďte, že odkaz na `Todo` komponentu funguje.</span><span class="sxs-lookup"><span data-stu-id="92eac-189">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="92eac-190">Do kořenového adresáře projektu přidejte soubor *TodoItem.cs* , který bude obsahovat třídu, která představuje položku todo.</span><span class="sxs-lookup"><span data-stu-id="92eac-190">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="92eac-191">Pro třídu použijte následující kód jazyka C# `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="92eac-191">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="92eac-192">Vraťte se do `Todo` komponenty (*Pages/todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="92eac-192">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="92eac-193">Přidejte pole pro položky ToDo v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="92eac-193">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="92eac-194">`Todo`Komponenta používá toto pole k údržbě stavu seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="92eac-194">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="92eac-195">Přidejte neuspořádaný seznam značek a `foreach` smyčku pro vykreslení každé položky ToDo jako položky seznamu ( `<li>` ).</span><span class="sxs-lookup"><span data-stu-id="92eac-195">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="92eac-196">Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu.</span><span class="sxs-lookup"><span data-stu-id="92eac-196">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="92eac-197">Přidejte textové zadání ( `<input>` ) a tlačítko ( `<button>` ) pod Neseřazený seznam ( `<ul>...</ul>` ):</span><span class="sxs-lookup"><span data-stu-id="92eac-197">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="92eac-198">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92eac-198">Rebuild and run the app.</span></span> <span data-ttu-id="92eac-199">Když je vybráno tlačítko **Přidat TODO** , nic se nestane, protože obslužná rutina události není na tlačítko kabelem.</span><span class="sxs-lookup"><span data-stu-id="92eac-199">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="92eac-200">Přidejte `AddTodo` do `Todo` komponenty metodu a zaregistrujte ji pro výběry tlačítek pomocí `@onclick` atributu.</span><span class="sxs-lookup"><span data-stu-id="92eac-200">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="92eac-201">`AddTodo`Metoda jazyka C# je volána, když je vybráno tlačítko:</span><span class="sxs-lookup"><span data-stu-id="92eac-201">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="92eac-202">Chcete-li získat název nové položky ToDo, přidejte `newTodo` pole řetězce v horní části `@code` bloku a vytvořte jeho svázání s hodnotou textového zadání pomocí `bind` atributu v `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="92eac-202">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="92eac-203">Aktualizujte `AddTodo` metodu tak, aby se do `TodoItem` seznamu přidal název se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="92eac-203">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="92eac-204">Vymažte hodnotu textového zadání nastavením `newTodo` na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="92eac-204">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="92eac-205">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92eac-205">Rebuild and run the app.</span></span> <span data-ttu-id="92eac-206">Chcete-li otestovat nový kód, přidejte do seznamu TODO některé položky ToDo.</span><span class="sxs-lookup"><span data-stu-id="92eac-206">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="92eac-207">Text nadpisu pro každou položku TODO lze upravovat a zaškrtávací políčko může uživatelům pomáhat sledovat dokončené položky.</span><span class="sxs-lookup"><span data-stu-id="92eac-207">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="92eac-208">Přidejte vstup zaškrtávacího políčka pro každou položku TODO a navažte její hodnotu na `IsDone` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="92eac-208">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="92eac-209">Změnit `@todo.Title` na `<input>` prvek vázaný na `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="92eac-209">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="92eac-210">Chcete-li ověřit, zda jsou tyto hodnoty vázány, aktualizujte `<h3>` záhlaví tak, aby zobrazovalo počet nedokončených položek TODO ( `IsDone` je `false` ).</span><span class="sxs-lookup"><span data-stu-id="92eac-210">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="92eac-211">Dokončená `Todo` součást (*Pages/todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="92eac-211">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="92eac-212">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92eac-212">Rebuild and run the app.</span></span> <span data-ttu-id="92eac-213">Přidejte položky ToDo pro otestování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="92eac-213">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="92eac-214">Další kroky</span><span class="sxs-lookup"><span data-stu-id="92eac-214">Next steps</span></span>

<span data-ttu-id="92eac-215">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="92eac-215">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="92eac-216">Vytvoření Blazor projektu aplikace seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="92eac-216">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="92eac-217">Upravit Razor součásti</span><span class="sxs-lookup"><span data-stu-id="92eac-217">Modify Razor components</span></span>
> * <span data-ttu-id="92eac-218">Použití zpracování událostí a datové vazby v součástech</span><span class="sxs-lookup"><span data-stu-id="92eac-218">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="92eac-219">Použití vkládání závislostí (DI) a směrování v Blazor aplikaci</span><span class="sxs-lookup"><span data-stu-id="92eac-219">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="92eac-220">Naučte se vytvářet a používat komponenty:</span><span class="sxs-lookup"><span data-stu-id="92eac-220">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
