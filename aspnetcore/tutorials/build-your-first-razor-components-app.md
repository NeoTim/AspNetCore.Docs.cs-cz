---
title: Vytvořte svoji první aplikaci součásti syntaxe Razor
author: guardrex
description: Vytvoření podrobné Razor komponent aplikace a seznamte se základními koncepty součásti syntaxe Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2019
uid: tutorials/first-razor-components-app
ms.openlocfilehash: 2a987b3f2e687cd9d4dffa2c573c938e68ea3cc8
ms.sourcegitcommit: 7d6019f762fc5b8cbedcd69801e8310f51a17c18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419362"
---
# <a name="build-your-first-razor-components-app"></a><span data-ttu-id="49b94-103">Vytvořte svoji první aplikaci součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="49b94-103">Build your first Razor Components app</span></span>

<span data-ttu-id="49b94-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="49b94-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="49b94-105">V tomto kurzu se dozvíte, jak vytvářet aplikace s komponentami Razor a ukazuje základní koncepty součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="49b94-105">This tutorial shows you how to build an app with Razor Components and demonstrates basic Razor Components concepts.</span></span> <span data-ttu-id="49b94-106">Můžete využívat tento kurz pomocí obou součásti Razor na základě projektu (podporované v .NET Core 3.0 nebo novější) nebo pomocí aplikace project na základě Blazor (podporované v budoucích verzích .NET Core).</span><span class="sxs-lookup"><span data-stu-id="49b94-106">You can enjoy this tutorial using either a Razor Components-based project (supported in .NET Core 3.0 or later) or using a Blazor-based project (supported in a future release of .NET Core).</span></span>

<span data-ttu-id="49b94-107">Pro prostředí pomocí technologie ASP.NET Core Razor součásti (*doporučuje*):</span><span class="sxs-lookup"><span data-stu-id="49b94-107">For an experience using ASP.NET Core Razor Components (*recommended*):</span></span>

* <span data-ttu-id="49b94-108">Postupujte podle pokynů v <xref:razor-components/get-started> k vytvoření projektu založeného na součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="49b94-108">Follow the guidance in <xref:razor-components/get-started> to create a Razor Components-based project.</span></span>
* <span data-ttu-id="49b94-109">Pojmenujte projekt `RazorComponents`.</span><span class="sxs-lookup"><span data-stu-id="49b94-109">Name the project `RazorComponents`.</span></span>

<span data-ttu-id="49b94-110">Pro prostředí pomocí Blazor:</span><span class="sxs-lookup"><span data-stu-id="49b94-110">For an experience using Blazor:</span></span>

* <span data-ttu-id="49b94-111">Postupujte podle pokynů v <xref:spa/blazor/get-started> k vytvoření projektu založeného na Blazor.</span><span class="sxs-lookup"><span data-stu-id="49b94-111">Follow the guidance in <xref:spa/blazor/get-started> to create a Blazor-based project.</span></span>
* <span data-ttu-id="49b94-112">Pojmenujte projekt `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="49b94-112">Name the project `Blazor`.</span></span>

<span data-ttu-id="49b94-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="49b94-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="49b94-114">Naleznete v následujících tématech pro požadavky:</span><span class="sxs-lookup"><span data-stu-id="49b94-114">See the following topics for prerequisites:</span></span>

## <a name="build-components"></a><span data-ttu-id="49b94-115">Sestavení komponent</span><span class="sxs-lookup"><span data-stu-id="49b94-115">Build components</span></span>

1. <span data-ttu-id="49b94-116">Přejděte do všech tří stránek vaší aplikace v *součásti/stránky* složky (*stránky* v Blazor): Domů čítač a načíst data.</span><span class="sxs-lookup"><span data-stu-id="49b94-116">Browse to each of the app's three pages in the *Components/Pages* folder (*Pages* in Blazor): Home, Counter, and Fetch data.</span></span> <span data-ttu-id="49b94-117">Tyto stránky jsou implementovány v souborech Razor komponenty: *Index.Razor*, *Counter.razor*, a *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="49b94-117">These pages are implemented by Razor Component files: *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span> <span data-ttu-id="49b94-118">(Blazor dál používat *.cshtml* příponu souboru: *Index.cshtml*, *Counter.cshtml*, a *FetchData.cshtml*.)</span><span class="sxs-lookup"><span data-stu-id="49b94-118">(Blazor continues to use the *.cshtml* file extension: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.)</span></span>

1. <span data-ttu-id="49b94-119">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="49b94-119">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="49b94-120">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti Razor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="49b94-120">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

1. <span data-ttu-id="49b94-121">Vyzkoušení implementace čítač součástí *Counter.razor* souboru.</span><span class="sxs-lookup"><span data-stu-id="49b94-121">Examine the implementation of the Counter component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="49b94-122">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="49b94-122">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="49b94-123">Uživatelské rozhraní komponenty čítač je definován v jazyce HTML.</span><span class="sxs-lookup"><span data-stu-id="49b94-123">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="49b94-124">Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="49b94-124">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="49b94-125">Značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty v okamžiku sestavení.</span><span class="sxs-lookup"><span data-stu-id="49b94-125">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="49b94-126">Název generované třídy .NET odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="49b94-126">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="49b94-127">Členy třídy komponenty jsou definovány v `@functions` bloku.</span><span class="sxs-lookup"><span data-stu-id="49b94-127">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="49b94-128">V `@functions` blokovat, stav komponent (vlastnosti, pole) a metody jsou určené pro zpracování událostí nebo definování dalších součástí logiky.</span><span class="sxs-lookup"><span data-stu-id="49b94-128">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="49b94-129">Tyto členy, se použije jako součást logiky komponenty vykreslování a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="49b94-129">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="49b94-130">Když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="49b94-130">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="49b94-131">Součást čítače zaregistrované `onclick` obslužná rutina volána ( `IncrementCount` metoda).</span><span class="sxs-lookup"><span data-stu-id="49b94-131">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="49b94-132">Součást čítače obnoví jeho vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="49b94-132">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="49b94-133">Nový vykreslovací stromu je ve srovnání s předchozím histogramem.</span><span class="sxs-lookup"><span data-stu-id="49b94-133">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="49b94-134">Se použijí pouze změny do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="49b94-134">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="49b94-135">Počet zobrazených se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="49b94-135">The displayed count is updated.</span></span>

1. <span data-ttu-id="49b94-136">Upravit C# logiky součást čítače aby přírůstek počtu dvou místo jednoho.</span><span class="sxs-lookup"><span data-stu-id="49b94-136">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="49b94-137">Znovu sestavte a spusťte aplikaci, aby se změny projevily.</span><span class="sxs-lookup"><span data-stu-id="49b94-137">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="49b94-138">Vyberte **klikněte na mě** tlačítko a zvýší čítač ve dvou.</span><span class="sxs-lookup"><span data-stu-id="49b94-138">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="49b94-139">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="49b94-139">Use components</span></span>

<span data-ttu-id="49b94-140">Zahrnout součásti do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="49b94-140">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="49b94-141">Přidat součást čítače pro součást aplikace indexu (domů) tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="49b94-141">Add the Counter component to the app's Index (Home) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="49b94-142">Pokud používáte Blazor pro toto prostředí, průzkum výzvy součásti (`<SurveyPrompt>` element) je v komponentě indexu.</span><span class="sxs-lookup"><span data-stu-id="49b94-142">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="49b94-143">Nahraďte `<SurveyPrompt>` křížkem `<Counter>` elementu.</span><span class="sxs-lookup"><span data-stu-id="49b94-143">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="49b94-144">*Components/Pages/Index.razor* (*Pages/Index.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="49b94-144">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Index.razor?highlight=7)]

1. <span data-ttu-id="49b94-145">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49b94-145">Rebuild and run the app.</span></span> <span data-ttu-id="49b94-146">Domovská stránka obsahuje vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="49b94-146">The Home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="49b94-147">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="49b94-147">Component parameters</span></span>

<span data-ttu-id="49b94-148">Součástí mohou mít také parametry.</span><span class="sxs-lookup"><span data-stu-id="49b94-148">Components can also have parameters.</span></span> <span data-ttu-id="49b94-149">Parametry komponenty jsou definovány pomocí vlastnosti neveřejné na komponentní třída dekorován `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="49b94-149">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="49b94-150">Atributy můžete zadat argumenty pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="49b94-150">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="49b94-151">Aktualizace komponenty `@functions` C# kódu:</span><span class="sxs-lookup"><span data-stu-id="49b94-151">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="49b94-152">Přidat `IncrementAmount` vlastnost upravené pomocí `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="49b94-152">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="49b94-153">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="49b94-153">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="49b94-154">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="49b94-154">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Counter.razor?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="49b94-155">Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="49b94-155">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="49b94-156">Nastavte hodnotu čítače přírůstku deset.</span><span class="sxs-lookup"><span data-stu-id="49b94-156">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="49b94-157">*Components/Pages/Index.razor* (*Pages/Index.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="49b94-157">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Index.razor?highlight=7)]

1. <span data-ttu-id="49b94-158">Znovu načte na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="49b94-158">Reload the Home page.</span></span> <span data-ttu-id="49b94-159">Hodnota čítače se zvýší o hodnotu deset pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="49b94-159">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="49b94-160">Čítače na stránku zvýší čítač jednou.</span><span class="sxs-lookup"><span data-stu-id="49b94-160">The counter on the Counter page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="49b94-161">Směrovat do komponenty</span><span class="sxs-lookup"><span data-stu-id="49b94-161">Route to components</span></span>

<span data-ttu-id="49b94-162">`@page` Direktiv v horní části *Counter.razor* soubor Určuje, že tato součást je koncový bod směrování.</span><span class="sxs-lookup"><span data-stu-id="49b94-162">The `@page` directive at the top of the *Counter.razor* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="49b94-163">Součást čítače zpracovává požadavky odeslané na `/Counter`.</span><span class="sxs-lookup"><span data-stu-id="49b94-163">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="49b94-164">Bez `@page` direktiv, komponenta nebude zpracovávat směrování žádostí, ale součást je stále možné ostatními komponentami.</span><span class="sxs-lookup"><span data-stu-id="49b94-164">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="49b94-165">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="49b94-165">Dependency injection</span></span>

<span data-ttu-id="49b94-166">Služby zaregistrované v kontejneru aplikace služby jsou k dispozici na komponenty prostřednictvím [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="49b94-166">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="49b94-167">Vložit do součástí s použitím služby `@inject` směrnice.</span><span class="sxs-lookup"><span data-stu-id="49b94-167">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="49b94-168">Prozkoumejte direktivy FetchData součásti v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49b94-168">Examine the directives of the FetchData component in the sample app.</span></span>

<span data-ttu-id="49b94-169">V ukázkové aplikaci Razor komponenty `WeatherForecastService` není registrován jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes), takže jedna instance služby je k dispozici v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="49b94-169">In the Razor Components sample app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="49b94-170">`@inject` Směrnice slouží k vložení instance `WeatherForecastService` služby do komponenty.</span><span class="sxs-lookup"><span data-stu-id="49b94-170">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="49b94-171">*Components/Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="49b94-171">*Components/Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="49b94-172">Komponenta FetchData používá vložený služby jako `ForecastService`, k načtení pole `WeatherForecast` objekty:</span><span class="sxs-lookup"><span data-stu-id="49b94-172">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="49b94-173">Ve verzi Blazor ukázkové aplikace `HttpClient` se vloží získávat data předpovědi počasí z *weather.json* soubor *wwwroot/ukázková data* složky:</span><span class="sxs-lookup"><span data-stu-id="49b94-173">In the Blazor version of the sample app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="49b94-174">*Pages/FetchData.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="49b94-174">*Pages/FetchData.cshtml*:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=7)]

<span data-ttu-id="49b94-175">V obou ukázkové aplikace [ @foreach ](/dotnet/csharp/language-reference/keywords/foreach-in) smyčky se použije k vykreslení každou prognózy instanci jako řadu data o počasí v tabulce:</span><span class="sxs-lookup"><span data-stu-id="49b94-175">In both sample apps, a [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="49b94-176">Vytvoření seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="49b94-176">Build a todo list</span></span>

<span data-ttu-id="49b94-177">Přidáte novou součást aplikaci, která implementuje seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="49b94-177">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="49b94-178">Přidáte prázdný soubor na ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="49b94-178">Add an empty file to the sample app:</span></span>

   * <span data-ttu-id="49b94-179">Pro prostředí Razor součásti přidat *Todo.razor* do souboru *součásti/stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="49b94-179">For the Razor Components experience, add a *Todo.razor* file to the *Components/Pages* folder.</span></span>
   * <span data-ttu-id="49b94-180">Prostředí Blazor přidat *Todo.cshtml* do souboru *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="49b94-180">For the Blazor experience, add a *Todo.cshtml* file to the *Pages* folder.</span></span>

1. <span data-ttu-id="49b94-181">Zadejte počáteční značka pro komponentu:</span><span class="sxs-lookup"><span data-stu-id="49b94-181">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="49b94-182">Přidáte součást Todo do navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="49b94-182">Add the Todo component to the navigation bar.</span></span>

   <span data-ttu-id="49b94-183">Komponenta NavMenu (*Components/Shared/NavMenu.razor* nebo *Shared/NavMenu.cshtml* v Blazor) se používá v rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="49b94-183">The NavMenu component (*Components/Shared/NavMenu.razor* or *Shared/NavMenu.cshtml* in Blazor) is used in the app's layout.</span></span> <span data-ttu-id="49b94-184">Rozložení jsou komponenty, které umožňují, aby se zabránilo duplicitě obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49b94-184">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="49b94-185">Další informace naleznete v tématu <xref:razor-components/layouts>.</span><span class="sxs-lookup"><span data-stu-id="49b94-185">For more information, see <xref:razor-components/layouts>.</span></span>

   <span data-ttu-id="49b94-186">Přidat `<NavLink>` pro komponentu Todo tak, že přidáte následující značky položky seznamu níže existující položky seznamu v *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* v Blazor) souboru:</span><span class="sxs-lookup"><span data-stu-id="49b94-186">Add a `<NavLink>` for the Todo component by adding the following list item markup below the existing list items in the *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* in Blazor) file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="49b94-187">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49b94-187">Rebuild and run the app.</span></span> <span data-ttu-id="49b94-188">Na stránce Nový Todo potvrďte, že odkaz na komponentu Todo funguje.</span><span class="sxs-lookup"><span data-stu-id="49b94-188">Visit the new Todo page to confirm that the link to the Todo component works.</span></span>

1. <span data-ttu-id="49b94-189">Přidat *TodoItem.cs* souboru do kořenového adresáře projektu k uložení třídu, která představuje položku seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="49b94-189">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="49b94-190">Pomocí následujících C# kód `TodoItem` třídy:</span><span class="sxs-lookup"><span data-stu-id="49b94-190">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/TodoItem.cs)]

1. <span data-ttu-id="49b94-191">Vraťte se do komponenty Todo (*Components/Pages/Todo.razor* nebo *Pages/Todo.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="49b94-191">Return to the Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   * <span data-ttu-id="49b94-192">Přidání pole pro úloh, ať už v `@functions` bloku.</span><span class="sxs-lookup"><span data-stu-id="49b94-192">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="49b94-193">Todo součásti používá toto pole pro uchování stavu pro seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="49b94-193">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="49b94-194">Přidat neuspořádaný seznam značek a `foreach` smyčky k vykreslení každé položky todo jako položku seznamu.</span><span class="sxs-lookup"><span data-stu-id="49b94-194">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="49b94-195">Aplikace vyžaduje pro přidání do seznamu úloh, ať už prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="49b94-195">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="49b94-196">Přidání textového zadání a tlačítka v seznamu níže:</span><span class="sxs-lookup"><span data-stu-id="49b94-196">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="49b94-197">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49b94-197">Rebuild and run the app.</span></span> <span data-ttu-id="49b94-198">Když **přidat todo** se vybere tlačítko, nic se nestane, protože obslužná rutina události není svázanou tlačítka.</span><span class="sxs-lookup"><span data-stu-id="49b94-198">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="49b94-199">Přidat `AddTodo` metodu pro součást Todo a zaregistrujte ho pro tlačítko klikne pomocí `onclick` atribut:</span><span class="sxs-lookup"><span data-stu-id="49b94-199">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   <span data-ttu-id="49b94-200">`AddTodo` C# Metoda je volána při výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="49b94-200">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="49b94-201">Pokud chcete získat název nové položky seznamu úkolů, přidejte `newTodo` řetězec pole a navázat jej na hodnotu text input pomocí `bind` atribut:</span><span class="sxs-lookup"><span data-stu-id="49b94-201">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo" />
   ```

1. <span data-ttu-id="49b94-202">Aktualizace `AddTodo` způsob, jak přidat `TodoItem` se zadaným názvem do seznamu.</span><span class="sxs-lookup"><span data-stu-id="49b94-202">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="49b94-203">Smazat hodnotu textového zadání tak, že nastavíte `newTodo` na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="49b94-203">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="49b94-204">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49b94-204">Rebuild and run the app.</span></span> <span data-ttu-id="49b94-205">Přidání některých úloh, ať už do seznamu k testování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="49b94-205">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="49b94-206">Text nadpisu pro každou položku seznamu úkolů lze upravovat a zaškrtávací políčko může pomoci udržovat přehled o dokončené položky uživatele.</span><span class="sxs-lookup"><span data-stu-id="49b94-206">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="49b94-207">Přidat vstup zaškrtněte políčko u každé položky todo a její hodnotu na vytvoření vazby `IsDone` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="49b94-207">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="49b94-208">Změna `@todo.Title` do `<input>` prvek vázán na `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="49b94-208">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="49b94-209">Chcete-li ověřit, že tyto hodnoty jsou vázány, aktualizujte `<h1>` záhlaví zobrazíte počet nesplněné položky seznamu úkolů nejsou kompletní (`IsDone` je `false`).</span><span class="sxs-lookup"><span data-stu-id="49b94-209">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="49b94-210">Dokončené komponenty Todo (*Components/Pages/Todo.razor* nebo *Pages/Todo.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="49b94-210">The completed Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Todo.razor)]

1. <span data-ttu-id="49b94-211">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49b94-211">Rebuild and run the app.</span></span> <span data-ttu-id="49b94-212">Přidání položky seznamu úkolů k testování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="49b94-212">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="49b94-213">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="49b94-213">Publish and deploy the app</span></span>

<span data-ttu-id="49b94-214">Publikování aplikace, najdete v článku <xref:host-and-deploy/razor-components/index#publish-the-app>.</span><span class="sxs-lookup"><span data-stu-id="49b94-214">To publish the app, see <xref:host-and-deploy/razor-components/index#publish-the-app>.</span></span>
