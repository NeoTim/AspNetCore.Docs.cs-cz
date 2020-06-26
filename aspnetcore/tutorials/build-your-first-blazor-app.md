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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: f791dae5915c87d4c36f23419961e3c53e888743
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409069"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="28aca-103">Sestavení první Blazor aplikace</span><span class="sxs-lookup"><span data-stu-id="28aca-103">Build your first Blazor app</span></span>

<span data-ttu-id="28aca-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="28aca-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="28aca-105">V tomto kurzu se dozvíte, jak vytvořit a upravit Blazor aplikaci.</span><span class="sxs-lookup"><span data-stu-id="28aca-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="28aca-106">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="28aca-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="28aca-107">Vytvoření Blazor projektu aplikace seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="28aca-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="28aca-108">Upravit Razor součásti</span><span class="sxs-lookup"><span data-stu-id="28aca-108">Modify Razor components</span></span>
> * <span data-ttu-id="28aca-109">Použití zpracování událostí a datové vazby v součástech</span><span class="sxs-lookup"><span data-stu-id="28aca-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="28aca-110">Použití vkládání závislostí (DI) a směrování v Blazor aplikaci</span><span class="sxs-lookup"><span data-stu-id="28aca-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="28aca-111">Na konci tohoto kurzu budete mít funkční aplikaci seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="28aca-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="28aca-112">Komponenty sestavení</span><span class="sxs-lookup"><span data-stu-id="28aca-112">Build components</span></span>

1. <span data-ttu-id="28aca-113">Pokud <xref:blazor/get-started> chcete vytvořit Blazor projekt pro tento kurz, postupujte podle pokynů v článku.</span><span class="sxs-lookup"><span data-stu-id="28aca-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="28aca-114">Pojmenujte projekt `ToDoList` .</span><span class="sxs-lookup"><span data-stu-id="28aca-114">Name the project `ToDoList`.</span></span>

1. <span data-ttu-id="28aca-115">Ve složce přejděte na každou ze tří stránek aplikace `Pages` : `Home` , `Counter` , a `Fetch data` .</span><span class="sxs-lookup"><span data-stu-id="28aca-115">Browse to each of the app's three pages in the `Pages` folder: `Home`, `Counter`, and `Fetch data`.</span></span> <span data-ttu-id="28aca-116">Tyto stránky jsou implementovány pomocí Razor souborů komponenty `Index.razor` , `Counter.razor` a `FetchData.razor` .</span><span class="sxs-lookup"><span data-stu-id="28aca-116">These pages are implemented by the Razor component files `Index.razor`, `Counter.razor`, and `FetchData.razor`.</span></span>

1. <span data-ttu-id="28aca-117">Na `Counter` stránce vyberte tlačítko pro zvýšení čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="28aca-117">On the `Counter` page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="28aca-118">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="28aca-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="28aca-119">Pomocí Blazor můžete místo toho napsat C#.</span><span class="sxs-lookup"><span data-stu-id="28aca-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="28aca-120">Prověřte implementaci `Counter` komponenty v `Counter.razor` souboru.</span><span class="sxs-lookup"><span data-stu-id="28aca-120">Examine the implementation of the `Counter` component in the `Counter.razor` file.</span></span>

   <span data-ttu-id="28aca-121">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="28aca-121">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="28aca-122">Uživatelské rozhraní `Counter` komponenty je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="28aca-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="28aca-123">Dynamická logika vykreslování (například cykly, podmíněné výrazy, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="28aca-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="28aca-124">Značky HTML značek a vykreslování v jazyce C# jsou převedeny na třídu komponenty v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="28aca-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="28aca-125">Název generované třídy .NET se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="28aca-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="28aca-126">Členy třídy komponenty jsou definovány v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="28aca-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="28aca-127">V `@code` bloku jsou pro zpracování událostí nebo pro definování jiné logiky komponent určeny stav součásti (vlastnosti, pole) a metody.</span><span class="sxs-lookup"><span data-stu-id="28aca-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="28aca-128">Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="28aca-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="28aca-129">Když je vybráno tlačítko přírůstek čítače:</span><span class="sxs-lookup"><span data-stu-id="28aca-129">When the counter increment button is selected:</span></span>

   * <span data-ttu-id="28aca-130">`Counter`Registrovaná `onclick` obslužná rutina komponenty je volána ( `IncrementCount` Metoda).</span><span class="sxs-lookup"><span data-stu-id="28aca-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="28aca-131">`Counter`Komponenta znovu vygeneruje svůj strom vykreslování.</span><span class="sxs-lookup"><span data-stu-id="28aca-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="28aca-132">Nový strom vykreslování je porovnán s předchozím.</span><span class="sxs-lookup"><span data-stu-id="28aca-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="28aca-133">Jsou aplikovány pouze změny model DOM (Document Object Model) (DOM).</span><span class="sxs-lookup"><span data-stu-id="28aca-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="28aca-134">Zobrazený počet je aktualizovaný.</span><span class="sxs-lookup"><span data-stu-id="28aca-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="28aca-135">Upravte logiku komponenty jazyka C# `Counter` tak, aby byl přírůstek počtu vynásoben dvěma místomi jednoho.</span><span class="sxs-lookup"><span data-stu-id="28aca-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="28aca-136">Pokud chcete zobrazit změny, sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="28aca-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="28aca-137">Vyberte tlačítko.</span><span class="sxs-lookup"><span data-stu-id="28aca-137">Select the button.</span></span> <span data-ttu-id="28aca-138">Čítače se zvýší o dva.</span><span class="sxs-lookup"><span data-stu-id="28aca-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="28aca-139">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="28aca-139">Use components</span></span>

<span data-ttu-id="28aca-140">Zahrnutí komponenty do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="28aca-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="28aca-141">Přidejte `Counter` komponentu do `Index` komponenty aplikace přidáním `<Counter />` elementu do `Index` součásti ( `Index.razor` ).</span><span class="sxs-lookup"><span data-stu-id="28aca-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (`Index.razor`).</span></span>

   <span data-ttu-id="28aca-142">Pokud používáte Blazor WebAssembly pro toto prostředí, `SurveyPrompt` Komponenta je používána komponentou `Index` .</span><span class="sxs-lookup"><span data-stu-id="28aca-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="28aca-143">Nahraďte `<SurveyPrompt>` element elementem `<Counter />` .</span><span class="sxs-lookup"><span data-stu-id="28aca-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="28aca-144">Pokud Blazor Server pro toto prostředí používáte aplikaci, přidejte `<Counter />` element do `Index` komponenty:</span><span class="sxs-lookup"><span data-stu-id="28aca-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="28aca-145">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="28aca-145">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="28aca-146">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="28aca-146">Rebuild and run the app.</span></span> <span data-ttu-id="28aca-147">`Index`Komponenta má vlastní čítač.</span><span class="sxs-lookup"><span data-stu-id="28aca-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="28aca-148">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="28aca-148">Component parameters</span></span>

<span data-ttu-id="28aca-149">Komponenty mohou mít také parametry.</span><span class="sxs-lookup"><span data-stu-id="28aca-149">Components can also have parameters.</span></span> <span data-ttu-id="28aca-150">Parametry komponenty jsou definovány pomocí veřejných vlastností třídy Component s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="28aca-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="28aca-151">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="28aca-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="28aca-152">Aktualizujte `@code` kód C# komponenty následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="28aca-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="28aca-153">Přidejte veřejnou `IncrementAmount` vlastnost s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="28aca-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="28aca-154">Změňte `IncrementCount` metodu na použití `IncrementAmount` vlastnosti při zvyšování hodnoty `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="28aca-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="28aca-155">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="28aca-155">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="28aca-156">Zadejte `IncrementAmount` parametr v `Index` `<Counter>` elementu komponenty pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="28aca-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="28aca-157">Nastavte hodnotu pro zvýšení čítače o deset.</span><span class="sxs-lookup"><span data-stu-id="28aca-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="28aca-158">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="28aca-158">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="28aca-159">Znovu načtěte `Index` komponentu.</span><span class="sxs-lookup"><span data-stu-id="28aca-159">Reload the `Index` component.</span></span> <span data-ttu-id="28aca-160">Čítač se zvýší o deset pokaždé, když je vybráno tlačítko.</span><span class="sxs-lookup"><span data-stu-id="28aca-160">The counter increments by ten each time the button is selected.</span></span> <span data-ttu-id="28aca-161">Čítač v součásti se `Counter` stále zvyšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="28aca-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="28aca-162">Směrování na součásti</span><span class="sxs-lookup"><span data-stu-id="28aca-162">Route to components</span></span>

<span data-ttu-id="28aca-163">`@page`Direktiva v horní části `Counter.razor` souboru určuje, že `Counter` součást je koncový bod směrování.</span><span class="sxs-lookup"><span data-stu-id="28aca-163">The `@page` directive at the top of the `Counter.razor` file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="28aca-164">`Counter`Komponenta zpracovává požadavky odeslané na `/counter` .</span><span class="sxs-lookup"><span data-stu-id="28aca-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="28aca-165">Bez `@page` direktivy nezpracovávají součásti směrované požadavky, ale komponentu mohou i nadále používat jiné komponenty.</span><span class="sxs-lookup"><span data-stu-id="28aca-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="28aca-166">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="28aca-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor Server<span data-ttu-id="28aca-167">Využij</span><span class="sxs-lookup"><span data-stu-id="28aca-167"> experience</span></span>

<span data-ttu-id="28aca-168">Při práci s Blazor Server aplikací `WeatherForecastService` se služba zaregistruje jako typ [singleton](xref:fundamentals/dependency-injection#service-lifetimes) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="28aca-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="28aca-169">Instance služby je k dispozici v celé aplikaci prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="28aca-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="28aca-170">[`@inject`](xref:mvc/views/razor#inject)Direktiva slouží k vložení instance `WeatherForecastService` služby do `FetchData` komponenty.</span><span class="sxs-lookup"><span data-stu-id="28aca-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="28aca-171">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="28aca-171">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="28aca-172">`FetchData`Komponenta používá vloženou službu jako `ForecastService` k načtení pole `WeatherForecast` objektů:</span><span class="sxs-lookup"><span data-stu-id="28aca-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor WebAssembly<span data-ttu-id="28aca-173">Využij</span><span class="sxs-lookup"><span data-stu-id="28aca-173"> experience</span></span>

<span data-ttu-id="28aca-174">Pokud pracujete s Blazor WebAssembly aplikací, <xref:System.Net.Http.HttpClient> je vložena pro získání dat předpovědi počasí ze `weather.json` souboru ve `wwwroot/sample-data` složce.</span><span class="sxs-lookup"><span data-stu-id="28aca-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the `weather.json` file in the `wwwroot/sample-data` folder.</span></span>

<span data-ttu-id="28aca-175">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="28aca-175">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="28aca-176">[`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)Smyčka se používá k vykreslení každé instance prognózy jako řádku v tabulce dat o počasí:</span><span class="sxs-lookup"><span data-stu-id="28aca-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="28aca-177">Sestavení seznamu TODO</span><span class="sxs-lookup"><span data-stu-id="28aca-177">Build a todo list</span></span>

<span data-ttu-id="28aca-178">Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="28aca-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="28aca-179">Do aplikace přidejte do `Todo` Razor ní novou komponentu `Pages` .</span><span class="sxs-lookup"><span data-stu-id="28aca-179">Add a new `Todo` Razor component to the app in the `Pages` folder.</span></span> <span data-ttu-id="28aca-180">Pokud používáte aplikaci Visual Studio, klikněte pravým tlačítkem myši na `Pages` složku a vyberte možnost **Přidat**  >  **novou položku**  >  \*\* Razor \*\*.</span><span class="sxs-lookup"><span data-stu-id="28aca-180">If you're using Visual Studio, right-click the `Pages` folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="28aca-181">Pojmenujte soubor součásti `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="28aca-181">Name the component's file `Todo.razor`.</span></span> <span data-ttu-id="28aca-182">V jiných vývojových prostředích přidejte do `Pages` složky s názvem prázdný soubor `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="28aca-182">In other development environments, add a blank file to the `Pages` folder named `Todo.razor`.</span></span> Razor<span data-ttu-id="28aca-183">názvy souborů součástí vyžadují první písmeno s velkými písmeny, proto ověřte, že `Todo` název souboru komponenty začíná velkým písmenem `T` .</span><span class="sxs-lookup"><span data-stu-id="28aca-183"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="28aca-184">Zadejte počáteční označení pro komponentu:</span><span class="sxs-lookup"><span data-stu-id="28aca-184">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="28aca-185">Přidejte `Todo` komponentu do navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="28aca-185">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="28aca-186">`NavMenu`Součást ( `Shared/NavMenu.razor` ) se používá v rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="28aca-186">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="28aca-187">Rozložení jsou komponenty, které umožňují vyhnout se duplikaci obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="28aca-187">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="28aca-188">Přidejte `<NavLink>` element pro komponentu přidáním `Todo` následujícího označení položky seznamu pod existující položky seznamu v `Shared/NavMenu.razor` souboru:</span><span class="sxs-lookup"><span data-stu-id="28aca-188">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="28aca-189">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="28aca-189">Rebuild and run the app.</span></span> <span data-ttu-id="28aca-190">Přejděte na stránku Nová TODO a potvrďte, že odkaz na `Todo` komponentu funguje.</span><span class="sxs-lookup"><span data-stu-id="28aca-190">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="28aca-191">Do `TodoItem.cs` kořenu projektu přidejte soubor, který bude obsahovat třídu, která představuje položku todo.</span><span class="sxs-lookup"><span data-stu-id="28aca-191">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="28aca-192">Pro třídu použijte následující kód jazyka C# `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="28aca-192">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="28aca-193">Vrátit se k `Todo` součásti ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="28aca-193">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="28aca-194">Přidejte pole pro položky ToDo v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="28aca-194">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="28aca-195">`Todo`Komponenta používá toto pole k údržbě stavu seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="28aca-195">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="28aca-196">Přidejte neuspořádaný seznam značek a `foreach` smyčku pro vykreslení každé položky ToDo jako položky seznamu ( `<li>` ).</span><span class="sxs-lookup"><span data-stu-id="28aca-196">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="28aca-197">Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu.</span><span class="sxs-lookup"><span data-stu-id="28aca-197">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="28aca-198">Přidejte textové zadání ( `<input>` ) a tlačítko ( `<button>` ) pod Neseřazený seznam ( `<ul>...</ul>` ):</span><span class="sxs-lookup"><span data-stu-id="28aca-198">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="28aca-199">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="28aca-199">Rebuild and run the app.</span></span> <span data-ttu-id="28aca-200">Když **`Add todo`** je vybráno tlačítko, nic se nestane, protože obslužná rutina události není na tlačítko kabelem.</span><span class="sxs-lookup"><span data-stu-id="28aca-200">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="28aca-201">Přidejte `AddTodo` do `Todo` komponenty metodu a zaregistrujte ji pro výběry tlačítek pomocí `@onclick` atributu.</span><span class="sxs-lookup"><span data-stu-id="28aca-201">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="28aca-202">`AddTodo`Metoda jazyka C# je volána, když je vybráno tlačítko:</span><span class="sxs-lookup"><span data-stu-id="28aca-202">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="28aca-203">Chcete-li získat název nové položky ToDo, přidejte `newTodo` pole řetězce v horní části `@code` bloku a vytvořte jeho svázání s hodnotou textového zadání pomocí `bind` atributu v `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="28aca-203">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="28aca-204">Aktualizujte `AddTodo` metodu tak, aby se do `TodoItem` seznamu přidal název se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="28aca-204">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="28aca-205">Vymažte hodnotu textového zadání nastavením `newTodo` na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="28aca-205">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="28aca-206">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="28aca-206">Rebuild and run the app.</span></span> <span data-ttu-id="28aca-207">Chcete-li otestovat nový kód, přidejte do seznamu TODO některé položky ToDo.</span><span class="sxs-lookup"><span data-stu-id="28aca-207">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="28aca-208">Text nadpisu pro každou položku TODO lze upravovat a zaškrtávací políčko může uživatelům pomáhat sledovat dokončené položky.</span><span class="sxs-lookup"><span data-stu-id="28aca-208">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="28aca-209">Přidejte vstup zaškrtávacího políčka pro každou položku TODO a navažte její hodnotu na `IsDone` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="28aca-209">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="28aca-210">Změnit `@todo.Title` na `<input>` prvek vázaný na `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="28aca-210">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="28aca-211">Chcete-li ověřit, zda jsou tyto hodnoty vázány, aktualizujte `<h3>` záhlaví tak, aby zobrazovalo počet nedokončených položek TODO ( `IsDone` je `false` ).</span><span class="sxs-lookup"><span data-stu-id="28aca-211">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="28aca-212">Dokončená `Todo` součást ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="28aca-212">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="28aca-213">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="28aca-213">Rebuild and run the app.</span></span> <span data-ttu-id="28aca-214">Přidejte položky ToDo pro otestování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="28aca-214">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="28aca-215">Další kroky</span><span class="sxs-lookup"><span data-stu-id="28aca-215">Next steps</span></span>

<span data-ttu-id="28aca-216">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="28aca-216">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="28aca-217">Vytvoření Blazor projektu aplikace seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="28aca-217">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="28aca-218">Upravit Razor součásti</span><span class="sxs-lookup"><span data-stu-id="28aca-218">Modify Razor components</span></span>
> * <span data-ttu-id="28aca-219">Použití zpracování událostí a datové vazby v součástech</span><span class="sxs-lookup"><span data-stu-id="28aca-219">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="28aca-220">Použití vkládání závislostí (DI) a směrování v Blazor aplikaci</span><span class="sxs-lookup"><span data-stu-id="28aca-220">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="28aca-221">Naučte se vytvářet a používat komponenty:</span><span class="sxs-lookup"><span data-stu-id="28aca-221">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
