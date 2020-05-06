---
title: Sestavení první Blazor aplikace
author: guardrex
description: Sestavte Blazor aplikaci krok za krokem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 5a5a56ee646cba21a883df2cf686cb1ccb18d7f9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776601"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="8cd60-103">Sestavení první Blazor aplikace</span><span class="sxs-lookup"><span data-stu-id="8cd60-103">Build your first Blazor app</span></span>

<span data-ttu-id="8cd60-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8cd60-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="8cd60-105">V Blazor tomto kurzu se dozvíte, jak vytvořit a upravit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8cd60-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

## <a name="build-components"></a><span data-ttu-id="8cd60-106">Komponenty sestavení</span><span class="sxs-lookup"><span data-stu-id="8cd60-106">Build components</span></span>

1. <span data-ttu-id="8cd60-107">Pokud chcete vytvořit Blazor projekt pro <xref:blazor/get-started> tento kurz, postupujte podle pokynů v článku.</span><span class="sxs-lookup"><span data-stu-id="8cd60-107">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="8cd60-108">Pojmenujte projekt *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="8cd60-108">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="8cd60-109">Ve složce *Pages (stránky* ) přejděte na jednotlivé tři stránky aplikace: domů, čítač a načíst data.</span><span class="sxs-lookup"><span data-stu-id="8cd60-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="8cd60-110">Tyto stránky jsou implementovány pomocí Razor indexu souborů komponenty *index. Razor*, *Counter. Razor*a *FetchData. Razor*.</span><span class="sxs-lookup"><span data-stu-id="8cd60-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="8cd60-111">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="8cd60-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="8cd60-112">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8cd60-112">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="8cd60-113">Pomocí Blazormůžete místo toho napsat C#.</span><span class="sxs-lookup"><span data-stu-id="8cd60-113">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="8cd60-114">Projděte si implementaci `Counter` komponenty v souboru *Counter. Razor* .</span><span class="sxs-lookup"><span data-stu-id="8cd60-114">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="8cd60-115">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="8cd60-115">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="8cd60-116">Uživatelské rozhraní `Counter` komponenty je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="8cd60-116">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="8cd60-117">Dynamická logika vykreslování (například cykly, podmíněné výrazy, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="8cd60-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="8cd60-118">Značky HTML značek a vykreslování v jazyce C# jsou převedeny na třídu komponenty v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="8cd60-118">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="8cd60-119">Název generované třídy .NET se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="8cd60-119">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="8cd60-120">Členy třídy komponenty jsou definovány v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="8cd60-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="8cd60-121">V `@code` bloku jsou pro zpracování událostí nebo pro definování jiné logiky komponent určeny stav součásti (vlastnosti, pole) a metody.</span><span class="sxs-lookup"><span data-stu-id="8cd60-121">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="8cd60-122">Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="8cd60-122">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="8cd60-123">Když je vybrané tlačítko pro **kliknutí na mou adresu** :</span><span class="sxs-lookup"><span data-stu-id="8cd60-123">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="8cd60-124">Registrovaná `Counter` `onclick` obslužná rutina komponenty je volána ( `IncrementCount` metoda).</span><span class="sxs-lookup"><span data-stu-id="8cd60-124">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="8cd60-125">`Counter` Komponenta znovu vygeneruje svůj strom vykreslování.</span><span class="sxs-lookup"><span data-stu-id="8cd60-125">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="8cd60-126">Nový strom vykreslování je porovnán s předchozím.</span><span class="sxs-lookup"><span data-stu-id="8cd60-126">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="8cd60-127">Jsou aplikovány pouze změny model DOM (Document Object Model) (DOM).</span><span class="sxs-lookup"><span data-stu-id="8cd60-127">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="8cd60-128">Zobrazený počet je aktualizovaný.</span><span class="sxs-lookup"><span data-stu-id="8cd60-128">The displayed count is updated.</span></span>

1. <span data-ttu-id="8cd60-129">Upravte logiku `Counter` komponenty jazyka C# tak, aby byl přírůstek počtu vynásoben dvěma místomi jednoho.</span><span class="sxs-lookup"><span data-stu-id="8cd60-129">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="8cd60-130">Pokud chcete zobrazit změny, sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8cd60-130">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="8cd60-131">Vyberte tlačítko pro **kliknutí na tlačítko** .</span><span class="sxs-lookup"><span data-stu-id="8cd60-131">Select the **Click me** button.</span></span> <span data-ttu-id="8cd60-132">Čítače se zvýší o dva.</span><span class="sxs-lookup"><span data-stu-id="8cd60-132">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="8cd60-133">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="8cd60-133">Use components</span></span>

<span data-ttu-id="8cd60-134">Zahrnutí komponenty do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="8cd60-134">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="8cd60-135">Přidejte `Counter` komponentu `Index` do komponenty aplikace přidáním `<Counter />` prvku do `Index` komponenty (*index. Razor*).</span><span class="sxs-lookup"><span data-stu-id="8cd60-135">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="8cd60-136">Pokud pro toto prostředí Blazor používáte WebAssembly, `SurveyPrompt` komponenta je používána `Index` komponentou.</span><span class="sxs-lookup"><span data-stu-id="8cd60-136">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="8cd60-137">Nahraďte `<SurveyPrompt>` element `<Counter />` elementem.</span><span class="sxs-lookup"><span data-stu-id="8cd60-137">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="8cd60-138">Pokud pro toto prostředí používáte Blazor serverovou aplikaci, přidejte `<Counter />` element do `Index` komponenty:</span><span class="sxs-lookup"><span data-stu-id="8cd60-138">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="8cd60-139">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="8cd60-139">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="8cd60-140">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8cd60-140">Rebuild and run the app.</span></span> <span data-ttu-id="8cd60-141">`Index` Komponenta má vlastní čítač.</span><span class="sxs-lookup"><span data-stu-id="8cd60-141">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="8cd60-142">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="8cd60-142">Component parameters</span></span>

<span data-ttu-id="8cd60-143">Komponenty mohou mít také parametry.</span><span class="sxs-lookup"><span data-stu-id="8cd60-143">Components can also have parameters.</span></span> <span data-ttu-id="8cd60-144">Parametry komponenty jsou definovány pomocí veřejných vlastností třídy Component s `[Parameter]` atributem.</span><span class="sxs-lookup"><span data-stu-id="8cd60-144">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="8cd60-145">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="8cd60-145">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="8cd60-146">Aktualizujte kód `@code` C# komponenty následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="8cd60-146">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="8cd60-147">Přidejte veřejnou `IncrementAmount` vlastnost s `[Parameter]` atributem.</span><span class="sxs-lookup"><span data-stu-id="8cd60-147">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="8cd60-148">Změňte `IncrementCount` metodu na použití `IncrementAmount` vlastnosti při zvyšování hodnoty. `currentCount`</span><span class="sxs-lookup"><span data-stu-id="8cd60-148">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="8cd60-149">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="8cd60-149">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="8cd60-150">Zadejte `IncrementAmount` parametr v `Index` `<Counter>` elementu komponenty pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="8cd60-150">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="8cd60-151">Nastavte hodnotu pro zvýšení čítače o deset.</span><span class="sxs-lookup"><span data-stu-id="8cd60-151">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="8cd60-152">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="8cd60-152">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="8cd60-153">Znovu načtěte `Index` komponentu.</span><span class="sxs-lookup"><span data-stu-id="8cd60-153">Reload the `Index` component.</span></span> <span data-ttu-id="8cd60-154">Čítač se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** .</span><span class="sxs-lookup"><span data-stu-id="8cd60-154">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="8cd60-155">Čítač v `Counter` součásti se stále zvyšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="8cd60-155">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="8cd60-156">Směrování na součásti</span><span class="sxs-lookup"><span data-stu-id="8cd60-156">Route to components</span></span>

<span data-ttu-id="8cd60-157">Direktiva v horní části souboru *Counter. Razor* určuje, že `Counter` komponenta je koncový bod směrování. `@page`</span><span class="sxs-lookup"><span data-stu-id="8cd60-157">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="8cd60-158">`Counter` Komponenta zpracovává požadavky odeslané na `/counter`.</span><span class="sxs-lookup"><span data-stu-id="8cd60-158">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="8cd60-159">Bez `@page` direktivy nezpracovávají součásti směrované požadavky, ale komponentu mohou i nadále používat jiné komponenty.</span><span class="sxs-lookup"><span data-stu-id="8cd60-159">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="8cd60-160">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="8cd60-160">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="8cd60-161">Prostředí serveru</span><span class="sxs-lookup"><span data-stu-id="8cd60-161"> Server experience</span></span>

<span data-ttu-id="8cd60-162">Pokud pracujete se Blazor serverovou aplikací, `WeatherForecastService` služba je v `Startup.ConfigureServices`nástroji registrována jako typ [singleton](xref:fundamentals/dependency-injection#service-lifetimes) .</span><span class="sxs-lookup"><span data-stu-id="8cd60-162">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8cd60-163">Instance služby je k dispozici v celé aplikaci prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="8cd60-163">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="8cd60-164">`@inject` Direktiva slouží k vložení instance `WeatherForecastService` služby do `FetchData` komponenty.</span><span class="sxs-lookup"><span data-stu-id="8cd60-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="8cd60-165">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="8cd60-165">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="8cd60-166">`FetchData` Komponenta používá vloženou službu jako `ForecastService`k načtení pole `WeatherForecast` objektů:</span><span class="sxs-lookup"><span data-stu-id="8cd60-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="8cd60-167">Prostředí WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8cd60-167"> WebAssembly experience</span></span>

<span data-ttu-id="8cd60-168">Při práci Blazor s aplikací WebAssembly `HttpClient` je vloženo pro získání dat předpovědi počasí ze souboru *počasí. JSON* ve složce *wwwroot/Sample-data* .</span><span class="sxs-lookup"><span data-stu-id="8cd60-168">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="8cd60-169">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="8cd60-169">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="8cd60-170">[`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) Smyčka se používá k vykreslení každé instance prognózy jako řádku v tabulce dat o počasí:</span><span class="sxs-lookup"><span data-stu-id="8cd60-170">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="8cd60-171">Sestavení seznamu TODO</span><span class="sxs-lookup"><span data-stu-id="8cd60-171">Build a todo list</span></span>

<span data-ttu-id="8cd60-172">Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="8cd60-172">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="8cd60-173">Přidejte do aplikace `Todo` Razor novou komponentu ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="8cd60-173">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="8cd60-174">V aplikaci Visual Studio klikněte pravým tlačítkem myši na složku **stránky** a vyberte možnost **Přidat** > **novou položku**  >  \*\* Razor komponenta\*\*.</span><span class="sxs-lookup"><span data-stu-id="8cd60-174">In Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="8cd60-175">Pojmenujte soubor komponenty *todo. Razor*.</span><span class="sxs-lookup"><span data-stu-id="8cd60-175">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="8cd60-176">V jiných vývojových prostředích přidejte prázdný soubor do složky **Pages** s názvem *todo. Razor*.</span><span class="sxs-lookup"><span data-stu-id="8cd60-176">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="8cd60-177">Zadejte počáteční označení pro komponentu:</span><span class="sxs-lookup"><span data-stu-id="8cd60-177">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="8cd60-178">Přidejte `Todo` komponentu do navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="8cd60-178">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="8cd60-179">`NavMenu` Komponenta (*Shared/NavMenu. Razor*) se používá v rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="8cd60-179">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="8cd60-180">Rozložení jsou komponenty, které umožňují vyhnout se duplikaci obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8cd60-180">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="8cd60-181">Přidejte `<NavLink>` element pro `Todo` komponentu přidáním následujícího označení položky seznamu pod existující položky seznamu v souboru *Shared/NavMenu. Razor* :</span><span class="sxs-lookup"><span data-stu-id="8cd60-181">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="8cd60-182">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8cd60-182">Rebuild and run the app.</span></span> <span data-ttu-id="8cd60-183">Přejděte na stránku Nová TODO a potvrďte, že odkaz na `Todo` komponentu funguje.</span><span class="sxs-lookup"><span data-stu-id="8cd60-183">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="8cd60-184">Do kořenového adresáře projektu přidejte soubor *TodoItem.cs* , který bude obsahovat třídu, která představuje položku todo.</span><span class="sxs-lookup"><span data-stu-id="8cd60-184">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="8cd60-185">Pro `TodoItem` třídu použijte následující kód jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="8cd60-185">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="8cd60-186">Vraťte se do `Todo` komponenty (*Pages/todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="8cd60-186">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="8cd60-187">Přidejte pole pro položky ToDo v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="8cd60-187">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="8cd60-188">`Todo` Komponenta používá toto pole k údržbě stavu seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="8cd60-188">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="8cd60-189">Přidejte neuspořádaný seznam značek a `foreach` smyčku pro vykreslení každé položky ToDo jako položky seznamu (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="8cd60-189">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="8cd60-190">Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu.</span><span class="sxs-lookup"><span data-stu-id="8cd60-190">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="8cd60-191">Přidejte textové zadání (`<input>`) a tlačítko (`<button>`) pod Neseřazený seznam (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="8cd60-191">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="8cd60-192">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8cd60-192">Rebuild and run the app.</span></span> <span data-ttu-id="8cd60-193">Když je vybráno tlačítko **Přidat TODO** , nic se nestane, protože obslužná rutina události není na tlačítko kabelem.</span><span class="sxs-lookup"><span data-stu-id="8cd60-193">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="8cd60-194">Přidejte do `AddTodo` `Todo` komponenty metodu a zaregistrujte ji pro výběry tlačítek pomocí `@onclick` atributu.</span><span class="sxs-lookup"><span data-stu-id="8cd60-194">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="8cd60-195">Metoda `AddTodo` jazyka C# je volána, když je vybráno tlačítko:</span><span class="sxs-lookup"><span data-stu-id="8cd60-195">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="8cd60-196">Chcete-li získat název nové položky ToDo, přidejte `newTodo` pole řetězce v horní části `@code` bloku a vytvořte jeho svázání s hodnotou textového zadání pomocí `bind` atributu v `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="8cd60-196">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="8cd60-197">Aktualizujte `AddTodo` metodu tak, aby `TodoItem` se do seznamu přidal název se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="8cd60-197">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="8cd60-198">Vymažte hodnotu textového zadání nastavením `newTodo` na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="8cd60-198">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="8cd60-199">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8cd60-199">Rebuild and run the app.</span></span> <span data-ttu-id="8cd60-200">Chcete-li otestovat nový kód, přidejte do seznamu TODO některé položky ToDo.</span><span class="sxs-lookup"><span data-stu-id="8cd60-200">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="8cd60-201">Text nadpisu pro každou položku TODO lze upravovat a zaškrtávací políčko může uživatelům pomáhat sledovat dokončené položky.</span><span class="sxs-lookup"><span data-stu-id="8cd60-201">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="8cd60-202">Přidejte vstup zaškrtávacího políčka pro každou položku TODO a navažte její hodnotu na `IsDone` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8cd60-202">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="8cd60-203">Změnit `@todo.Title` na `<input>` prvek vázaný na `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="8cd60-203">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="8cd60-204">Chcete-li ověřit, zda jsou tyto hodnoty vázány, aktualizujte `<h3>` záhlaví tak, aby zobrazovalo počet nedokončených položek`IsDone` TODO `false`(je).</span><span class="sxs-lookup"><span data-stu-id="8cd60-204">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="8cd60-205">Dokončená `Todo` součást (*Pages/todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="8cd60-205">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="8cd60-206">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8cd60-206">Rebuild and run the app.</span></span> <span data-ttu-id="8cd60-207">Přidejte položky ToDo pro otestování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="8cd60-207">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
