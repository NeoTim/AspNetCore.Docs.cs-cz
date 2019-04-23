---
title: Vytvořte svoji první aplikaci Blazor
author: guardrex
description: Vytvoření podrobné Blazor aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: c52efcb1224e2bbea56fa55c70faf253ef96d433
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982756"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="e2c10-103">Vytvořte svoji první aplikaci Blazor</span><span class="sxs-lookup"><span data-stu-id="e2c10-103">Build your first Blazor app</span></span>

<span data-ttu-id="e2c10-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e2c10-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e2c10-105">V tomto kurzu se dozvíte, jak vytvářet a upravovat aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="e2c10-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="e2c10-106">Postupujte podle pokynů v <xref:blazor/get-started> článku vytvořte projekt Blazor pro účely tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="e2c10-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span>

## <a name="build-components"></a><span data-ttu-id="e2c10-107">Sestavení komponent</span><span class="sxs-lookup"><span data-stu-id="e2c10-107">Build components</span></span>

1. <span data-ttu-id="e2c10-108">Přejděte do všech tří stránek vaší aplikace v *stránky* složky: Domů čítač a načíst data.</span><span class="sxs-lookup"><span data-stu-id="e2c10-108">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="e2c10-109">Tyto stránky jsou implementovány v souborech Razor komponenty: *Index.Razor*, *Counter.razor*, a *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="e2c10-109">These pages are implemented by Razor Component files: *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="e2c10-110">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="e2c10-110">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="e2c10-111">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale Blazor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="e2c10-111">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="e2c10-112">Vyzkoušení implementace čítač součástí *Counter.razor* souboru.</span><span class="sxs-lookup"><span data-stu-id="e2c10-112">Examine the implementation of the Counter component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="e2c10-113">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="e2c10-113">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="e2c10-114">Uživatelské rozhraní komponenty čítač je definován v jazyce HTML.</span><span class="sxs-lookup"><span data-stu-id="e2c10-114">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="e2c10-115">Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="e2c10-115">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="e2c10-116">Značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty v okamžiku sestavení.</span><span class="sxs-lookup"><span data-stu-id="e2c10-116">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="e2c10-117">Název generované třídy .NET odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="e2c10-117">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="e2c10-118">Členy třídy komponenty jsou definovány v `@functions` bloku.</span><span class="sxs-lookup"><span data-stu-id="e2c10-118">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="e2c10-119">V `@functions` blokovat, stav komponent (vlastnosti, pole) a metody jsou určené pro zpracování událostí nebo definování dalších součástí logiky.</span><span class="sxs-lookup"><span data-stu-id="e2c10-119">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="e2c10-120">Tyto členy, se použije jako součást logiky komponenty vykreslování a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="e2c10-120">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="e2c10-121">Když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="e2c10-121">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="e2c10-122">Součást čítače zaregistrované `onclick` obslužná rutina volána ( `IncrementCount` metoda).</span><span class="sxs-lookup"><span data-stu-id="e2c10-122">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="e2c10-123">Součást čítače obnoví jeho vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="e2c10-123">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="e2c10-124">Nový vykreslovací stromu je ve srovnání s předchozím histogramem.</span><span class="sxs-lookup"><span data-stu-id="e2c10-124">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="e2c10-125">Se použijí pouze změny do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="e2c10-125">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="e2c10-126">Počet zobrazených se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="e2c10-126">The displayed count is updated.</span></span>

1. <span data-ttu-id="e2c10-127">Upravit C# logiky součást čítače aby přírůstek počtu dvou místo jednoho.</span><span class="sxs-lookup"><span data-stu-id="e2c10-127">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="e2c10-128">Znovu sestavte a spusťte aplikaci, aby se změny projevily.</span><span class="sxs-lookup"><span data-stu-id="e2c10-128">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="e2c10-129">Vyberte **klikněte na mě** tlačítko a zvýší čítač ve dvou.</span><span class="sxs-lookup"><span data-stu-id="e2c10-129">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="e2c10-130">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="e2c10-130">Use components</span></span>

<span data-ttu-id="e2c10-131">Zahrnout součásti do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="e2c10-131">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="e2c10-132">Přidat součást čítače pro součást aplikace indexu (domů) tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="e2c10-132">Add the Counter component to the app's Index (Home) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="e2c10-133">Pokud používáte Blazor pro toto prostředí, průzkum výzvy součásti (`<SurveyPrompt>` element) je v komponentě indexu.</span><span class="sxs-lookup"><span data-stu-id="e2c10-133">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="e2c10-134">Nahraďte `<SurveyPrompt>` křížkem `<Counter>` elementu.</span><span class="sxs-lookup"><span data-stu-id="e2c10-134">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="e2c10-135">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="e2c10-135">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="e2c10-136">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e2c10-136">Rebuild and run the app.</span></span> <span data-ttu-id="e2c10-137">Domovská stránka obsahuje vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="e2c10-137">The Home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="e2c10-138">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="e2c10-138">Component parameters</span></span>

<span data-ttu-id="e2c10-139">Součástí mohou mít také parametry.</span><span class="sxs-lookup"><span data-stu-id="e2c10-139">Components can also have parameters.</span></span> <span data-ttu-id="e2c10-140">Parametry komponenty jsou definovány pomocí vlastnosti neveřejné na komponentní třída dekorován `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="e2c10-140">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="e2c10-141">Atributy můžete zadat argumenty pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="e2c10-141">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="e2c10-142">Aktualizace komponenty `@functions` C# kódu:</span><span class="sxs-lookup"><span data-stu-id="e2c10-142">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="e2c10-143">Přidat `IncrementAmount` vlastnost upravené pomocí `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="e2c10-143">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="e2c10-144">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="e2c10-144">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="e2c10-145">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="e2c10-145">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="e2c10-146">Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="e2c10-146">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="e2c10-147">Nastavte hodnotu čítače přírůstku deset.</span><span class="sxs-lookup"><span data-stu-id="e2c10-147">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="e2c10-148">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="e2c10-148">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="e2c10-149">Znovu načte na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="e2c10-149">Reload the Home page.</span></span> <span data-ttu-id="e2c10-150">Hodnota čítače se zvýší o hodnotu deset pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="e2c10-150">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="e2c10-151">Čítače na stránku zvýší čítač jednou.</span><span class="sxs-lookup"><span data-stu-id="e2c10-151">The counter on the Counter page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="e2c10-152">Směrovat do komponenty</span><span class="sxs-lookup"><span data-stu-id="e2c10-152">Route to components</span></span>

<span data-ttu-id="e2c10-153">`@page` Direktiv v horní části *Counter.razor* soubor Určuje, že tato součást je koncový bod směrování.</span><span class="sxs-lookup"><span data-stu-id="e2c10-153">The `@page` directive at the top of the *Counter.razor* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="e2c10-154">Součást čítače zpracovává požadavky odeslané na `/Counter`.</span><span class="sxs-lookup"><span data-stu-id="e2c10-154">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="e2c10-155">Bez `@page` direktiv, komponenta nebude zpracovávat směrování žádostí, ale součást je stále možné ostatními komponentami.</span><span class="sxs-lookup"><span data-stu-id="e2c10-155">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="e2c10-156">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="e2c10-156">Dependency injection</span></span>

<span data-ttu-id="e2c10-157">Služby zaregistrované v kontejneru aplikace služby jsou k dispozici na komponenty prostřednictvím [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e2c10-157">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e2c10-158">Vložit do součástí s použitím služby `@inject` směrnice.</span><span class="sxs-lookup"><span data-stu-id="e2c10-158">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="e2c10-159">Prozkoumejte direktivy FetchData komponenty.</span><span class="sxs-lookup"><span data-stu-id="e2c10-159">Examine the directives of the FetchData component.</span></span>

<span data-ttu-id="e2c10-160">Je-li pracovat s aplikací Blazor na straně serveru, `WeatherForecastService` není registrován jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes), takže jedna instance služby je k dispozici v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e2c10-160">If working with a Blazor server-side app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="e2c10-161">`@inject` Směrnice slouží k vložení instance `WeatherForecastService` služby do komponenty.</span><span class="sxs-lookup"><span data-stu-id="e2c10-161">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="e2c10-162">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="e2c10-162">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="e2c10-163">Komponenta FetchData používá vložený služby jako `ForecastService`, k načtení pole `WeatherForecast` objekty:</span><span class="sxs-lookup"><span data-stu-id="e2c10-163">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="e2c10-164">Je-li pracovat s aplikací na straně klienta Blazor, `HttpClient` se vloží získávat data předpovědi počasí z *weather.json* ve *wwwroot/ukázková data* složky:</span><span class="sxs-lookup"><span data-stu-id="e2c10-164">If working with a Blazor client-side app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="e2c10-165">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="e2c10-165">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7)]

<span data-ttu-id="e2c10-166">A [ \@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) smyčky se použije k vykreslení každou prognózy instanci jako řadu data o počasí v tabulce:</span><span class="sxs-lookup"><span data-stu-id="e2c10-166">A [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]


## <a name="build-a-todo-list"></a><span data-ttu-id="e2c10-167">Vytvoření seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="e2c10-167">Build a todo list</span></span>

<span data-ttu-id="e2c10-168">Přidáte novou součást aplikaci, která implementuje seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="e2c10-168">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="e2c10-169">Přidat prázdný soubor nazvaný *Todo.razor* k aplikaci v *stránky* složky:</span><span class="sxs-lookup"><span data-stu-id="e2c10-169">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="e2c10-170">Zadejte počáteční značka pro komponentu:</span><span class="sxs-lookup"><span data-stu-id="e2c10-170">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="e2c10-171">Přidáte součást Todo do navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="e2c10-171">Add the Todo component to the navigation bar.</span></span>

   <span data-ttu-id="e2c10-172">Komponenta NavMenu (*Pages/Shared/NavMenu.razor*) se používá v rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="e2c10-172">The NavMenu component (*Pages/Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="e2c10-173">Rozložení jsou komponenty, které umožňují, aby se zabránilo duplicitě obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e2c10-173">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="e2c10-174">Další informace naleznete v tématu <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="e2c10-174">For more information, see <xref:blazor/layouts>.</span></span>

   <span data-ttu-id="e2c10-175">Přidat `<NavLink>` pro komponentu Todo tak, že přidáte následující značky položky seznamu níže existující položky seznamu v *Pages/Shared/NavMenu.razor* souboru:</span><span class="sxs-lookup"><span data-stu-id="e2c10-175">Add a `<NavLink>` for the Todo component by adding the following list item markup below the existing list items in the *Pages/Shared/NavMenu.razor* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="e2c10-176">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e2c10-176">Rebuild and run the app.</span></span> <span data-ttu-id="e2c10-177">Na stránce Nový Todo potvrďte, že odkaz na komponentu Todo funguje.</span><span class="sxs-lookup"><span data-stu-id="e2c10-177">Visit the new Todo page to confirm that the link to the Todo component works.</span></span>

1. <span data-ttu-id="e2c10-178">Přidat *TodoItem.cs* souboru do kořenového adresáře projektu k uložení třídu, která představuje položku seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="e2c10-178">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="e2c10-179">Pomocí následujících C# kód `TodoItem` třídy:</span><span class="sxs-lookup"><span data-stu-id="e2c10-179">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="e2c10-180">Vraťte se do komponenty Todo (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="e2c10-180">Return to the Todo component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="e2c10-181">Přidání pole pro úloh, ať už v `@functions` bloku.</span><span class="sxs-lookup"><span data-stu-id="e2c10-181">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="e2c10-182">Todo součásti používá toto pole pro uchování stavu pro seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="e2c10-182">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="e2c10-183">Přidat neuspořádaný seznam značek a `foreach` smyčky k vykreslení každé položky todo jako položku seznamu.</span><span class="sxs-lookup"><span data-stu-id="e2c10-183">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="e2c10-184">Aplikace vyžaduje pro přidání do seznamu úloh, ať už prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e2c10-184">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="e2c10-185">Přidání textového zadání a tlačítka v seznamu níže:</span><span class="sxs-lookup"><span data-stu-id="e2c10-185">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="e2c10-186">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e2c10-186">Rebuild and run the app.</span></span> <span data-ttu-id="e2c10-187">Když **přidat todo** se vybere tlačítko, nic se nestane, protože obslužná rutina události není svázanou tlačítka.</span><span class="sxs-lookup"><span data-stu-id="e2c10-187">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="e2c10-188">Přidat `AddTodo` metodu pro součást Todo a zaregistrujte ho pro tlačítko klikne pomocí `onclick` atribut:</span><span class="sxs-lookup"><span data-stu-id="e2c10-188">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   <span data-ttu-id="e2c10-189">`AddTodo` C# Metoda je volána při výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="e2c10-189">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="e2c10-190">Pokud chcete získat název nové položky seznamu úkolů, přidejte `newTodo` řetězec pole a navázat jej na hodnotu text input pomocí `bind` atribut:</span><span class="sxs-lookup"><span data-stu-id="e2c10-190">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. <span data-ttu-id="e2c10-191">Aktualizace `AddTodo` způsob, jak přidat `TodoItem` se zadaným názvem do seznamu.</span><span class="sxs-lookup"><span data-stu-id="e2c10-191">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="e2c10-192">Smazat hodnotu textového zadání tak, že nastavíte `newTodo` na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="e2c10-192">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="e2c10-193">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e2c10-193">Rebuild and run the app.</span></span> <span data-ttu-id="e2c10-194">Přidání některých úloh, ať už do seznamu k testování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="e2c10-194">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="e2c10-195">Text nadpisu pro každou položku seznamu úkolů lze upravovat a zaškrtávací políčko může pomoci udržovat přehled o dokončené položky uživatele.</span><span class="sxs-lookup"><span data-stu-id="e2c10-195">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="e2c10-196">Přidat vstup zaškrtněte políčko u každé položky todo a její hodnotu na vytvoření vazby `IsDone` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="e2c10-196">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="e2c10-197">Změna `@todo.Title` do `<input>` prvek vázán na `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="e2c10-197">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="e2c10-198">Chcete-li ověřit, že tyto hodnoty jsou vázány, aktualizujte `<h1>` záhlaví zobrazíte počet nesplněné položky seznamu úkolů nejsou kompletní (`IsDone` je `false`).</span><span class="sxs-lookup"><span data-stu-id="e2c10-198">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="e2c10-199">Dokončené komponenty Todo (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="e2c10-199">The completed Todo component (*Pages/Todo.razor*):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="e2c10-200">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e2c10-200">Rebuild and run the app.</span></span> <span data-ttu-id="e2c10-201">Přidání položky seznamu úkolů k testování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="e2c10-201">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="e2c10-202">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="e2c10-202">Publish and deploy the app</span></span>

<span data-ttu-id="e2c10-203">Publikování aplikace, najdete v článku <xref:host-and-deploy/blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="e2c10-203">To publish the app, see <xref:host-and-deploy/blazor/index>.</span></span>
