---
title: Vytvoření první aplikace v Blazor
author: guardrex
description: Sestavte aplikaci pro Blazor krok za krokem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: cc7caa1ee01e0282024895ab35c5b9933b1504d0
ms.sourcegitcommit: eb2fe5ad2e82fab86ca952463af8d017ba659b25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2019
ms.locfileid: "73416167"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="671d3-103">Vytvoření první aplikace v Blazor</span><span class="sxs-lookup"><span data-stu-id="671d3-103">Build your first Blazor app</span></span>

<span data-ttu-id="671d3-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="671d3-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="671d3-105">V tomto kurzu se dozvíte, jak vytvořit a upravit aplikaci v Blazor.</span><span class="sxs-lookup"><span data-stu-id="671d3-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="671d3-106">Podle pokynů v článku <xref:blazor/get-started> vytvořte projekt Blazor pro tento kurz.</span><span class="sxs-lookup"><span data-stu-id="671d3-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="671d3-107">Pojmenujte projekt *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="671d3-107">Name the project *ToDoList*.</span></span>

## <a name="build-components"></a><span data-ttu-id="671d3-108">Komponenty sestavení</span><span class="sxs-lookup"><span data-stu-id="671d3-108">Build components</span></span>

1. <span data-ttu-id="671d3-109">Ve složce *Pages (stránky* ) přejděte na jednotlivé tři stránky aplikace: domů, čítač a načíst data.</span><span class="sxs-lookup"><span data-stu-id="671d3-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="671d3-110">Tyto stránky jsou implementovány pomocí indexu souborů komponent Razor *. Razor*, *Counter. Razor*a *FetchData. Razor*.</span><span class="sxs-lookup"><span data-stu-id="671d3-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="671d3-111">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="671d3-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="671d3-112">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="671d3-112">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="671d3-113">Pomocí Blazor můžete místo toho napsat C# .</span><span class="sxs-lookup"><span data-stu-id="671d3-113">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="671d3-114">Projděte si implementaci součásti `Counter` v souboru *Counter. Razor* .</span><span class="sxs-lookup"><span data-stu-id="671d3-114">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="671d3-115">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="671d3-115">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="671d3-116">Uživatelské rozhraní komponenty `Counter` je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="671d3-116">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="671d3-117">Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="671d3-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="671d3-118">Logika značek HTML a C# vykreslování jsou v době sestavení převedena na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="671d3-118">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="671d3-119">Název generované třídy .NET se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="671d3-119">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="671d3-120">Členy třídy komponenty jsou definovány v bloku `@code`.</span><span class="sxs-lookup"><span data-stu-id="671d3-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="671d3-121">V bloku `@code` jsou pro zpracování událostí nebo pro definování jiné logiky komponent určeny stav součásti (vlastnosti, pole) a metody.</span><span class="sxs-lookup"><span data-stu-id="671d3-121">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="671d3-122">Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="671d3-122">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="671d3-123">Když je vybrané tlačítko pro **kliknutí na mou adresu** :</span><span class="sxs-lookup"><span data-stu-id="671d3-123">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="671d3-124">Zavolala se registrovaná obslužná rutina `onclick` komponenty `Counter` (metoda `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="671d3-124">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="671d3-125">Komponenta `Counter` znovu vygeneruje svůj strom vykreslování.</span><span class="sxs-lookup"><span data-stu-id="671d3-125">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="671d3-126">Nový strom vykreslování je porovnán s předchozím.</span><span class="sxs-lookup"><span data-stu-id="671d3-126">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="671d3-127">Jsou aplikovány pouze změny model DOM (Document Object Model) (DOM).</span><span class="sxs-lookup"><span data-stu-id="671d3-127">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="671d3-128">Zobrazený počet je aktualizovaný.</span><span class="sxs-lookup"><span data-stu-id="671d3-128">The displayed count is updated.</span></span>

1. <span data-ttu-id="671d3-129">Upravte C# logiku součásti `Counter` tak, aby byl přírůstek počtu vynásoben dvěma místo na jednom.</span><span class="sxs-lookup"><span data-stu-id="671d3-129">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="671d3-130">Pokud chcete zobrazit změny, sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="671d3-130">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="671d3-131">Vyberte tlačítko pro **kliknutí na tlačítko** .</span><span class="sxs-lookup"><span data-stu-id="671d3-131">Select the **Click me** button.</span></span> <span data-ttu-id="671d3-132">Čítače se zvýší o dva.</span><span class="sxs-lookup"><span data-stu-id="671d3-132">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="671d3-133">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="671d3-133">Use components</span></span>

<span data-ttu-id="671d3-134">Zahrnutí komponenty do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="671d3-134">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="671d3-135">Přidejte komponentu `Counter` do komponenty `Index` aplikace přidáním prvku `<Counter />` do součásti `Index` (*index. Razor*).</span><span class="sxs-lookup"><span data-stu-id="671d3-135">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="671d3-136">Pokud pro toto prostředí používáte Blazor WebAssembly, komponenta `Index` používá komponentu `SurveyPrompt`.</span><span class="sxs-lookup"><span data-stu-id="671d3-136">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="671d3-137">Nahraďte prvek `<SurveyPrompt>` prvkem `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="671d3-137">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="671d3-138">Pokud pro toto prostředí používáte aplikaci Blazor Server, přidejte do komponenty `Index` prvek `<Counter />`:</span><span class="sxs-lookup"><span data-stu-id="671d3-138">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="671d3-139">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="671d3-139">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="671d3-140">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="671d3-140">Rebuild and run the app.</span></span> <span data-ttu-id="671d3-141">Komponenta `Index` má vlastní čítač.</span><span class="sxs-lookup"><span data-stu-id="671d3-141">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="671d3-142">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="671d3-142">Component parameters</span></span>

<span data-ttu-id="671d3-143">Komponenty mohou mít také parametry.</span><span class="sxs-lookup"><span data-stu-id="671d3-143">Components can also have parameters.</span></span> <span data-ttu-id="671d3-144">Parametry komponenty jsou definovány pomocí veřejných vlastností třídy Component s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="671d3-144">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="671d3-145">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="671d3-145">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="671d3-146">Aktualizujte kód `@code` C# komponenty:</span><span class="sxs-lookup"><span data-stu-id="671d3-146">Update the component's `@code` C# code:</span></span>

   * <span data-ttu-id="671d3-147">Přidejte vlastnost Public `IncrementAmount` s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="671d3-147">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="671d3-148">Změňte metodu `IncrementCount` tak, aby při zvyšování hodnoty `currentCount` používala `IncrementAmount`.</span><span class="sxs-lookup"><span data-stu-id="671d3-148">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="671d3-149">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="671d3-149">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="671d3-150">Zadejte parametr `IncrementAmount` v prvku `<Counter>` komponenty `Index` s použitím atributu.</span><span class="sxs-lookup"><span data-stu-id="671d3-150">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="671d3-151">Nastavte hodnotu pro zvýšení čítače o deset.</span><span class="sxs-lookup"><span data-stu-id="671d3-151">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="671d3-152">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="671d3-152">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="671d3-153">Znovu načtěte komponentu `Index`.</span><span class="sxs-lookup"><span data-stu-id="671d3-153">Reload the `Index` component.</span></span> <span data-ttu-id="671d3-154">Čítač se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** .</span><span class="sxs-lookup"><span data-stu-id="671d3-154">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="671d3-155">Čítač v komponentě `Counter` se stále zvyšuje o 1.</span><span class="sxs-lookup"><span data-stu-id="671d3-155">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="671d3-156">Směrování na součásti</span><span class="sxs-lookup"><span data-stu-id="671d3-156">Route to components</span></span>

<span data-ttu-id="671d3-157">Direktiva `@page` v horní části souboru *Counter. Razor* určuje, že komponenta `Counter` je koncový bod směrování.</span><span class="sxs-lookup"><span data-stu-id="671d3-157">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="671d3-158">Komponenta `Counter` zpracovává požadavky odeslané do `/counter`.</span><span class="sxs-lookup"><span data-stu-id="671d3-158">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="671d3-159">Bez direktivy `@page` součást nezpracovává směrované požadavky, ale komponentu mohou i nadále používat jiné komponenty.</span><span class="sxs-lookup"><span data-stu-id="671d3-159">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="671d3-160">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="671d3-160">Dependency injection</span></span>

### <a name="blazor-server-experience"></a><span data-ttu-id="671d3-161">Prostředí serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="671d3-161">Blazor Server experience</span></span>

<span data-ttu-id="671d3-162">Pokud pracujete s aplikací serveru Blazor, je služba `WeatherForecastService` v `Startup.ConfigureServices` registrována jako typ [singleton](xref:fundamentals/dependency-injection#service-lifetimes) .</span><span class="sxs-lookup"><span data-stu-id="671d3-162">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="671d3-163">Instance služby je k dispozici v celé aplikaci prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="671d3-163">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="671d3-164">Direktiva `@inject` se používá k vložení instance služby `WeatherForecastService` do komponenty `FetchData`.</span><span class="sxs-lookup"><span data-stu-id="671d3-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="671d3-165">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="671d3-165">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="671d3-166">Komponenta `FetchData` používá vloženou službu jako `ForecastService` k načtení pole objektů `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="671d3-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a><span data-ttu-id="671d3-167">Prostředí WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="671d3-167">Blazor WebAssembly experience</span></span>

<span data-ttu-id="671d3-168">Pokud pracujete s aplikací Blazor WebAssembly, je `HttpClient` vloženo pro získání dat předpovědi počasí ze souboru *počasí. JSON* ve složce *wwwroot/Sample-data* .</span><span class="sxs-lookup"><span data-stu-id="671d3-168">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="671d3-169">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="671d3-169">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="671d3-170">K vykreslení každé instance prognózy jako řádku v tabulce dat o počasí se používá smyčka [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) :</span><span class="sxs-lookup"><span data-stu-id="671d3-170">An [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="671d3-171">Sestavení seznamu TODO</span><span class="sxs-lookup"><span data-stu-id="671d3-171">Build a todo list</span></span>

<span data-ttu-id="671d3-172">Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="671d3-172">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="671d3-173">Do aplikace ve složce *Pages* přidejte prázdný soubor s názvem *todo. Razor* :</span><span class="sxs-lookup"><span data-stu-id="671d3-173">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="671d3-174">Zadejte počáteční označení pro komponentu:</span><span class="sxs-lookup"><span data-stu-id="671d3-174">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="671d3-175">Přidejte komponentu `Todo` do navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="671d3-175">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="671d3-176">Součást `NavMenu` (*Shared/NavMenu. Razor*) se používá v rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="671d3-176">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="671d3-177">Rozložení jsou komponenty, které umožňují vyhnout se duplikaci obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="671d3-177">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="671d3-178">Přidejte `<NavLink>` elementu pro komponentu `Todo` přidáním následujícího označení položky seznamu pod existující položky seznamu v souboru *Shared/NavMenu. Razor* :</span><span class="sxs-lookup"><span data-stu-id="671d3-178">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="671d3-179">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="671d3-179">Rebuild and run the app.</span></span> <span data-ttu-id="671d3-180">Přejděte na stránku Nová TODO a potvrďte, že odkaz na komponentu `Todo` funguje.</span><span class="sxs-lookup"><span data-stu-id="671d3-180">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="671d3-181">Do kořenového adresáře projektu přidejte soubor *TodoItem.cs* , který bude obsahovat třídu, která představuje položku todo.</span><span class="sxs-lookup"><span data-stu-id="671d3-181">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="671d3-182">Pro třídu `TodoItem` C# použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="671d3-182">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="671d3-183">Vraťte se do součásti `Todo` (*Pages/todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="671d3-183">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="671d3-184">Přidejte pole pro položky ToDo v bloku `@code`.</span><span class="sxs-lookup"><span data-stu-id="671d3-184">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="671d3-185">Komponenta `Todo` používá toto pole k údržbě stavu seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="671d3-185">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="671d3-186">Přidejte neuspořádané označení seznamu a smyčku `foreach` pro vykreslení každé položky ToDo jako položky seznamu (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="671d3-186">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="671d3-187">Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu.</span><span class="sxs-lookup"><span data-stu-id="671d3-187">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="671d3-188">Přidejte textové zadání (`<input>`) a tlačítko (`<button>`) pod Neseřazený seznam (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="671d3-188">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="671d3-189">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="671d3-189">Rebuild and run the app.</span></span> <span data-ttu-id="671d3-190">Když je vybráno tlačítko **Přidat TODO** , nic se nestane, protože obslužná rutina události není na tlačítko kabelem.</span><span class="sxs-lookup"><span data-stu-id="671d3-190">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="671d3-191">Přidejte metodu `AddTodo` do komponenty `Todo` a zaregistrujte ji pro výběry tlačítek pomocí atributu `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="671d3-191">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="671d3-192">Pokud je vybráno C# tlačítko, je volána metoda `AddTodo`:</span><span class="sxs-lookup"><span data-stu-id="671d3-192">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="671d3-193">Chcete-li získat název nové položky ToDo, přidejte pole řetězce `newTodo` v horní části bloku `@code` a vytvořte jeho vazby k hodnotě textového zadání pomocí atributu `bind` v elementu `<input>`:</span><span class="sxs-lookup"><span data-stu-id="671d3-193">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="671d3-194">Aktualizujte metodu `AddTodo` a přidejte do seznamu `TodoItem` se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="671d3-194">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="671d3-195">Vymažte hodnotu textového zadání nastavením `newTodo` na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="671d3-195">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="671d3-196">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="671d3-196">Rebuild and run the app.</span></span> <span data-ttu-id="671d3-197">Chcete-li otestovat nový kód, přidejte do seznamu TODO některé položky ToDo.</span><span class="sxs-lookup"><span data-stu-id="671d3-197">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="671d3-198">Text nadpisu pro každou položku TODO lze upravovat a zaškrtávací políčko může uživatelům pomáhat sledovat dokončené položky.</span><span class="sxs-lookup"><span data-stu-id="671d3-198">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="671d3-199">Přidejte vstup zaškrtávacího políčka pro každou položku TODO a navažte její hodnotu na vlastnost `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="671d3-199">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="671d3-200">Změňte `@todo.Title` na prvek `<input>` vázaný na `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="671d3-200">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="671d3-201">Chcete-li ověřit, zda jsou tyto hodnoty vázány, aktualizujte hlavičku `<h1>`, aby se zobrazil počet nedokončených položek TODO (`IsDone` je `false`).</span><span class="sxs-lookup"><span data-stu-id="671d3-201">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="671d3-202">Dokončená součást `Todo` (*Pages/todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="671d3-202">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="671d3-203">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="671d3-203">Rebuild and run the app.</span></span> <span data-ttu-id="671d3-204">Přidejte položky ToDo pro otestování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="671d3-204">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
