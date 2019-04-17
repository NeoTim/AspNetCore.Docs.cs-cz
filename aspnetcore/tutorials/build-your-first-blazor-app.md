---
title: Vytvořte svoji první aplikaci Blazor
author: guardrex
description: Vytvoření podrobné Blazor aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: 310eb211f68076d6f52d6427940e07736d833e5b
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614813"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="b7e60-103">Vytvořte svoji první aplikaci Blazor</span><span class="sxs-lookup"><span data-stu-id="b7e60-103">Build your first Blazor app</span></span>

<span data-ttu-id="b7e60-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b7e60-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="b7e60-105">V tomto kurzu se dozvíte, jak vytvářet aplikace pomocí syntaxe Razor komponenty na straně serveru nebo Blazor na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="b7e60-105">This tutorial shows you how to build an app with server-side Razor Components or client-side Blazor.</span></span>

<span data-ttu-id="b7e60-106">Pro prostředí pomocí technologie ASP.NET Core Razor součástmi (*doporučuje*):</span><span class="sxs-lookup"><span data-stu-id="b7e60-106">For an experience using server-side ASP.NET Core Razor Components (*recommended*):</span></span>

* <span data-ttu-id="b7e60-107">Postupujte podle *prostředí Razor komponenty* podle pokynů uvedených v <xref:blazor/get-started#server-side-razor-components-experience> k vytvoření projektu založeného na součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="b7e60-107">Follow the *Razor Components experience* guidance in <xref:blazor/get-started#server-side-razor-components-experience> to create a Razor Components-based project.</span></span>
* <span data-ttu-id="b7e60-108">Pojmenujte projekt `RazorComponents`.</span><span class="sxs-lookup"><span data-stu-id="b7e60-108">Name the project `RazorComponents`.</span></span>

<span data-ttu-id="b7e60-109">Pro prostředí pomocí Blazor:</span><span class="sxs-lookup"><span data-stu-id="b7e60-109">For an experience using Blazor:</span></span>

* <span data-ttu-id="b7e60-110">Postupujte podle *Blazor prostředí* podle pokynů uvedených v <xref:blazor/get-started#client-side-blazor-experience> k vytvoření projektu založeného na Blazor.</span><span class="sxs-lookup"><span data-stu-id="b7e60-110">Follow the *Blazor experience* guidance in <xref:blazor/get-started#client-side-blazor-experience> to create a Blazor-based project.</span></span>
* <span data-ttu-id="b7e60-111">Pojmenujte projekt `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="b7e60-111">Name the project `Blazor`.</span></span>

<span data-ttu-id="b7e60-112">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b7e60-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="b7e60-113">Naleznete v následujících tématech pro požadavky:</span><span class="sxs-lookup"><span data-stu-id="b7e60-113">See the following topics for prerequisites:</span></span>

## <a name="build-components"></a><span data-ttu-id="b7e60-114">Sestavení komponent</span><span class="sxs-lookup"><span data-stu-id="b7e60-114">Build components</span></span>

1. <span data-ttu-id="b7e60-115">Přejděte do všech tří stránek vaší aplikace v *součásti/stránky* složky (*stránky* v Blazor): Domů čítač a načíst data.</span><span class="sxs-lookup"><span data-stu-id="b7e60-115">Browse to each of the app's three pages in the *Components/Pages* folder (*Pages* in Blazor): Home, Counter, and Fetch data.</span></span> <span data-ttu-id="b7e60-116">Tyto stránky jsou implementovány v souborech Razor komponenty: *Index.Razor*, *Counter.razor*, a *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="b7e60-116">These pages are implemented by Razor Component files: *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span> <span data-ttu-id="b7e60-117">(Blazor dál používat *.cshtml* příponu souboru: *Index.cshtml*, *Counter.cshtml*, a *FetchData.cshtml*.)</span><span class="sxs-lookup"><span data-stu-id="b7e60-117">(Blazor continues to use the *.cshtml* file extension: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.)</span></span>

1. <span data-ttu-id="b7e60-118">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="b7e60-118">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="b7e60-119">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale Blazor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="b7e60-119">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="b7e60-120">Vyzkoušení implementace čítač součástí *Counter.razor* souboru.</span><span class="sxs-lookup"><span data-stu-id="b7e60-120">Examine the implementation of the Counter component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="b7e60-121">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="b7e60-121">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="b7e60-122">Uživatelské rozhraní komponenty čítač je definován v jazyce HTML.</span><span class="sxs-lookup"><span data-stu-id="b7e60-122">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="b7e60-123">Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="b7e60-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="b7e60-124">Značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty v okamžiku sestavení.</span><span class="sxs-lookup"><span data-stu-id="b7e60-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="b7e60-125">Název generované třídy .NET odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="b7e60-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="b7e60-126">Členy třídy komponenty jsou definovány v `@functions` bloku.</span><span class="sxs-lookup"><span data-stu-id="b7e60-126">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="b7e60-127">V `@functions` blokovat, stav komponent (vlastnosti, pole) a metody jsou určené pro zpracování událostí nebo definování dalších součástí logiky.</span><span class="sxs-lookup"><span data-stu-id="b7e60-127">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="b7e60-128">Tyto členy, se použije jako součást logiky komponenty vykreslování a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="b7e60-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="b7e60-129">Když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="b7e60-129">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="b7e60-130">Součást čítače zaregistrované `onclick` obslužná rutina volána ( `IncrementCount` metoda).</span><span class="sxs-lookup"><span data-stu-id="b7e60-130">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="b7e60-131">Součást čítače obnoví jeho vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="b7e60-131">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="b7e60-132">Nový vykreslovací stromu je ve srovnání s předchozím histogramem.</span><span class="sxs-lookup"><span data-stu-id="b7e60-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="b7e60-133">Se použijí pouze změny do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="b7e60-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="b7e60-134">Počet zobrazených se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="b7e60-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="b7e60-135">Upravit C# logiky součást čítače aby přírůstek počtu dvou místo jednoho.</span><span class="sxs-lookup"><span data-stu-id="b7e60-135">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="b7e60-136">Znovu sestavte a spusťte aplikaci, aby se změny projevily.</span><span class="sxs-lookup"><span data-stu-id="b7e60-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="b7e60-137">Vyberte **klikněte na mě** tlačítko a zvýší čítač ve dvou.</span><span class="sxs-lookup"><span data-stu-id="b7e60-137">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="b7e60-138">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="b7e60-138">Use components</span></span>

<span data-ttu-id="b7e60-139">Zahrnout součásti do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="b7e60-139">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="b7e60-140">Přidat součást čítače pro součást aplikace indexu (domů) tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="b7e60-140">Add the Counter component to the app's Index (Home) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="b7e60-141">Pokud používáte Blazor pro toto prostředí, průzkum výzvy součásti (`<SurveyPrompt>` element) je v komponentě indexu.</span><span class="sxs-lookup"><span data-stu-id="b7e60-141">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="b7e60-142">Nahraďte `<SurveyPrompt>` křížkem `<Counter>` elementu.</span><span class="sxs-lookup"><span data-stu-id="b7e60-142">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="b7e60-143">*Components/Pages/Index.razor* (*Pages/Index.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="b7e60-143">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index.razor?highlight=7)]

1. <span data-ttu-id="b7e60-144">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7e60-144">Rebuild and run the app.</span></span> <span data-ttu-id="b7e60-145">Domovská stránka obsahuje vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="b7e60-145">The Home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="b7e60-146">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="b7e60-146">Component parameters</span></span>

<span data-ttu-id="b7e60-147">Součástí mohou mít také parametry.</span><span class="sxs-lookup"><span data-stu-id="b7e60-147">Components can also have parameters.</span></span> <span data-ttu-id="b7e60-148">Parametry komponenty jsou definovány pomocí vlastnosti neveřejné na komponentní třída dekorován `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="b7e60-148">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="b7e60-149">Atributy můžete zadat argumenty pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="b7e60-149">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="b7e60-150">Aktualizace komponenty `@functions` C# kódu:</span><span class="sxs-lookup"><span data-stu-id="b7e60-150">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="b7e60-151">Přidat `IncrementAmount` vlastnost upravené pomocí `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="b7e60-151">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="b7e60-152">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="b7e60-152">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="b7e60-153">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="b7e60-153">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Counter.razor?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="b7e60-154">Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="b7e60-154">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="b7e60-155">Nastavte hodnotu čítače přírůstku deset.</span><span class="sxs-lookup"><span data-stu-id="b7e60-155">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="b7e60-156">*Components/Pages/Index.razor* (*Pages/Index.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="b7e60-156">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Index.razor?highlight=7)]

1. <span data-ttu-id="b7e60-157">Znovu načte na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="b7e60-157">Reload the Home page.</span></span> <span data-ttu-id="b7e60-158">Hodnota čítače se zvýší o hodnotu deset pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="b7e60-158">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="b7e60-159">Čítače na stránku zvýší čítač jednou.</span><span class="sxs-lookup"><span data-stu-id="b7e60-159">The counter on the Counter page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="b7e60-160">Směrovat do komponenty</span><span class="sxs-lookup"><span data-stu-id="b7e60-160">Route to components</span></span>

<span data-ttu-id="b7e60-161">`@page` Direktiv v horní části *Counter.razor* soubor Určuje, že tato součást je koncový bod směrování.</span><span class="sxs-lookup"><span data-stu-id="b7e60-161">The `@page` directive at the top of the *Counter.razor* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="b7e60-162">Součást čítače zpracovává požadavky odeslané na `/Counter`.</span><span class="sxs-lookup"><span data-stu-id="b7e60-162">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="b7e60-163">Bez `@page` direktiv, komponenta nebude zpracovávat směrování žádostí, ale součást je stále možné ostatními komponentami.</span><span class="sxs-lookup"><span data-stu-id="b7e60-163">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="b7e60-164">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="b7e60-164">Dependency injection</span></span>

<span data-ttu-id="b7e60-165">Služby zaregistrované v kontejneru aplikace služby jsou k dispozici na komponenty prostřednictvím [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b7e60-165">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b7e60-166">Vložit do součástí s použitím služby `@inject` směrnice.</span><span class="sxs-lookup"><span data-stu-id="b7e60-166">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="b7e60-167">Prozkoumejte direktivy FetchData součásti v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7e60-167">Examine the directives of the FetchData component in the sample app.</span></span>

<span data-ttu-id="b7e60-168">V ukázkové aplikaci Razor komponenty `WeatherForecastService` není registrován jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes), takže jedna instance služby je k dispozici v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7e60-168">In the Razor Components sample app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="b7e60-169">`@inject` Směrnice slouží k vložení instance `WeatherForecastService` služby do komponenty.</span><span class="sxs-lookup"><span data-stu-id="b7e60-169">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="b7e60-170">*Components/Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="b7e60-170">*Components/Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="b7e60-171">Komponenta FetchData používá vložený služby jako `ForecastService`, k načtení pole `WeatherForecast` objekty:</span><span class="sxs-lookup"><span data-stu-id="b7e60-171">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="b7e60-172">Ve verzi Blazor ukázkové aplikace `HttpClient` se vloží získávat data předpovědi počasí z *weather.json* soubor *wwwroot/ukázková data* složky:</span><span class="sxs-lookup"><span data-stu-id="b7e60-172">In the Blazor version of the sample app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="b7e60-173">*Pages/FetchData.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b7e60-173">*Pages/FetchData.cshtml*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=7)]

<span data-ttu-id="b7e60-174">V obou ukázkové aplikace [ @foreach ](/dotnet/csharp/language-reference/keywords/foreach-in) smyčky se použije k vykreslení každou prognózy instanci jako řadu data o počasí v tabulce:</span><span class="sxs-lookup"><span data-stu-id="b7e60-174">In both sample apps, a [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="b7e60-175">Vytvoření seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="b7e60-175">Build a todo list</span></span>

<span data-ttu-id="b7e60-176">Přidáte novou součást aplikaci, která implementuje seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="b7e60-176">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="b7e60-177">Přidáte prázdný soubor na ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="b7e60-177">Add an empty file to the sample app:</span></span>

   * <span data-ttu-id="b7e60-178">Pro prostředí Razor součásti přidat *Todo.razor* do souboru *součásti/stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="b7e60-178">For the Razor Components experience, add a *Todo.razor* file to the *Components/Pages* folder.</span></span>
   * <span data-ttu-id="b7e60-179">Prostředí Blazor přidat *Todo.cshtml* do souboru *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="b7e60-179">For the Blazor experience, add a *Todo.cshtml* file to the *Pages* folder.</span></span>

1. <span data-ttu-id="b7e60-180">Zadejte počáteční značka pro komponentu:</span><span class="sxs-lookup"><span data-stu-id="b7e60-180">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="b7e60-181">Přidáte součást Todo do navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="b7e60-181">Add the Todo component to the navigation bar.</span></span>

   <span data-ttu-id="b7e60-182">Komponenta NavMenu (*Components/Shared/NavMenu.razor* nebo *Shared/NavMenu.cshtml* v Blazor) se používá v rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7e60-182">The NavMenu component (*Components/Shared/NavMenu.razor* or *Shared/NavMenu.cshtml* in Blazor) is used in the app's layout.</span></span> <span data-ttu-id="b7e60-183">Rozložení jsou komponenty, které umožňují, aby se zabránilo duplicitě obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7e60-183">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="b7e60-184">Další informace naleznete v tématu <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="b7e60-184">For more information, see <xref:blazor/layouts>.</span></span>

   <span data-ttu-id="b7e60-185">Přidat `<NavLink>` pro komponentu Todo tak, že přidáte následující značky položky seznamu níže existující položky seznamu v *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* v Blazor) souboru:</span><span class="sxs-lookup"><span data-stu-id="b7e60-185">Add a `<NavLink>` for the Todo component by adding the following list item markup below the existing list items in the *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* in Blazor) file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="b7e60-186">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7e60-186">Rebuild and run the app.</span></span> <span data-ttu-id="b7e60-187">Na stránce Nový Todo potvrďte, že odkaz na komponentu Todo funguje.</span><span class="sxs-lookup"><span data-stu-id="b7e60-187">Visit the new Todo page to confirm that the link to the Todo component works.</span></span>

1. <span data-ttu-id="b7e60-188">Přidat *TodoItem.cs* souboru do kořenového adresáře projektu k uložení třídu, která představuje položku seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="b7e60-188">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="b7e60-189">Pomocí následujících C# kód `TodoItem` třídy:</span><span class="sxs-lookup"><span data-stu-id="b7e60-189">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/TodoItem.cs)]

1. <span data-ttu-id="b7e60-190">Vraťte se do komponenty Todo (*Components/Pages/Todo.razor* nebo *Pages/Todo.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="b7e60-190">Return to the Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   * <span data-ttu-id="b7e60-191">Přidání pole pro úloh, ať už v `@functions` bloku.</span><span class="sxs-lookup"><span data-stu-id="b7e60-191">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="b7e60-192">Todo součásti používá toto pole pro uchování stavu pro seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="b7e60-192">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="b7e60-193">Přidat neuspořádaný seznam značek a `foreach` smyčky k vykreslení každé položky todo jako položku seznamu.</span><span class="sxs-lookup"><span data-stu-id="b7e60-193">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="b7e60-194">Aplikace vyžaduje pro přidání do seznamu úloh, ať už prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b7e60-194">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="b7e60-195">Přidání textového zadání a tlačítka v seznamu níže:</span><span class="sxs-lookup"><span data-stu-id="b7e60-195">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="b7e60-196">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7e60-196">Rebuild and run the app.</span></span> <span data-ttu-id="b7e60-197">Když **přidat todo** se vybere tlačítko, nic se nestane, protože obslužná rutina události není svázanou tlačítka.</span><span class="sxs-lookup"><span data-stu-id="b7e60-197">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="b7e60-198">Přidat `AddTodo` metodu pro součást Todo a zaregistrujte ho pro tlačítko klikne pomocí `onclick` atribut:</span><span class="sxs-lookup"><span data-stu-id="b7e60-198">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   <span data-ttu-id="b7e60-199">`AddTodo` C# Metoda je volána při výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="b7e60-199">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="b7e60-200">Pokud chcete získat název nové položky seznamu úkolů, přidejte `newTodo` řetězec pole a navázat jej na hodnotu text input pomocí `bind` atribut:</span><span class="sxs-lookup"><span data-stu-id="b7e60-200">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. <span data-ttu-id="b7e60-201">Aktualizace `AddTodo` způsob, jak přidat `TodoItem` se zadaným názvem do seznamu.</span><span class="sxs-lookup"><span data-stu-id="b7e60-201">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="b7e60-202">Smazat hodnotu textového zadání tak, že nastavíte `newTodo` na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="b7e60-202">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="b7e60-203">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7e60-203">Rebuild and run the app.</span></span> <span data-ttu-id="b7e60-204">Přidání některých úloh, ať už do seznamu k testování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="b7e60-204">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="b7e60-205">Text nadpisu pro každou položku seznamu úkolů lze upravovat a zaškrtávací políčko může pomoci udržovat přehled o dokončené položky uživatele.</span><span class="sxs-lookup"><span data-stu-id="b7e60-205">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="b7e60-206">Přidat vstup zaškrtněte políčko u každé položky todo a její hodnotu na vytvoření vazby `IsDone` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="b7e60-206">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="b7e60-207">Změna `@todo.Title` do `<input>` prvek vázán na `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="b7e60-207">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="b7e60-208">Chcete-li ověřit, že tyto hodnoty jsou vázány, aktualizujte `<h1>` záhlaví zobrazíte počet nesplněné položky seznamu úkolů nejsou kompletní (`IsDone` je `false`).</span><span class="sxs-lookup"><span data-stu-id="b7e60-208">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="b7e60-209">Dokončené komponenty Todo (*Components/Pages/Todo.razor* nebo *Pages/Todo.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="b7e60-209">The completed Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Todo.razor)]

1. <span data-ttu-id="b7e60-210">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7e60-210">Rebuild and run the app.</span></span> <span data-ttu-id="b7e60-211">Přidání položky seznamu úkolů k testování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="b7e60-211">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="b7e60-212">Publikování a nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="b7e60-212">Publish and deploy the app</span></span>

<span data-ttu-id="b7e60-213">Publikování aplikace, najdete v článku <xref:host-and-deploy/blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="b7e60-213">To publish the app, see <xref:host-and-deploy/blazor/index>.</span></span>
