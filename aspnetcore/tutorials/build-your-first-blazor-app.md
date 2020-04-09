---
title: Vytvoření první Blazor aplikace
author: guardrex
description: Vytvořte Blazor aplikaci krok za krokem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
uid: tutorials/first-blazor-app
ms.openlocfilehash: 138057c2ceb9ed01bdf958c01f5cf2275387df23
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989435"
---
# <a name="build-your-first-opno-locblazor-app"></a><span data-ttu-id="36396-103">Vytvoření první Blazor aplikace</span><span class="sxs-lookup"><span data-stu-id="36396-103">Build your first Blazor app</span></span>

<span data-ttu-id="36396-104">[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="36396-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="36396-105">V tomto kurzu se můžete Blazor stavit a upravit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36396-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

## <a name="build-components"></a><span data-ttu-id="36396-106">Vytváření součástí</span><span class="sxs-lookup"><span data-stu-id="36396-106">Build components</span></span>

1. <span data-ttu-id="36396-107">Postupujte podle <xref:blazor/get-started> pokynů v Blazor článku k vytvoření projektu pro tento kurz.</span><span class="sxs-lookup"><span data-stu-id="36396-107">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="36396-108">Název projektu *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="36396-108">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="36396-109">Přejděte na všechny tři stránky aplikace ve složce *Stránky:* Domů, Čítač a Načíst data.</span><span class="sxs-lookup"><span data-stu-id="36396-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="36396-110">Tyto stránky jsou implementovány razor dílčí soubory *Index.razor*, *Counter.razor*, a *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="36396-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="36396-111">Na stránce Čítač vyberte tlačítko Klikněte na **mě,** chcete-li čítač narůst bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="36396-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="36396-112">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="36396-112">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="36396-113">S Blazor, můžete napsat C# místo.</span><span class="sxs-lookup"><span data-stu-id="36396-113">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="36396-114">Zkontrolujte implementaci `Counter` součásti v souboru *Counter.razor.*</span><span class="sxs-lookup"><span data-stu-id="36396-114">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="36396-115">*Stránky/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="36396-115">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="36396-116">UI komponenty `Counter` je definováno pomocí HTML.</span><span class="sxs-lookup"><span data-stu-id="36396-116">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="36396-117">Logika dynamického vykreslování (například smyčky, podmínky, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="36396-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="36396-118">Logika html a vykreslování Jazyka C# jsou převedeny na třídu komponenty v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="36396-118">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="36396-119">Název generované třídy .NET odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="36396-119">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="36396-120">Členové třídy komponent jsou `@code` definováni v bloku.</span><span class="sxs-lookup"><span data-stu-id="36396-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="36396-121">V `@code` bloku jsou pro zpracování událostí nebo pro definování jiné logiky komponenty určeny stav komponenty (vlastnosti, pole) a metody.</span><span class="sxs-lookup"><span data-stu-id="36396-121">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="36396-122">Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="36396-122">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="36396-123">Když je vybráno tlačítko **Klikněte na mě:**</span><span class="sxs-lookup"><span data-stu-id="36396-123">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="36396-124">Je `Counter` volána `onclick` registrovaná obslužná rutina komponenty `IncrementCount` (metoda).</span><span class="sxs-lookup"><span data-stu-id="36396-124">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="36396-125">Komponenta `Counter` regeneruje svůj strom vykreslení.</span><span class="sxs-lookup"><span data-stu-id="36396-125">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="36396-126">Nový strom vykreslení je porovnán s předchozím stromem.</span><span class="sxs-lookup"><span data-stu-id="36396-126">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="36396-127">Použijí se pouze změny objektového modelu dokumentu (DOM).</span><span class="sxs-lookup"><span data-stu-id="36396-127">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="36396-128">Zobrazený počet je aktualizován.</span><span class="sxs-lookup"><span data-stu-id="36396-128">The displayed count is updated.</span></span>

1. <span data-ttu-id="36396-129">Upravte logiku C# komponenty `Counter` tak, aby se počet zvýšil o dva místo jednoho.</span><span class="sxs-lookup"><span data-stu-id="36396-129">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="36396-130">Znovu sestavte a spusťte aplikaci, abyste viděli změny.</span><span class="sxs-lookup"><span data-stu-id="36396-130">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="36396-131">Vyberte **tlačítko Kliknout na tlačítko** .</span><span class="sxs-lookup"><span data-stu-id="36396-131">Select the **Click me** button.</span></span> <span data-ttu-id="36396-132">Čítač se ztoho čítač utápějí o dvě.</span><span class="sxs-lookup"><span data-stu-id="36396-132">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="36396-133">Použití součástí</span><span class="sxs-lookup"><span data-stu-id="36396-133">Use components</span></span>

<span data-ttu-id="36396-134">Zahrnout komponentu do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="36396-134">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="36396-135">Přidejte `Counter` komponentu do `Index` součásti aplikace `<Counter />` přidáním `Index` prvku do komponenty (*Index.razor*).</span><span class="sxs-lookup"><span data-stu-id="36396-135">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="36396-136">Pokud pro toto prostředí používáte Blazor `SurveyPrompt` WebAssembly, komponenta je komponentou `Index` používána.</span><span class="sxs-lookup"><span data-stu-id="36396-136">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="36396-137">Nahraďte `<SurveyPrompt>` prvek `<Counter />` elementem.</span><span class="sxs-lookup"><span data-stu-id="36396-137">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="36396-138">Pokud pro toto Blazor prostředí používáte aplikaci `<Counter />` Server, `Index` přidejte prvek do komponenty:</span><span class="sxs-lookup"><span data-stu-id="36396-138">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="36396-139">*Stránky/Index.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="36396-139">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="36396-140">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36396-140">Rebuild and run the app.</span></span> <span data-ttu-id="36396-141">Komponenta `Index` má svůj vlastní čítač.</span><span class="sxs-lookup"><span data-stu-id="36396-141">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="36396-142">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="36396-142">Component parameters</span></span>

<span data-ttu-id="36396-143">Komponenty mohou mít také parametry.</span><span class="sxs-lookup"><span data-stu-id="36396-143">Components can also have parameters.</span></span> <span data-ttu-id="36396-144">Parametry komponenty jsou definovány pomocí veřejných vlastností třídy komponenty s atributem. `[Parameter]`</span><span class="sxs-lookup"><span data-stu-id="36396-144">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="36396-145">Pomocí atributů můžete zadat argumenty pro komponentu ve značkách.</span><span class="sxs-lookup"><span data-stu-id="36396-145">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="36396-146">Aktualizujte kód `@code` C# komponenty takto:</span><span class="sxs-lookup"><span data-stu-id="36396-146">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="36396-147">Přidejte `IncrementAmount` veřejnou `[Parameter]` vlastnost s atributem.</span><span class="sxs-lookup"><span data-stu-id="36396-147">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="36396-148">Změňte `IncrementCount` metodu, `IncrementAmount` která má být `currentCount`používána vlastnost při zvyšování hodnoty .</span><span class="sxs-lookup"><span data-stu-id="36396-148">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="36396-149">*Stránky/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="36396-149">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="36396-150">Zadejte `IncrementAmount` parametr `Index` v elementu komponenty `<Counter>` pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="36396-150">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="36396-151">Nastavte hodnotu na zvýšení čítače o deset.</span><span class="sxs-lookup"><span data-stu-id="36396-151">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="36396-152">*Stránky/Index.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="36396-152">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="36396-153">Znovu načtěte `Index` součást.</span><span class="sxs-lookup"><span data-stu-id="36396-153">Reload the `Index` component.</span></span> <span data-ttu-id="36396-154">Čítač se při každém výběru tlačítka **Klikni mi** pozbytčí se deset.</span><span class="sxs-lookup"><span data-stu-id="36396-154">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="36396-155">Čítač `Counter` v součásti se nadále zvedá o jednu.</span><span class="sxs-lookup"><span data-stu-id="36396-155">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="36396-156">Směrovat na součásti</span><span class="sxs-lookup"><span data-stu-id="36396-156">Route to components</span></span>

<span data-ttu-id="36396-157">Direktiva `@page` v horní části souboru `Counter` *Counter.razor* určuje, že komponenta je koncový bod směrování.</span><span class="sxs-lookup"><span data-stu-id="36396-157">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="36396-158">Součást `Counter` zpracovává požadavky odeslané `/counter`společnosti .</span><span class="sxs-lookup"><span data-stu-id="36396-158">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="36396-159">Bez `@page` směrnice součást nezpracovává směrované požadavky, ale komponenta může být stále používána jinými součástmi.</span><span class="sxs-lookup"><span data-stu-id="36396-159">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="36396-160">Injektáž závislosti</span><span class="sxs-lookup"><span data-stu-id="36396-160">Dependency injection</span></span>

### <a name="opno-locblazor-server-experience"></a>Blazor<span data-ttu-id="36396-161">Serverové prostředí</span><span class="sxs-lookup"><span data-stu-id="36396-161"> Server experience</span></span>

<span data-ttu-id="36396-162">Pokud pracujete Blazor s aplikací `WeatherForecastService` Server, služba je registrována jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes) v aplikaci `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="36396-162">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36396-163">Instance služby je k dispozici v celé aplikaci prostřednictvím [vkládání závislostí (DI)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="36396-163">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="36396-164">Směrnice `@inject` se používá k vložení `WeatherForecastService` instance služby do komponenty. `FetchData`</span><span class="sxs-lookup"><span data-stu-id="36396-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="36396-165">*Stránky/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="36396-165">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="36396-166">Komponenta `FetchData` používá vstřikované služby, jako `ForecastService`, načíst pole `WeatherForecast` objektů:</span><span class="sxs-lookup"><span data-stu-id="36396-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a>Blazor<span data-ttu-id="36396-167">Webassembly zkušenosti</span><span class="sxs-lookup"><span data-stu-id="36396-167"> WebAssembly experience</span></span>

<span data-ttu-id="36396-168">Pokud pracujete Blazor s aplikací `HttpClient` WebAssembly, je vložen získat data předpověď počasí ze souboru *weather.json* ve složce *wwwroot/sample-data.*</span><span class="sxs-lookup"><span data-stu-id="36396-168">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="36396-169">*Stránky/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="36396-169">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="36396-170">Smyčka [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) se používá k vykreslení každé instance prognózy jako řádku v tabulce dat o počasí:</span><span class="sxs-lookup"><span data-stu-id="36396-170">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="36396-171">Sestavení seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="36396-171">Build a todo list</span></span>

<span data-ttu-id="36396-172">Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="36396-172">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="36396-173">Přidejte `Todo` do aplikace novou komponentu Razor ve složce *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="36396-173">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="36396-174">V sadě Visual Studio klepněte pravým tlačítkem myši na složku **Stránky** a vyberte **přidat** > **komponentu Razor položky** > **Razor Component**.</span><span class="sxs-lookup"><span data-stu-id="36396-174">In Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="36396-175">Pojmenujte soubor komponenty *Todo.razor*.</span><span class="sxs-lookup"><span data-stu-id="36396-175">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="36396-176">V jiných vývojových prostředích přidejte prázdný soubor do složky **Stránky** s názvem *Todo.razor*.</span><span class="sxs-lookup"><span data-stu-id="36396-176">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="36396-177">Zadejte počáteční značky pro komponentu:</span><span class="sxs-lookup"><span data-stu-id="36396-177">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="36396-178">Přidejte `Todo` komponentu na navigační panel.</span><span class="sxs-lookup"><span data-stu-id="36396-178">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="36396-179">Komponenta `NavMenu` (*Shared/NavMenu.razor*) se používá v rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="36396-179">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="36396-180">Rozložení jsou součásti, které umožňují vyhnout se duplikaci obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36396-180">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="36396-181">Přidejte `<NavLink>` prvek `Todo` pro komponentu přidáním následujících značek položek seznamu pod existující položky seznamu v souboru *Shared/NavMenu.razor:*</span><span class="sxs-lookup"><span data-stu-id="36396-181">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="36396-182">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36396-182">Rebuild and run the app.</span></span> <span data-ttu-id="36396-183">Navštivte novou stránku Todo a potvrďte, že odkaz na komponentu `Todo` funguje.</span><span class="sxs-lookup"><span data-stu-id="36396-183">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="36396-184">Přidejte *TodoItem.cs* soubor do kořenového adresáře projektu pro uložení třídy, která představuje položku todo.</span><span class="sxs-lookup"><span data-stu-id="36396-184">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="36396-185">Pro `TodoItem` třídu použijte následující kód jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="36396-185">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="36396-186">Zpět na `Todo` komponentu *(Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="36396-186">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="36396-187">Přidejte pole pro položky `@code` todo v bloku.</span><span class="sxs-lookup"><span data-stu-id="36396-187">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="36396-188">Komponenta `Todo` používá toto pole k zachování stavu seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="36396-188">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="36396-189">Přidejte neuspořádané značky seznamu a `foreach` smyčku, která`<li>`vykreslí každou položku úkolů jako položku seznamu ( ).</span><span class="sxs-lookup"><span data-stu-id="36396-189">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="36396-190">Aplikace vyžaduje prvky uživatelského rozhraní pro přidávání položek úkolů do seznamu.</span><span class="sxs-lookup"><span data-stu-id="36396-190">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="36396-191">Přidejte textový`<input>`vstup ( )`<button>`a tlačítko (`<ul>...</ul>`) pod neuspořádaný seznam ( ):</span><span class="sxs-lookup"><span data-stu-id="36396-191">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="36396-192">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36396-192">Rebuild and run the app.</span></span> <span data-ttu-id="36396-193">Když je vybráno tlačítko **Přidat todo,** nic se nestane, protože obslužná rutina události není připojena k tlačítku.</span><span class="sxs-lookup"><span data-stu-id="36396-193">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="36396-194">Přidejte `AddTodo` metodu `Todo` do komponenty a zaregistrujte `@onclick` ji pro výběry tlačítek pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="36396-194">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="36396-195">Metoda `AddTodo` Jazyka C# je volána, když je tlačítko vybráno:</span><span class="sxs-lookup"><span data-stu-id="36396-195">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="36396-196">Chcete-li získat název nové položky `newTodo` todo, přidejte `@code` pole řetězce v horní části bloku a `bind` svázat `<input>` ji s hodnotou vstupu textu pomocí atributu v elementu:</span><span class="sxs-lookup"><span data-stu-id="36396-196">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="36396-197">Aktualizujte `AddTodo` metodu `TodoItem` a přidejte do seznamu zadaný název.</span><span class="sxs-lookup"><span data-stu-id="36396-197">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="36396-198">Zrušte hodnotu vstupu textu `newTodo` nastavením prázdného řetězce:</span><span class="sxs-lookup"><span data-stu-id="36396-198">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="36396-199">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36396-199">Rebuild and run the app.</span></span> <span data-ttu-id="36396-200">Přidejte některé položky úkolů do seznamu úkolů a otestujte nový kód.</span><span class="sxs-lookup"><span data-stu-id="36396-200">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="36396-201">Název textu pro každou položku úkolů lze upravit a zaškrtávací políčko může uživateli pomoci sledovat dokončené položky.</span><span class="sxs-lookup"><span data-stu-id="36396-201">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="36396-202">Přidejte vstup zaškrtávacího políčka pro `IsDone` každou položku todo a spojte její hodnotu s vlastností.</span><span class="sxs-lookup"><span data-stu-id="36396-202">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="36396-203">Změna `@todo.Title` prvku `<input>` vázaného `@todo.Title`na :</span><span class="sxs-lookup"><span data-stu-id="36396-203">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="36396-204">Chcete-li ověřit, zda jsou `<h3>` tyto hodnoty svázány, aktualizujte záhlaví tak,`IsDone` aby `false`zobrazovala počet položek, které nejsou dokončeny ( je ).</span><span class="sxs-lookup"><span data-stu-id="36396-204">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="36396-205">Dokončená `Todo` komponenta (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="36396-205">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="36396-206">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="36396-206">Rebuild and run the app.</span></span> <span data-ttu-id="36396-207">Přidejte položky todo k testování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="36396-207">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
