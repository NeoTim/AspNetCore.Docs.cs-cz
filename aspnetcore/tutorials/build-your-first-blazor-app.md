---
title: Vytvořte svoji první aplikaci Blazor
author: guardrex
description: Vytvoření podrobné Blazor aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: d592c5bac1eb9822843a1ad1513a15fdfd6b1032
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500315"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="ae579-103">Vytvořte svoji první aplikaci Blazor</span><span class="sxs-lookup"><span data-stu-id="ae579-103">Build your first Blazor app</span></span>

<span data-ttu-id="ae579-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ae579-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ae579-105">V tomto kurzu se dozvíte, jak vytvářet a upravovat aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="ae579-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="ae579-106">Postupujte podle pokynů v <xref:blazor/get-started> článku vytvořte projekt Blazor pro účely tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="ae579-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="ae579-107">Pojmenujte projekt *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="ae579-107">Name the project *ToDoList*.</span></span>

## <a name="build-components"></a><span data-ttu-id="ae579-108">Sestavení komponent</span><span class="sxs-lookup"><span data-stu-id="ae579-108">Build components</span></span>

1. <span data-ttu-id="ae579-109">Přejděte do všech tří stránek vaší aplikace v *stránky* složky: Domů čítač a načíst data.</span><span class="sxs-lookup"><span data-stu-id="ae579-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="ae579-110">Tyto stránky jsou implementované soubory součástí Razor *Index.razor*, *Counter.razor*, a *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="ae579-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="ae579-111">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="ae579-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="ae579-112">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale Blazor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="ae579-112">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="ae579-113">Prozkoumat provádění `Counter` v komponentu *Counter.razor* souboru.</span><span class="sxs-lookup"><span data-stu-id="ae579-113">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="ae579-114">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="ae579-114">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="ae579-115">Uživatelské rozhraní `Counter` komponenta je definován v jazyce HTML.</span><span class="sxs-lookup"><span data-stu-id="ae579-115">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="ae579-116">Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="ae579-116">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="ae579-117">Značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty v okamžiku sestavení.</span><span class="sxs-lookup"><span data-stu-id="ae579-117">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="ae579-118">Název generované třídy .NET odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="ae579-118">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="ae579-119">Členy třídy komponenty jsou definovány v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="ae579-119">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="ae579-120">V `@code` blokovat, stav komponent (vlastnosti, pole) a metody jsou určené pro zpracování událostí nebo definování dalších součástí logiky.</span><span class="sxs-lookup"><span data-stu-id="ae579-120">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="ae579-121">Tyto členy, se použije jako součást logiky komponenty vykreslování a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="ae579-121">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="ae579-122">Když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="ae579-122">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="ae579-123">`Counter` Zaregistrované komponenty `onclick` obslužná rutina volána ( `IncrementCount` metoda).</span><span class="sxs-lookup"><span data-stu-id="ae579-123">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="ae579-124">`Counter` Obnoví komponenty jeho vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="ae579-124">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="ae579-125">Nový vykreslovací stromu je ve srovnání s předchozím histogramem.</span><span class="sxs-lookup"><span data-stu-id="ae579-125">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="ae579-126">Se použijí pouze změny do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="ae579-126">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="ae579-127">Počet zobrazených se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="ae579-127">The displayed count is updated.</span></span>

1. <span data-ttu-id="ae579-128">Upravit C# logiku `Counter` součást přírůstek počtu ve dvou místo jednoho.</span><span class="sxs-lookup"><span data-stu-id="ae579-128">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="ae579-129">Znovu sestavte a spusťte aplikaci, aby se změny projevily.</span><span class="sxs-lookup"><span data-stu-id="ae579-129">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="ae579-130">Vyberte **klikněte na mě** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="ae579-130">Select the **Click me** button.</span></span> <span data-ttu-id="ae579-131">Zvýší čítač ve dvou.</span><span class="sxs-lookup"><span data-stu-id="ae579-131">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="ae579-132">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="ae579-132">Use components</span></span>

<span data-ttu-id="ae579-133">Zahrňte součásti v jiné součásti pomocí syntaxe pro HTML.</span><span class="sxs-lookup"><span data-stu-id="ae579-133">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="ae579-134">Přidat `Counter` komponentu aplikace `Index` tak, že přidáte komponentu `<Counter />` elementu `Index` součásti (*Index.razor*).</span><span class="sxs-lookup"><span data-stu-id="ae579-134">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="ae579-135">Pokud používáte Blazor na straně klienta pro toto prostředí `SurveyPrompt` používá komponenty `Index` komponenty.</span><span class="sxs-lookup"><span data-stu-id="ae579-135">If you're using Blazor client-side for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="ae579-136">Nahradit `<SurveyPrompt>` element s `<Counter />` elementu.</span><span class="sxs-lookup"><span data-stu-id="ae579-136">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="ae579-137">Pokud používáte aplikaci na straně serveru Blazor pro toto prostředí, přidejte `<Counter />` elementu `Index` komponenty:</span><span class="sxs-lookup"><span data-stu-id="ae579-137">If you're using a Blazor server-side app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="ae579-138">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="ae579-138">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="ae579-139">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ae579-139">Rebuild and run the app.</span></span> <span data-ttu-id="ae579-140">`Index` Komponenta má svůj vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="ae579-140">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="ae579-141">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="ae579-141">Component parameters</span></span>

<span data-ttu-id="ae579-142">Součástí mohou mít také parametry.</span><span class="sxs-lookup"><span data-stu-id="ae579-142">Components can also have parameters.</span></span> <span data-ttu-id="ae579-143">Parametry komponenty jsou definovány pomocí vlastnosti neveřejné na komponentní třída dekorován `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="ae579-143">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="ae579-144">Atributy můžete zadat argumenty pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="ae579-144">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="ae579-145">Aktualizace komponenty `@code` C# kódu:</span><span class="sxs-lookup"><span data-stu-id="ae579-145">Update the component's `@code` C# code:</span></span>

   * <span data-ttu-id="ae579-146">Přidat `IncrementAmount` vlastnost upravené pomocí `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="ae579-146">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="ae579-147">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="ae579-147">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="ae579-148">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="ae579-148">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="ae579-149">Zadejte `IncrementAmount` parametr `Index` komponenty `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="ae579-149">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="ae579-150">Nastavte hodnotu čítače přírůstku deset.</span><span class="sxs-lookup"><span data-stu-id="ae579-150">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="ae579-151">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="ae579-151">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="ae579-152">Znovu načíst `Index` komponenty.</span><span class="sxs-lookup"><span data-stu-id="ae579-152">Reload the `Index` component.</span></span> <span data-ttu-id="ae579-153">Hodnota čítače se zvýší o hodnotu deset pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="ae579-153">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="ae579-154">Čítače v `Counter` součást pokračuje se zvýší o jedna.</span><span class="sxs-lookup"><span data-stu-id="ae579-154">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="ae579-155">Směrovat do komponenty</span><span class="sxs-lookup"><span data-stu-id="ae579-155">Route to components</span></span>

<span data-ttu-id="ae579-156">`@page` Direktiv v horní části *Counter.razor* souboru Určuje, že `Counter` komponenta je koncový bod směrování.</span><span class="sxs-lookup"><span data-stu-id="ae579-156">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="ae579-157">`Counter` Komponenta zpracovává požadavky odeslané na `/counter`.</span><span class="sxs-lookup"><span data-stu-id="ae579-157">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="ae579-158">Bez `@page` direktiv, komponenta nebude zpracovávat směrování žádostí, ale komponenta je stále možné ostatními komponentami.</span><span class="sxs-lookup"><span data-stu-id="ae579-158">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="ae579-159">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="ae579-159">Dependency injection</span></span>

<span data-ttu-id="ae579-160">Služby zaregistrované v kontejneru aplikace služby jsou k dispozici na komponenty prostřednictvím [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ae579-160">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ae579-161">Vložit do součástí s použitím služby `@inject` směrnice.</span><span class="sxs-lookup"><span data-stu-id="ae579-161">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="ae579-162">Prozkoumejte direktivy z `FetchData` komponenty.</span><span class="sxs-lookup"><span data-stu-id="ae579-162">Examine the directives of the `FetchData` component.</span></span>

<span data-ttu-id="ae579-163">Je-li pracovat s aplikací Blazor na straně serveru, `WeatherForecastService` není registrován jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes), takže jedna instance služby je k dispozici v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae579-163">If working with a Blazor server-side app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="ae579-164">`@inject` Směrnice slouží k vložení instance `WeatherForecastService` služby do komponenty.</span><span class="sxs-lookup"><span data-stu-id="ae579-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="ae579-165">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="ae579-165">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="ae579-166">`FetchData` Komponenta používá vložený služby jako `ForecastService`, k načtení pole `WeatherForecast` objekty:</span><span class="sxs-lookup"><span data-stu-id="ae579-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="ae579-167">Je-li pracovat s aplikací na straně klienta Blazor, `HttpClient` se vloží získávat data předpovědi počasí z *weather.json* ve *wwwroot/ukázková data* složky:</span><span class="sxs-lookup"><span data-stu-id="ae579-167">If working with a Blazor client-side app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="ae579-168">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="ae579-168">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="ae579-169">A [ \@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) smyčky se použije k vykreslení každou prognózy instanci jako řadu data o počasí v tabulce:</span><span class="sxs-lookup"><span data-stu-id="ae579-169">A [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]


## <a name="build-a-todo-list"></a><span data-ttu-id="ae579-170">Vytvoření seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="ae579-170">Build a todo list</span></span>

<span data-ttu-id="ae579-171">Přidáte novou součást aplikaci, která implementuje seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="ae579-171">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="ae579-172">Přidat prázdný soubor nazvaný *Todo.razor* k aplikaci v *stránky* složky:</span><span class="sxs-lookup"><span data-stu-id="ae579-172">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="ae579-173">Zadejte počáteční značka pro komponentu:</span><span class="sxs-lookup"><span data-stu-id="ae579-173">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="ae579-174">Přidat `Todo` komponentu do navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="ae579-174">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="ae579-175">`NavMenu` Součásti (*Shared/NavMenu.razor*) se používá v rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae579-175">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="ae579-176">Rozložení jsou komponenty, které umožňují, aby se zabránilo duplicitě obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ae579-176">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="ae579-177">Přidat `<NavLink>` – element pro `Todo` součásti tak, že přidáte následující značky položky seznamu níže existující seznam položek v *Shared/NavMenu.razor* souboru:</span><span class="sxs-lookup"><span data-stu-id="ae579-177">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="ae579-178">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ae579-178">Rebuild and run the app.</span></span> <span data-ttu-id="ae579-179">Na stránce Nový Todo potvrdit, že odkaz `Todo` komponenty funguje.</span><span class="sxs-lookup"><span data-stu-id="ae579-179">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="ae579-180">Přidat *TodoItem.cs* souboru do kořenového adresáře projektu k uložení třídu, která představuje položku seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="ae579-180">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="ae579-181">Pomocí následujících C# kód `TodoItem` třídy:</span><span class="sxs-lookup"><span data-stu-id="ae579-181">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="ae579-182">Vraťte se `Todo` součásti (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="ae579-182">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="ae579-183">Přidání polí pro položky seznamu úkolů `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="ae579-183">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="ae579-184">`Todo` Součásti používá toto pole pro uchování stavu pro seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="ae579-184">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="ae579-185">Přidat neuspořádaný seznam značek a `foreach` smyčky k vykreslení každé položky todo jako položku seznamu (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="ae579-185">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="ae579-186">Aplikace vyžaduje prvky uživatelského rozhraní pro přidávání položek todo do seznamu.</span><span class="sxs-lookup"><span data-stu-id="ae579-186">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="ae579-187">Přidat textový vstup (`<input>`) a tlačítko (`<button>`) pod neuspořádaný seznam (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="ae579-187">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="ae579-188">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ae579-188">Rebuild and run the app.</span></span> <span data-ttu-id="ae579-189">Když **přidat todo** se vybere tlačítko, nic se nestane, protože obslužná rutina události není svázanou tlačítka.</span><span class="sxs-lookup"><span data-stu-id="ae579-189">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="ae579-190">Přidat `AddTodo` metodu `Todo` komponenty a zaregistrujte ho pro výběr tlačítka pomocí `@onclick` atribut.</span><span class="sxs-lookup"><span data-stu-id="ae579-190">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="ae579-191">`AddTodo` C# Metoda je volána při výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="ae579-191">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="ae579-192">Chcete-li získat název nové položky seznamu úkolů, přidejte `newTodo` pole řetězce v horní části `@code` blokovat a navázat jej na hodnotu text input pomocí `bind` atribut v `<input>` element:</span><span class="sxs-lookup"><span data-stu-id="ae579-192">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="@newTodo" />
   ```

1. <span data-ttu-id="ae579-193">Aktualizace `AddTodo` způsob, jak přidat `TodoItem` se zadaným názvem do seznamu.</span><span class="sxs-lookup"><span data-stu-id="ae579-193">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="ae579-194">Smazat hodnotu textového zadání tak, že nastavíte `newTodo` na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="ae579-194">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="ae579-195">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ae579-195">Rebuild and run the app.</span></span> <span data-ttu-id="ae579-196">Přidejte několik položek todo do seznamu todo k testování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="ae579-196">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="ae579-197">Text názvu pro každou položku seznamu úkolů lze upravovat, a zaškrtávací políčko může pomoci udržovat přehled o dokončené položky uživatele.</span><span class="sxs-lookup"><span data-stu-id="ae579-197">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="ae579-198">Přidat vstup zaškrtněte políčko u každé položky todo a její hodnotu na vytvoření vazby `IsDone` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ae579-198">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="ae579-199">Změna `@todo.Title` do `<input>` prvek vázán na `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="ae579-199">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="ae579-200">Chcete-li ověřit, že tyto hodnoty jsou vázány, aktualizujte `<h1>` záhlaví zobrazíte počet nesplněné položky seznamu úkolů nejsou kompletní (`IsDone` je `false`).</span><span class="sxs-lookup"><span data-stu-id="ae579-200">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="ae579-201">Dokončené `Todo` součásti (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="ae579-201">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="ae579-202">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ae579-202">Rebuild and run the app.</span></span> <span data-ttu-id="ae579-203">Přidání položky seznamu úkolů k testování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="ae579-203">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
