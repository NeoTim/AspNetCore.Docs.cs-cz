---
title: Vytvořte svoji první aplikaci součásti syntaxe Razor
author: guardrex
description: Vytvoření podrobné Razor komponent aplikace a seznamte se základními koncepty framework součásti syntaxe Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2019
uid: tutorials/first-razor-components-app
ms.openlocfilehash: 4bf3884d5d9575ebf2a09237e364b37fa1b35246
ms.sourcegitcommit: 3c2ba9a0d833d2a096d9d800ba67a1a7f9491af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854599"
---
# <a name="build-your-first-razor-components-app"></a><span data-ttu-id="4ca06-103">Vytvořte svoji první aplikaci součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="4ca06-103">Build your first Razor Components app</span></span>

<span data-ttu-id="4ca06-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4ca06-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="4ca06-105">V tomto kurzu se dozvíte, jak vytvořit aplikaci Razor součásti a ukazuje základní koncepty framework součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="4ca06-105">This tutorial shows you how to build a Razor Components app and demonstrates basic Razor Components framework concepts.</span></span>

<span data-ttu-id="4ca06-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4ca06-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="4ca06-107">Najdete v článku [Začínáme](xref:razor-components/get-started) tématu pro požadavky.</span><span class="sxs-lookup"><span data-stu-id="4ca06-107">See the [Get started](xref:razor-components/get-started) topic for prerequisites.</span></span>

## <a name="create-an-app-from-the-razor-components-template"></a><span data-ttu-id="4ca06-108">Vytvoření aplikace ze šablony Razor komponenty</span><span class="sxs-lookup"><span data-stu-id="4ca06-108">Create an app from the Razor Components template</span></span>

<span data-ttu-id="4ca06-109">Postupujte podle pokynů v [Začínáme](xref:razor-components/get-started) tématu k vytvoření Razor součásti projektu ze šablony Razor komponenty.</span><span class="sxs-lookup"><span data-stu-id="4ca06-109">Follow the guidance in the [Get started](xref:razor-components/get-started) topic to create a Razor Components project from the Razor Components template.</span></span> <span data-ttu-id="4ca06-110">Pojmenujte řešení *WebApplication1*.</span><span class="sxs-lookup"><span data-stu-id="4ca06-110">Name the solution *WebApplication1*.</span></span> <span data-ttu-id="4ca06-111">Visual Studio nebo příkazového prostředí můžete použít s příkazy rozhraní příkazového řádku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ca06-111">You can use Visual Studio or a command shell with .NET Core CLI commands.</span></span>

## <a name="build-components"></a><span data-ttu-id="4ca06-112">Sestavení komponent</span><span class="sxs-lookup"><span data-stu-id="4ca06-112">Build components</span></span>

1. <span data-ttu-id="4ca06-113">Přejděte do všech tří stránek vaší aplikace: Domů čítač a načíst data.</span><span class="sxs-lookup"><span data-stu-id="4ca06-113">Browse to each of the app's three pages: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="4ca06-114">Tyto stránky jsou implementovány v souborech Razor v *WebApplication1.App/Pages* složky: *Index.cshtml*, *Counter.cshtml*, a *FetchData.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4ca06-114">These pages are implemented by Razor files in the *WebApplication1.App/Pages* folder: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.</span></span>

1. <span data-ttu-id="4ca06-115">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="4ca06-115">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="4ca06-116">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti Razor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="4ca06-116">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

1. <span data-ttu-id="4ca06-117">Vyzkoušení implementace čítač součástí *Counter.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="4ca06-117">Examine the implementation of the Counter component in the *Counter.cshtml* file.</span></span>

   <span data-ttu-id="4ca06-118">*WebApplication1.App/Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4ca06-118">*WebApplication1.App/Pages/Counter.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter1.cshtml)]

   <span data-ttu-id="4ca06-119">Uživatelské rozhraní komponenty čítač je definován v jazyce HTML.</span><span class="sxs-lookup"><span data-stu-id="4ca06-119">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="4ca06-120">Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="4ca06-120">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="4ca06-121">Značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty v okamžiku sestavení.</span><span class="sxs-lookup"><span data-stu-id="4ca06-121">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="4ca06-122">Název generované třídy .NET odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="4ca06-122">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="4ca06-123">Členy třídy komponenty jsou definovány v `@functions` bloku.</span><span class="sxs-lookup"><span data-stu-id="4ca06-123">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="4ca06-124">V `@functions` blokovat, stav komponent (vlastnosti, pole) a metody jsou určené pro zpracování událostí nebo definování dalších součástí logiky.</span><span class="sxs-lookup"><span data-stu-id="4ca06-124">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="4ca06-125">Tyto členy, se použije jako součást logiky komponenty vykreslování a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="4ca06-125">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="4ca06-126">Když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="4ca06-126">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="4ca06-127">Součást čítače zaregistrované `onclick` obslužná rutina volána ( `IncrementCount` metoda).</span><span class="sxs-lookup"><span data-stu-id="4ca06-127">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="4ca06-128">Součást čítače obnoví jeho vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="4ca06-128">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="4ca06-129">Nový vykreslovací stromu je ve srovnání s předchozím histogramem.</span><span class="sxs-lookup"><span data-stu-id="4ca06-129">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="4ca06-130">Se použijí pouze změny do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="4ca06-130">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="4ca06-131">Počet zobrazených se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="4ca06-131">The displayed count is updated.</span></span>

1. <span data-ttu-id="4ca06-132">Upravit C# logiky součást čítače aby přírůstek počtu dvou místo jednoho.</span><span class="sxs-lookup"><span data-stu-id="4ca06-132">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter2.cshtml?highlight=14)]

1. <span data-ttu-id="4ca06-133">Znovu sestavte a spusťte aplikaci, aby se změny projevily.</span><span class="sxs-lookup"><span data-stu-id="4ca06-133">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="4ca06-134">Vyberte **klikněte na mě** tlačítko a zvýší čítač ve dvou.</span><span class="sxs-lookup"><span data-stu-id="4ca06-134">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="4ca06-135">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="4ca06-135">Use components</span></span>

<span data-ttu-id="4ca06-136">Zahrnout součásti do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="4ca06-136">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="4ca06-137">Přidat součást čítače pro součást aplikace indexu (Domovská stránka) tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="4ca06-137">Add the Counter component to the app's Index (home page) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="4ca06-138">*WebApplication1.App/Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4ca06-138">*WebApplication1.App/Pages/Index.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Index.cshtml?highlight=7)]

1. <span data-ttu-id="4ca06-139">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ca06-139">Rebuild and run the app.</span></span> <span data-ttu-id="4ca06-140">Domovská stránka obsahuje vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="4ca06-140">The home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="4ca06-141">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="4ca06-141">Component parameters</span></span>

<span data-ttu-id="4ca06-142">Součástí mohou mít také parametry.</span><span class="sxs-lookup"><span data-stu-id="4ca06-142">Components can also have parameters.</span></span> <span data-ttu-id="4ca06-143">Parametry komponenty jsou definovány pomocí vlastnosti neveřejné na komponentní třída dekorován `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="4ca06-143">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="4ca06-144">Atributy můžete zadat argumenty pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="4ca06-144">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="4ca06-145">Aktualizace komponenty `@functions` C# kódu:</span><span class="sxs-lookup"><span data-stu-id="4ca06-145">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="4ca06-146">Přidat `IncrementAmount` vlastnost upravené pomocí `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="4ca06-146">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="4ca06-147">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="4ca06-147">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="4ca06-148">*WebApplication1.App/Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4ca06-148">*WebApplication1.App/Pages/Counter.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/WebApplication1/WebApplication1.App/Pages/Counter.cshtml?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="4ca06-149">Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="4ca06-149">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="4ca06-150">Nastavte hodnotu čítače přírůstku deset.</span><span class="sxs-lookup"><span data-stu-id="4ca06-150">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="4ca06-151">*WebApplication1.App/Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4ca06-151">*WebApplication1.App/Pages/Index.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/WebApplication1/WebApplication1.App/Pages/Index.cshtml?highlight=7)]

1. <span data-ttu-id="4ca06-152">Načtěte tuto stránku.</span><span class="sxs-lookup"><span data-stu-id="4ca06-152">Reload the page.</span></span> <span data-ttu-id="4ca06-153">Domovská stránka čítače se zvýší o hodnotu deset pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="4ca06-153">The home page counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="4ca06-154">Čítače na *čítač* stránce zvýší o jedna.</span><span class="sxs-lookup"><span data-stu-id="4ca06-154">The counter on the *Counter* page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="4ca06-155">Směrovat do komponenty</span><span class="sxs-lookup"><span data-stu-id="4ca06-155">Route to components</span></span>

<span data-ttu-id="4ca06-156">`@page` Direktiv v horní části *Counter.cshtml* soubor Určuje, že tato součást je koncový bod směrování.</span><span class="sxs-lookup"><span data-stu-id="4ca06-156">The `@page` directive at the top of the *Counter.cshtml* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="4ca06-157">Součást čítače zpracovává požadavky odeslané na `/Counter`.</span><span class="sxs-lookup"><span data-stu-id="4ca06-157">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="4ca06-158">Bez `@page` direktiv, komponenta nebude zpracovávat směrování žádostí, ale součást je stále možné ostatními komponentami.</span><span class="sxs-lookup"><span data-stu-id="4ca06-158">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="4ca06-159">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="4ca06-159">Dependency injection</span></span>

<span data-ttu-id="4ca06-160">Služby zaregistrované v kontejneru aplikace služby jsou k dispozici na komponenty prostřednictvím [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4ca06-160">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4ca06-161">Vložit do součástí s použitím služby `@inject` směrnice.</span><span class="sxs-lookup"><span data-stu-id="4ca06-161">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="4ca06-162">Prozkoumat direktivy FetchData součásti (*WebApplication1.App/Pages/FetchData.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="4ca06-162">Examine the directives of the FetchData component (*WebApplication1.App/Pages/FetchData.cshtml*).</span></span> <span data-ttu-id="4ca06-163">`@inject` Směrnice slouží k vložení instance `WeatherForecastService` služby do komponenty:</span><span class="sxs-lookup"><span data-stu-id="4ca06-163">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=3)]

<span data-ttu-id="4ca06-164">`WeatherForecastService` Není registrován jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes), takže jedna instance služby je k dispozici v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ca06-164">The `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span>

<span data-ttu-id="4ca06-165">Komponenta FetchData používá vložený služby jako `ForecastService`, k načtení pole `WeatherForecast` objekty:</span><span class="sxs-lookup"><span data-stu-id="4ca06-165">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData2.cshtml?highlight=6)]

<span data-ttu-id="4ca06-166">A [ @foreach ](/dotnet/csharp/language-reference/keywords/foreach-in) smyčky se použije k vykreslení každou prognózy instanci jako řadu data o počasí v tabulce:</span><span class="sxs-lookup"><span data-stu-id="4ca06-166">A [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData3.cshtml?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="4ca06-167">Vytvoření seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="4ca06-167">Build a todo list</span></span>

<span data-ttu-id="4ca06-168">Přidejte novou stránku do aplikace, která implementuje seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="4ca06-168">Add a new page to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="4ca06-169">Přidat prázdný soubor *WebApplication1.App/Pages* složku s názvem *Todo.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4ca06-169">Add an empty file to the *WebApplication1.App/Pages* folder named *Todo.cshtml*.</span></span>

1. <span data-ttu-id="4ca06-170">Na stránce zadejte počáteční značky:</span><span class="sxs-lookup"><span data-stu-id="4ca06-170">Provide the initial markup for the page:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="4ca06-171">Přidáte stránku Todo do navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="4ca06-171">Add the Todo page to the navigation bar.</span></span>

   <span data-ttu-id="4ca06-172">Komponenta NavMenu (*WebApplication1/Shared/NavMenu.csthml*) se používá v rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ca06-172">The NavMenu component (*WebApplication1/Shared/NavMenu.csthml*) is used in the app's layout.</span></span> <span data-ttu-id="4ca06-173">Rozložení jsou komponenty, které umožňují, aby se zabránilo duplicitě obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ca06-173">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="4ca06-174">Další informace naleznete v tématu <xref:razor-components/layouts>.</span><span class="sxs-lookup"><span data-stu-id="4ca06-174">For more information, see <xref:razor-components/layouts>.</span></span>

   <span data-ttu-id="4ca06-175">Přidat `<NavLink>` Todo stránky tak, že přidáte následující značky položky seznamu níže existující položky seznamu v *WebApplication1/Shared/NavMenu.csthml* souboru:</span><span class="sxs-lookup"><span data-stu-id="4ca06-175">Add a `<NavLink>` for the Todo page by adding the following list item markup below the existing list items in the *WebApplication1/Shared/NavMenu.csthml* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="4ca06-176">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ca06-176">Rebuild and run the app.</span></span> <span data-ttu-id="4ca06-177">Na stránce Nový Todo potvrďte, že odkaz na stránku Todo funguje.</span><span class="sxs-lookup"><span data-stu-id="4ca06-177">Visit the new Todo page to confirm that the link to the Todo page works.</span></span>

1. <span data-ttu-id="4ca06-178">Přidat *TodoItem.cs* souboru do kořenového adresáře projektu k uložení třídu, která představuje položku seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="4ca06-178">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="4ca06-179">Pomocí následujících C# kód `TodoItem` třídy:</span><span class="sxs-lookup"><span data-stu-id="4ca06-179">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/WebApplication1/WebApplication1.App/TodoItem.cs)]

1. <span data-ttu-id="4ca06-180">Vraťte se do komponenty Todo (*Todo.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="4ca06-180">Return to the Todo component (*Todo.cshtml*):</span></span>

   * <span data-ttu-id="4ca06-181">Přidání pole pro úloh, ať už v `@functions` bloku.</span><span class="sxs-lookup"><span data-stu-id="4ca06-181">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="4ca06-182">Todo součásti používá toto pole pro uchování stavu pro seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="4ca06-182">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="4ca06-183">Přidat neuspořádaný seznam značek a `foreach` smyčky k vykreslení každé položky todo jako položku seznamu.</span><span class="sxs-lookup"><span data-stu-id="4ca06-183">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData4.cshtml?highlight=5-10,12-14)]

1. <span data-ttu-id="4ca06-184">Aplikace vyžaduje pro přidání do seznamu úloh, ať už prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4ca06-184">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="4ca06-185">Přidání textového zadání a tlačítka v seznamu níže:</span><span class="sxs-lookup"><span data-stu-id="4ca06-185">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData5.cshtml?highlight=12-13)]

1. <span data-ttu-id="4ca06-186">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ca06-186">Rebuild and run the app.</span></span> <span data-ttu-id="4ca06-187">Když **přidat todo** se vybere tlačítko, nic se nestane, protože obslužná rutina události není svázanou tlačítka.</span><span class="sxs-lookup"><span data-stu-id="4ca06-187">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="4ca06-188">Přidat `AddTodo` metodu pro součást Todo a zaregistrujte ho pro tlačítko klikne pomocí `onclick` atribut:</span><span class="sxs-lookup"><span data-stu-id="4ca06-188">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData6.cshtml?highlight=2,7-10)]

   <span data-ttu-id="4ca06-189">`AddTodo` C# Metoda je volána při výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="4ca06-189">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="4ca06-190">Pokud chcete získat název nové položky seznamu úkolů, přidejte `newTodo` řetězec pole a navázat jej na hodnotu text input pomocí `bind` atribut:</span><span class="sxs-lookup"><span data-stu-id="4ca06-190">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData7.cshtml?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo" />
   ```

1. <span data-ttu-id="4ca06-191">Aktualizace `AddTodo` způsob, jak přidat `TodoItem` se zadaným názvem do seznamu.</span><span class="sxs-lookup"><span data-stu-id="4ca06-191">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="4ca06-192">Smazat hodnotu textového zadání tak, že nastavíte `newTodo` na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="4ca06-192">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData8.cshtml?highlight=19-26)]

1. <span data-ttu-id="4ca06-193">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ca06-193">Rebuild and run the app.</span></span> <span data-ttu-id="4ca06-194">Přidání některých úloh, ať už do seznamu k testování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="4ca06-194">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="4ca06-195">Text nadpisu pro každou položku seznamu úkolů lze upravovat a zaškrtávací políčko může pomoci udržovat přehled o dokončené položky uživatele.</span><span class="sxs-lookup"><span data-stu-id="4ca06-195">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="4ca06-196">Přidat vstup zaškrtněte políčko u každé položky todo a její hodnotu na vytvoření vazby `IsDone` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4ca06-196">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="4ca06-197">Změna `@todo.Title` do `<input>` prvek vázán na `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="4ca06-197">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData9.cshtml?highlight=5-6)]

1. <span data-ttu-id="4ca06-198">Chcete-li ověřit, že tyto hodnoty jsou vázány, aktualizujte `<h1>` záhlaví zobrazíte počet nesplněné položky seznamu úkolů nejsou kompletní (`IsDone` je `false`).</span><span class="sxs-lookup"><span data-stu-id="4ca06-198">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="4ca06-199">Dokončené komponenty Todo (*Todo.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="4ca06-199">The completed Todo component (*Todo.cshtml*):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/WebApplication1/WebApplication1.App/Pages/Todo.cshtml)]

1. <span data-ttu-id="4ca06-200">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ca06-200">Rebuild and run the app.</span></span> <span data-ttu-id="4ca06-201">Přidání položky seznamu úkolů k testování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="4ca06-201">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="4ca06-202">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="4ca06-202">Publish and deploy the app</span></span>

<span data-ttu-id="4ca06-203">Publikování aplikace, najdete v článku <xref:host-and-deploy/razor-components/index#publish-the-app>.</span><span class="sxs-lookup"><span data-stu-id="4ca06-203">To publish the app, see <xref:host-and-deploy/razor-components/index#publish-the-app>.</span></span>
