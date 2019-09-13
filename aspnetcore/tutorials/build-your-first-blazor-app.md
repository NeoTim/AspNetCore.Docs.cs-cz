---
title: Vytvoření první aplikace v Blazor
author: guardrex
description: Sestavte aplikaci pro Blazor krok za krokem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: ffbdf6991830d554fc508d1d2fe8e4b9586210df
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70964182"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="9db56-103">Vytvoření první aplikace v Blazor</span><span class="sxs-lookup"><span data-stu-id="9db56-103">Build your first Blazor app</span></span>

<span data-ttu-id="9db56-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9db56-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9db56-105">V tomto kurzu se dozvíte, jak vytvořit a upravit aplikaci v Blazor.</span><span class="sxs-lookup"><span data-stu-id="9db56-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="9db56-106">Podle pokynů v <xref:blazor/get-started> článku vytvořte projekt Blazor pro tento kurz.</span><span class="sxs-lookup"><span data-stu-id="9db56-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="9db56-107">Pojmenujte projekt *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="9db56-107">Name the project *ToDoList*.</span></span>

## <a name="build-components"></a><span data-ttu-id="9db56-108">Komponenty sestavení</span><span class="sxs-lookup"><span data-stu-id="9db56-108">Build components</span></span>

1. <span data-ttu-id="9db56-109">Ve složce *Pages (stránky* ) vyhledejte jednotlivé tři stránky aplikace: Domů, čítač a načíst data.</span><span class="sxs-lookup"><span data-stu-id="9db56-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="9db56-110">Tyto stránky jsou implementovány pomocí indexu souborů komponent Razor *. Razor*, *Counter. Razor*a *FetchData. Razor*.</span><span class="sxs-lookup"><span data-stu-id="9db56-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="9db56-111">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="9db56-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="9db56-112">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale Blazor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="9db56-112">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="9db56-113">Projděte si implementaci `Counter` komponenty v souboru *Counter. Razor* .</span><span class="sxs-lookup"><span data-stu-id="9db56-113">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="9db56-114">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="9db56-114">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="9db56-115">Uživatelské rozhraní `Counter` komponenty je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="9db56-115">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="9db56-116">Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="9db56-116">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="9db56-117">Logika značek HTML a C# vykreslování jsou v době sestavení převedena na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="9db56-117">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="9db56-118">Název generované třídy .NET se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="9db56-118">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="9db56-119">Členy třídy komponenty jsou definovány v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="9db56-119">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="9db56-120">`@code` V bloku jsou pro zpracování událostí nebo pro definování jiné logiky komponent určeny stav součásti (vlastnosti, pole) a metody.</span><span class="sxs-lookup"><span data-stu-id="9db56-120">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="9db56-121">Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="9db56-121">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="9db56-122">Když je vybrané tlačítko pro **kliknutí na mou adresu** :</span><span class="sxs-lookup"><span data-stu-id="9db56-122">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="9db56-123">Registrovaná `Counter` obslužná`onclick`rutina komponenty je volána (metoda). `IncrementCount`</span><span class="sxs-lookup"><span data-stu-id="9db56-123">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="9db56-124">`Counter` Komponenta znovu vygeneruje svůj strom vykreslování.</span><span class="sxs-lookup"><span data-stu-id="9db56-124">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="9db56-125">Nový strom vykreslování je porovnán s předchozím.</span><span class="sxs-lookup"><span data-stu-id="9db56-125">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="9db56-126">Jsou aplikovány pouze změny model DOM (Document Object Model) (DOM).</span><span class="sxs-lookup"><span data-stu-id="9db56-126">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="9db56-127">Zobrazený počet je aktualizovaný.</span><span class="sxs-lookup"><span data-stu-id="9db56-127">The displayed count is updated.</span></span>

1. <span data-ttu-id="9db56-128">Upravte C# logiku `Counter` komponenty tak, aby byl přírůstek počtu o dva místo jednoho.</span><span class="sxs-lookup"><span data-stu-id="9db56-128">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="9db56-129">Pokud chcete zobrazit změny, sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9db56-129">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="9db56-130">Vyberte tlačítko pro **kliknutí na tlačítko** .</span><span class="sxs-lookup"><span data-stu-id="9db56-130">Select the **Click me** button.</span></span> <span data-ttu-id="9db56-131">Čítače se zvýší o dva.</span><span class="sxs-lookup"><span data-stu-id="9db56-131">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="9db56-132">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="9db56-132">Use components</span></span>

<span data-ttu-id="9db56-133">Zahrnutí komponenty do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="9db56-133">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="9db56-134">`Index` Přidejtekomponentudo`Index` komponenty aplikace přidáním `<Counter />` prvku do komponenty (*index. Razor*). `Counter`</span><span class="sxs-lookup"><span data-stu-id="9db56-134">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="9db56-135">Pokud pro toto prostředí používáte Blazor WebAssembly, `SurveyPrompt` komponenta se používá `Index` komponentou.</span><span class="sxs-lookup"><span data-stu-id="9db56-135">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="9db56-136">Nahraďte `<Counter />`elementelementem. `<SurveyPrompt>`</span><span class="sxs-lookup"><span data-stu-id="9db56-136">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="9db56-137">Pokud pro toto prostředí používáte aplikaci serveru Blazor, přidejte `<Counter />` element `Index` do komponenty:</span><span class="sxs-lookup"><span data-stu-id="9db56-137">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="9db56-138">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="9db56-138">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="9db56-139">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9db56-139">Rebuild and run the app.</span></span> <span data-ttu-id="9db56-140">`Index` Komponenta má vlastní čítač.</span><span class="sxs-lookup"><span data-stu-id="9db56-140">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="9db56-141">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="9db56-141">Component parameters</span></span>

<span data-ttu-id="9db56-142">Komponenty mohou mít také parametry.</span><span class="sxs-lookup"><span data-stu-id="9db56-142">Components can also have parameters.</span></span> <span data-ttu-id="9db56-143">Parametry komponenty jsou definovány pomocí veřejných vlastností třídy Component s `[Parameter]` atributem.</span><span class="sxs-lookup"><span data-stu-id="9db56-143">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="9db56-144">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="9db56-144">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="9db56-145">Aktualizujte C# kód komponenty `@code` :</span><span class="sxs-lookup"><span data-stu-id="9db56-145">Update the component's `@code` C# code:</span></span>

   * <span data-ttu-id="9db56-146">Přidejte veřejnou `IncrementAmount` vlastnost `[Parameter]` s atributem.</span><span class="sxs-lookup"><span data-stu-id="9db56-146">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="9db56-147">Změňte metodu na `IncrementAmount` použití`currentCount`při zvyšování hodnoty. `IncrementCount`</span><span class="sxs-lookup"><span data-stu-id="9db56-147">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="9db56-148">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="9db56-148">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="9db56-149">Zadejte parametr v elementu`<Counter>`komponentypomocíatributu. `Index` `IncrementAmount`</span><span class="sxs-lookup"><span data-stu-id="9db56-149">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="9db56-150">Nastavte hodnotu pro zvýšení čítače o deset.</span><span class="sxs-lookup"><span data-stu-id="9db56-150">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="9db56-151">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="9db56-151">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="9db56-152">Znovu načtěte `Index` komponentu.</span><span class="sxs-lookup"><span data-stu-id="9db56-152">Reload the `Index` component.</span></span> <span data-ttu-id="9db56-153">Čítač se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** .</span><span class="sxs-lookup"><span data-stu-id="9db56-153">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="9db56-154">Čítač v `Counter` součásti se stále zvyšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="9db56-154">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="9db56-155">Směrování na součásti</span><span class="sxs-lookup"><span data-stu-id="9db56-155">Route to components</span></span>

<span data-ttu-id="9db56-156">Direktiva v horní části `Counter` souboru *Counter. Razor* určuje, že komponenta je koncový bod směrování. `@page`</span><span class="sxs-lookup"><span data-stu-id="9db56-156">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="9db56-157">Komponenta zpracovává požadavky odeslané na `/counter`. `Counter`</span><span class="sxs-lookup"><span data-stu-id="9db56-157">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="9db56-158">`@page` Bez direktivy nezpracovávají součásti směrované požadavky, ale komponentu mohou i nadále používat jiné komponenty.</span><span class="sxs-lookup"><span data-stu-id="9db56-158">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="9db56-159">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="9db56-159">Dependency injection</span></span>

<span data-ttu-id="9db56-160">Služby zaregistrované v kontejneru služby aplikace jsou dostupné komponentám prostřednictvím [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9db56-160">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9db56-161">Vloží služby do komponenty pomocí `@inject` direktivy.</span><span class="sxs-lookup"><span data-stu-id="9db56-161">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="9db56-162">Projděte si direktivy `FetchData` komponenty.</span><span class="sxs-lookup"><span data-stu-id="9db56-162">Examine the directives of the `FetchData` component.</span></span>

<span data-ttu-id="9db56-163">Pokud pracujete s aplikací serveru Blazor, `WeatherForecastService` služba se zaregistruje jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes), takže v celé aplikaci je dostupná jedna instance služby.</span><span class="sxs-lookup"><span data-stu-id="9db56-163">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="9db56-164">Direktiva slouží k vložení instance `WeatherForecastService` služby do komponenty. `@inject`</span><span class="sxs-lookup"><span data-stu-id="9db56-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="9db56-165">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="9db56-165">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="9db56-166">Komponenta používá vloženou službu jako `ForecastService` `WeatherForecast` k načtení pole objektů: `FetchData`</span><span class="sxs-lookup"><span data-stu-id="9db56-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="9db56-167">Pokud pracujete s aplikací Blazor WebAssembly, `HttpClient` je vložena pro získání dat předpovědi počasí ze souboru *počasí. JSON* ve složce *wwwroot/Sample-data* :</span><span class="sxs-lookup"><span data-stu-id="9db56-167">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="9db56-168">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="9db56-168">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="9db56-169">Smyčka foreach se používá k vykreslení každé instance prognózy jako řádku v tabulce dat o počasí: [ \@](/dotnet/csharp/language-reference/keywords/foreach-in)</span><span class="sxs-lookup"><span data-stu-id="9db56-169">A [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]


## <a name="build-a-todo-list"></a><span data-ttu-id="9db56-170">Sestavení seznamu TODO</span><span class="sxs-lookup"><span data-stu-id="9db56-170">Build a todo list</span></span>

<span data-ttu-id="9db56-171">Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="9db56-171">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="9db56-172">Do aplikace ve složce *Pages* přidejte prázdný soubor s názvem *todo. Razor* :</span><span class="sxs-lookup"><span data-stu-id="9db56-172">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="9db56-173">Zadejte počáteční označení pro komponentu:</span><span class="sxs-lookup"><span data-stu-id="9db56-173">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="9db56-174">`Todo` Přidejte komponentu do navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="9db56-174">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="9db56-175">Komponenta (*Shared/NavMenu. Razor*) se používá v rozložení aplikace. `NavMenu`</span><span class="sxs-lookup"><span data-stu-id="9db56-175">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="9db56-176">Rozložení jsou komponenty, které umožňují vyhnout se duplikaci obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9db56-176">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="9db56-177">Přidejte element pro komponentu přidáním následujícího označení položky seznamu pod existující položky seznamu v souboru *Shared/NavMenu. Razor:* `Todo` `<NavLink>`</span><span class="sxs-lookup"><span data-stu-id="9db56-177">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="9db56-178">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9db56-178">Rebuild and run the app.</span></span> <span data-ttu-id="9db56-179">Přejděte na stránku Nová TODO a potvrďte, že odkaz na `Todo` komponentu funguje.</span><span class="sxs-lookup"><span data-stu-id="9db56-179">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="9db56-180">Do kořenového adresáře projektu přidejte soubor *TodoItem.cs* , který bude obsahovat třídu, která představuje položku todo.</span><span class="sxs-lookup"><span data-stu-id="9db56-180">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="9db56-181">Pro`TodoItem` třídu použijte C# následující kód:</span><span class="sxs-lookup"><span data-stu-id="9db56-181">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="9db56-182">Vraťte se do `Todo` komponenty (*Pages/todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="9db56-182">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="9db56-183">Přidejte pole pro položky ToDo v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="9db56-183">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="9db56-184">`Todo` Komponenta používá toto pole k údržbě stavu seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="9db56-184">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="9db56-185">Přidejte neuspořádaný seznam značek a `foreach` smyčku pro vykreslení každé položky ToDo jako položky seznamu (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="9db56-185">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="9db56-186">Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu.</span><span class="sxs-lookup"><span data-stu-id="9db56-186">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="9db56-187">Přidejte textové zadání (`<input>`) a tlačítko (`<button>`) pod Neseřazený seznam (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="9db56-187">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="9db56-188">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9db56-188">Rebuild and run the app.</span></span> <span data-ttu-id="9db56-189">Když je vybráno tlačítko **Přidat TODO** , nic se nestane, protože obslužná rutina události není na tlačítko kabelem.</span><span class="sxs-lookup"><span data-stu-id="9db56-189">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="9db56-190">Přidejte do komponenty metodu a zaregistrujte ji `@onclick` pro výběry tlačítek pomocí atributu. `AddTodo` `Todo`</span><span class="sxs-lookup"><span data-stu-id="9db56-190">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="9db56-191">C# volána, když je vybráno tlačítko `AddTodo` :</span><span class="sxs-lookup"><span data-stu-id="9db56-191">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="9db56-192">Chcete-li získat název nové položky ToDo, `newTodo` přidejte pole řetězce v horní `@code` části bloku a vytvořte jeho svázání s hodnotou textového zadání `<input>` pomocí `bind` atributu v elementu:</span><span class="sxs-lookup"><span data-stu-id="9db56-192">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="9db56-193">Aktualizujte `TodoItem` metodu tak, aby se do seznamu přidal název se zadaným názvem. `AddTodo`</span><span class="sxs-lookup"><span data-stu-id="9db56-193">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="9db56-194">Vymažte hodnotu textového zadání nastavením `newTodo` na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="9db56-194">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="9db56-195">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9db56-195">Rebuild and run the app.</span></span> <span data-ttu-id="9db56-196">Chcete-li otestovat nový kód, přidejte do seznamu TODO některé položky ToDo.</span><span class="sxs-lookup"><span data-stu-id="9db56-196">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="9db56-197">Text nadpisu pro každou položku TODO lze upravovat a zaškrtávací políčko může uživatelům pomáhat sledovat dokončené položky.</span><span class="sxs-lookup"><span data-stu-id="9db56-197">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="9db56-198">Přidejte vstup zaškrtávacího políčka pro každou položku TODO a navažte její hodnotu na `IsDone` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="9db56-198">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="9db56-199">Změnit `@todo.Title` `@todo.Title`na prvekvázanýna:`<input>`</span><span class="sxs-lookup"><span data-stu-id="9db56-199">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="9db56-200">Chcete-li ověřit, zda jsou tyto hodnoty vázány, aktualizujte `<h1>` záhlaví tak, aby zobrazovalo počet nedokončených položek TODO (`IsDone` je `false`).</span><span class="sxs-lookup"><span data-stu-id="9db56-200">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="9db56-201">Dokončená `Todo` součást (*Pages/todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="9db56-201">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="9db56-202">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9db56-202">Rebuild and run the app.</span></span> <span data-ttu-id="9db56-203">Přidejte položky ToDo pro otestování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="9db56-203">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
