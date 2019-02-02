---
title: Vytvořte svoji první aplikaci Blazor
author: guardrex
description: Vytvoření podrobné Blazor aplikace a rychle se naučíte základní funkce rozhraní komponenty Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: 99396e200eb121524abccc20a7d461062c94ecc7
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668099"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="40f2a-103">Vytvořte svoji první aplikaci Blazor</span><span class="sxs-lookup"><span data-stu-id="40f2a-103">Build your first Blazor app</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="40f2a-104">V tomto kurzu se sestavení krok za krokem Blazor aplikace a rychle se naučíte základní funkce rozhraní komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="40f2a-104">In this tutorial, you build a Blazor app step-by-step and quickly learn the basic features of the Razor Components framework.</span></span>

<span data-ttu-id="40f2a-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="40f2a-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="40f2a-106">Najdete v článku [Začínáme](xref:razor-components/get-started) tématu pro požadavky.</span><span class="sxs-lookup"><span data-stu-id="40f2a-106">See the [Get started](xref:razor-components/get-started) topic for prerequisites.</span></span>

<span data-ttu-id="40f2a-107">Vytvoření projektu v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="40f2a-107">To create the project in Visual Studio:</span></span>

1. <span data-ttu-id="40f2a-108">Vyberte **Soubor** > **Nový** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="40f2a-108">Select **File** > **New** > **Project**.</span></span> <span data-ttu-id="40f2a-109">Vyberte **webové** > **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="40f2a-109">Select **Web** > **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="40f2a-110">Pojmenujte projekt "BlazorApp1" v **název** pole.</span><span class="sxs-lookup"><span data-stu-id="40f2a-110">Name the project "BlazorApp1" in the **Name** field.</span></span> <span data-ttu-id="40f2a-111">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="40f2a-111">Select **OK**.</span></span>

    ![Nový projekt ASP.NET Core](build-your-first-blazor-app/_static/new-aspnet-core-project.png)

1. <span data-ttu-id="40f2a-113">**Nová webová aplikace ASP.NET Core** se zobrazí dialogové okno.</span><span class="sxs-lookup"><span data-stu-id="40f2a-113">The **New ASP.NET Core Web Application** dialog appears.</span></span> <span data-ttu-id="40f2a-114">Ujistěte se, že **.NET Core** je vybraný v horní části.</span><span class="sxs-lookup"><span data-stu-id="40f2a-114">Make sure **.NET Core** is selected at the top.</span></span> <span data-ttu-id="40f2a-115">Vyberte **ASP.NET Core 2.1**.</span><span class="sxs-lookup"><span data-stu-id="40f2a-115">Select **ASP.NET Core 2.1**.</span></span> <span data-ttu-id="40f2a-116">Zvolte **Blazor** šablony a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="40f2a-116">Choose the **Blazor** template and select **OK**.</span></span>

    ![Dialogové okno Nový Blazor aplikace](build-your-first-blazor-app/_static/new-blazor-app-dialog.png)

1. <span data-ttu-id="40f2a-118">Po vytvoření projektu stiskněte **Ctrl-F5** a spusťte tak aplikaci *bez ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="40f2a-118">Once the project is created, press **Ctrl-F5** to run the app *without the debugger*.</span></span> <span data-ttu-id="40f2a-119">Spouštění v ladicím programu (**F5**) není v tuto chvíli nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="40f2a-119">Running with the debugger (**F5**) isn't supported at this time.</span></span>

> [!NOTE]
> <span data-ttu-id="40f2a-120">Pokud ne pomocí sady Visual Studio, vytvořit aplikaci Blazor na příkazovém řádku ve Windows, macOS nebo Linuxu:</span><span class="sxs-lookup"><span data-stu-id="40f2a-120">If not using Visual Studio, create the Blazor app at a command prompt on Windows, macOS, or Linux:</span></span>
>
> ```console
> dotnet new --install "Microsoft.AspNetCore.Blazor.Templates"
> dotnet new blazor -o BlazorApp1
> cd BlazorApp1
> dotnet run
> ```
>
> <span data-ttu-id="40f2a-121">Přejděte do aplikace pomocí adresy místního hostitele a portu zadaném v výstup okna konzoly po `dotnet run` provádí.</span><span class="sxs-lookup"><span data-stu-id="40f2a-121">Navigate to the app using the localhost address and port provided in the console window output after `dotnet run` is executed.</span></span> <span data-ttu-id="40f2a-122">Použití **Ctrl-C** v okně konzoly na ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="40f2a-122">Use **Ctrl-C** in the console window to shutdown the app.</span></span>

<span data-ttu-id="40f2a-123">Blazor aplikace běží v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="40f2a-123">The Blazor app runs in the browser:</span></span>

![Domovská stránka Blazor aplikace](https://user-images.githubusercontent.com/1874516/39509497-5515c3ea-4d9b-11e8-887f-019ea4fdb3ee.png)

## <a name="build-components"></a><span data-ttu-id="40f2a-125">Sestavení komponent</span><span class="sxs-lookup"><span data-stu-id="40f2a-125">Build components</span></span>

1. <span data-ttu-id="40f2a-126">Přejděte do všech tří stránek vaší aplikace: Domů čítač a načíst data.</span><span class="sxs-lookup"><span data-stu-id="40f2a-126">Browse to each of the app's three pages: Home, Counter, and Fetch data.</span></span>

    <span data-ttu-id="40f2a-127">Tyto tři stránky jsou implementované v tři soubory Razor *stránky* složky: *Index.cshtml*, *Counter.cshtml*, a *FetchData.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="40f2a-127">These three pages are implemented by the three Razor files in the *Pages* folder: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.</span></span> <span data-ttu-id="40f2a-128">Každý z těchto souborů implementuje komponentu, která se má zkompilovat a spustit na straně klienta v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="40f2a-128">Each of these files implements a component that's compiled and executed client-side in the browser.</span></span>

1. <span data-ttu-id="40f2a-129">Klikněte na tlačítko na stránce čítače.</span><span class="sxs-lookup"><span data-stu-id="40f2a-129">Select the button on the Counter page.</span></span>

    ![Domovská stránka Blazor aplikace](https://user-images.githubusercontent.com/1874516/39509525-6e367c66-4d9b-11e8-9978-e52a9750c34b.png)

    <span data-ttu-id="40f2a-131">Pokaždé, když výběru tlačítka čítače se zvýší, bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="40f2a-131">Each time the button is selected, the counter is incremented without a page refresh.</span></span> <span data-ttu-id="40f2a-132">Za normálních okolností tento druh chování na straně klienta probíhá v jazyce JavaScript; ale tady je implementována v C# a .NET pomocí `Counter` komponenty.</span><span class="sxs-lookup"><span data-stu-id="40f2a-132">Normally, this kind of client-side behavior is handled in JavaScript; but here, it's implemented in C# and .NET by the `Counter` component.</span></span>

1. <span data-ttu-id="40f2a-133">Podívejte se na provádění `Counter` v komponentu *Counter.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="40f2a-133">Take a look at the implementation of the `Counter` component in the *Counter.cshtml* file:</span></span>

    ```cshtml
    @page "/counter"

    <h1>Counter</h1>

    <p>Current count: @currentCount</p>

    <button class="btn btn-primary" onclick="@IncrementCount">Click me</button>

    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
    ```

    <span data-ttu-id="40f2a-134">V uživatelském rozhraní `Counter` komponenta je definován pomocí normální HTML.</span><span class="sxs-lookup"><span data-stu-id="40f2a-134">The UI for the `Counter` component is defined using normal HTML.</span></span> <span data-ttu-id="40f2a-135">Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="40f2a-135">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor).</span></span> <span data-ttu-id="40f2a-136">Značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty v okamžiku sestavení.</span><span class="sxs-lookup"><span data-stu-id="40f2a-136">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="40f2a-137">Název generované třídy .NET odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="40f2a-137">The name of the generated .NET class matches the name of the file.</span></span>

    <span data-ttu-id="40f2a-138">Členy třídy komponenty jsou definovány v `@functions` bloku.</span><span class="sxs-lookup"><span data-stu-id="40f2a-138">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="40f2a-139">V `@functions` blokovat, stav komponent (vlastnosti, pole) a metod se dá nastavit pro zpracování událostí nebo definování dalších součástí logiky.</span><span class="sxs-lookup"><span data-stu-id="40f2a-139">In the `@functions` block, component state (properties, fields) and methods can be specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="40f2a-140">Tyto členy můžete použije jako součást logiky komponenty vykreslování a pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="40f2a-140">These members can then be used as part of the component's rendering logic and for handling events.</span></span>

    <span data-ttu-id="40f2a-141">Při výběru tlačítka `Counter` zaregistrované komponenty `onclick` obslužná rutina volána ( `IncrementCount` – metoda) a `Counter` obnoví komponenty jeho vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="40f2a-141">When the button is selected, the `Counter` component's registered `onclick` handler is called (the `IncrementCount` method) and the `Counter` component regenerates its render tree.</span></span> <span data-ttu-id="40f2a-142">Blazor porovná větve vykreslení oproti předchozímu a platí všechny změny v prohlížeči Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="40f2a-142">Blazor compares the new render tree against the previous one and applies any modifications to the browser Document Object Model (DOM).</span></span> <span data-ttu-id="40f2a-143">Počet zobrazených se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="40f2a-143">The displayed count is updated.</span></span>

1. <span data-ttu-id="40f2a-144">Aktualizujte kód pro `Counter` součást nejvyšší úrovně záhlaví více *zajímavých*.</span><span class="sxs-lookup"><span data-stu-id="40f2a-144">Update the markup for the `Counter` component to make the top-level header more *exciting*.</span></span>

    ```cshtml
    @page "/counter"
    <h1><em>Counter!!</em></h1>
    ```

1. <span data-ttu-id="40f2a-145">Také upravit C# logiku `Counter` součást přírůstek počtu ve dvou místo jednoho.</span><span class="sxs-lookup"><span data-stu-id="40f2a-145">Also modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

    ```cshtml
    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount += 2;
        }
    }
    ```

1. <span data-ttu-id="40f2a-146">Aktualizujte čítače stránku v prohlížeči, aby se změny projevily.</span><span class="sxs-lookup"><span data-stu-id="40f2a-146">Refresh the counter page in the browser to see the changes.</span></span>

    ![Zajímavé čítače](https://user-images.githubusercontent.com/1874516/39509668-e8949a92-4d9b-11e8-91e9-b6a494695d92.png)

## <a name="use-components"></a><span data-ttu-id="40f2a-148">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="40f2a-148">Use components</span></span>

<span data-ttu-id="40f2a-149">Po definování komponenty součást je možné provádět další komponenty.</span><span class="sxs-lookup"><span data-stu-id="40f2a-149">After a component is defined, the component can be used to implement other components.</span></span> <span data-ttu-id="40f2a-150">Kód pro použití komponenty vypadá jako značku jazyka HTML, kde název značky je typ součásti.</span><span class="sxs-lookup"><span data-stu-id="40f2a-150">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

1. <span data-ttu-id="40f2a-151">Přidat `Counter` komponentu na domovskou stránku aplikace (*Index.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="40f2a-151">Add a `Counter` component to the Home page of the app (*Index.cshtml*).</span></span>

    ```cshtml
    @page "/"

    <h1>Hello, world!</h1>

    Welcome to your new app.

    <SurveyPrompt Title="How is Blazor working for you?" />

    <Counter />
    ```

1. <span data-ttu-id="40f2a-152">Aktualizujte domovskou stránku v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="40f2a-152">Refresh the home page in the browser.</span></span> <span data-ttu-id="40f2a-153">Všimněte si samostatnou instanci `Counter` komponentu na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="40f2a-153">Note the separate instance of the `Counter` component on the Home page.</span></span>

    ![Blazor Domovská stránka s čítačem](https://user-images.githubusercontent.com/1874516/39509718-224483f6-4d9c-11e8-9030-b4c7228d669d.png)

## <a name="component-parameters"></a><span data-ttu-id="40f2a-155">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="40f2a-155">Component parameters</span></span>

<span data-ttu-id="40f2a-156">Součásti může mít také parametry, které jsou definovány pomocí vlastnosti privátní na komponentní třída dekorován `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="40f2a-156">Components can also have parameters, which are defined using private properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="40f2a-157">Atributy můžete zadat argumenty pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="40f2a-157">Use attributes to specify arguments for a component in markup.</span></span> 

1. <span data-ttu-id="40f2a-158">Aktualizace `Counter` součást, kterou jste `IncrementAmount` parametr, který se výchozí hodnota je 1.</span><span class="sxs-lookup"><span data-stu-id="40f2a-158">Update the `Counter` component to have an `IncrementAmount` parameter that defaults to 1.</span></span>

    ```cshtml
    @functions {
        int currentCount = 0;

        [Parameter]
        private int IncrementAmount { get; set; } = 1;

        void IncrementCount()
        {
            currentCount += IncrementAmount;
        }
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="40f2a-159">Ze sady Visual Studio, můžete rychle přidat parametr součásti s použitím `para` fragment kódu.</span><span class="sxs-lookup"><span data-stu-id="40f2a-159">From Visual Studio, you can quickly add a component parameter by using the `para` snippet.</span></span> <span data-ttu-id="40f2a-160">Typ `para` a potom stiskněte klávesu `Tab` klíč dvakrát.</span><span class="sxs-lookup"><span data-stu-id="40f2a-160">Type `para` and then press the `Tab` key twice.</span></span>

1. <span data-ttu-id="40f2a-161">Na domovské stránce (*Index.cshtml*), změnit částku přírůstek `Counter` 10 nastavením atributu, který odpovídá názvu vlastnosti součásti pro `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="40f2a-161">On the Home page (*Index.cshtml*), change the increment amount for the `Counter` to 10 by setting an attribute that matches the name of the component's property for `IncrementCount`.</span></span>

    ```cshtml
    <Counter IncrementAmount="10" />
    ```

1. <span data-ttu-id="40f2a-162">Načtěte tuto stránku.</span><span class="sxs-lookup"><span data-stu-id="40f2a-162">Reload the page.</span></span>

    <span data-ttu-id="40f2a-163">Čítače na domovskou stránku přírůstky teď o 10, zatímco čítače na stránce čítače pořád zvýší o 1.</span><span class="sxs-lookup"><span data-stu-id="40f2a-163">The counter on the Home page now increments by 10, while the counter on the Counter page still increments by 1.</span></span>

    ![Počet Blazor podle deset](https://user-images.githubusercontent.com/1874516/39509798-618f0720-4d9c-11e8-9125-3d4c634dff46.png)

## <a name="route-to-components"></a><span data-ttu-id="40f2a-165">Směrovat do komponenty</span><span class="sxs-lookup"><span data-stu-id="40f2a-165">Route to components</span></span>

<span data-ttu-id="40f2a-166">`@page` Direktiv v horní části *Counter.cshtml* soubor Určuje, zda tato součást je stránka, ke které je možné směrovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="40f2a-166">The `@page` directive at the top of the *Counter.cshtml* file specifies that this component is a page to which requests can be routed.</span></span> <span data-ttu-id="40f2a-167">Konkrétně `Counter` komponenta zpracovává požadavky odeslané na `/Counter`.</span><span class="sxs-lookup"><span data-stu-id="40f2a-167">Specifically, the `Counter` component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="40f2a-168">Bez `@page` směrnice, by komponenta zpracování směrované požadavků, ale součást může stále používat další komponenty.</span><span class="sxs-lookup"><span data-stu-id="40f2a-168">Without the `@page` directive, the component wouldn't handle routed requests, but the component could still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="40f2a-169">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="40f2a-169">Dependency injection</span></span>

<span data-ttu-id="40f2a-170">Služby zaregistrované pomocí poskytovatele služby app service jsou k dispozici na komponenty prostřednictvím [injektáž závislostí (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="40f2a-170">Services registered with the app's service provider are available to components via [dependency injection (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).</span></span> <span data-ttu-id="40f2a-171">Služby můžete vloženy do komponenty pomocí `@inject` směrnice.</span><span class="sxs-lookup"><span data-stu-id="40f2a-171">Services can be injected into a component using the `@inject` directive.</span></span>

<span data-ttu-id="40f2a-172">Podívejte se na provádění `FetchData` v komponentu *FetchData.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="40f2a-172">Take a look at the implementation of the `FetchData` component in *FetchData.cshtml*.</span></span> <span data-ttu-id="40f2a-173">`@inject` Směrnice slouží k vložení [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) instance do komponenty.</span><span class="sxs-lookup"><span data-stu-id="40f2a-173">The `@inject` directive is used to inject an [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) instance into the component.</span></span>

```cshtml
@page "/fetchdata"
@inject HttpClient Http
```

<span data-ttu-id="40f2a-174">`FetchData` Komponenta používá vložené `HttpClient` k načtení dat JSON ze serveru, když komponenta je inicializována.</span><span class="sxs-lookup"><span data-stu-id="40f2a-174">The `FetchData` component uses the injected `HttpClient` to retrieve JSON data from the server when the component is initialized.</span></span> <span data-ttu-id="40f2a-175">Na pozadí `HttpClient` zadaný ve Blazor je implementovaná za běhu pomocí zprostředkovatele komunikace s objekty jazyka JavaScript pro volání prohlížeče načíst API k odeslání žádosti (z C#, je možné volat jakékoli knihovny nebo prohlížeč rozhraní API pro JavaScript).</span><span class="sxs-lookup"><span data-stu-id="40f2a-175">Under the covers, the `HttpClient` provided by the Blazor runtime is implemented using JavaScript interop to call the underlying browser's Fetch API to send the request (from C#, it's possible to call any JavaScript library or browser API).</span></span> <span data-ttu-id="40f2a-176">Načtená data je deserializovat do `forecasts` C# proměnnou pole `WeatherForecast` objekty.</span><span class="sxs-lookup"><span data-stu-id="40f2a-176">The retrieved data is deserialized into the `forecasts` C# variable as an array of `WeatherForecast` objects.</span></span>

```cshtml
@functions {
    WeatherForecast[] forecasts;

    protected override async Task OnInitAsync()
    {
        forecasts = await Http.GetJsonAsync<WeatherForecast[]>("/sample-data/weather.json");
    }

    class WeatherForecast
    {
        public DateTime Date { get; set; }
        public int TemperatureC { get; set; }
        public int TemperatureF { get; set; }
        public string Summary { get; set; }
    }
}
```

<span data-ttu-id="40f2a-177">A `@foreach` smyčky se použije k vykreslení každého prognózy instance jako řádek v tabulce počasí.</span><span class="sxs-lookup"><span data-stu-id="40f2a-177">A `@foreach` loop is used to render each forecast instance as a row in the weather table.</span></span>

```cshtml
<table class="table">
    <thead>
        <tr>
            <th>Date</th>
            <th>Temp. (C)</th>
            <th>Temp. (F)</th>
            <th>Summary</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var forecast in forecasts)
        {
            <tr>
                <td>@forecast.Date.ToShortDateString()</td>
                <td>@forecast.TemperatureC</td>
                <td>@forecast.TemperatureF</td>
                <td>@forecast.Summary</td>
            </tr>
        }
    </tbody>
</table>
```

## <a name="build-a-todo-list"></a><span data-ttu-id="40f2a-178">Vytvoření seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="40f2a-178">Build a todo list</span></span>

<span data-ttu-id="40f2a-179">Přidejte novou stránku do aplikace, která implementuje seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="40f2a-179">Add a new page to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="40f2a-180">Prázdný textový soubor, který chcete přidat *stránky* složku s názvem *Todo.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="40f2a-180">Add an empty text file to the *Pages* folder named *Todo.cshtml*.</span></span>

1. <span data-ttu-id="40f2a-181">Zadejte počáteční značka pro stránku.</span><span class="sxs-lookup"><span data-stu-id="40f2a-181">Provide the initial markup for the page.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>
    ```

1. <span data-ttu-id="40f2a-182">Přidat stránku Todo do navigačního panelu aktualizací *Shared/NavMenu.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="40f2a-182">Add the Todo page to the navigation bar by updating *Shared/NavMenu.cshtml*.</span></span> <span data-ttu-id="40f2a-183">Přidat `NavLink` Todo stránky tak, že přidáte následující značky položky seznamu níže existující položky seznamu.</span><span class="sxs-lookup"><span data-stu-id="40f2a-183">Add a `NavLink` for the Todo page by adding the following list item markup below the existing list items.</span></span>

    ```cshtml
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="todo">
            <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
        </NavLink>
    </li>
    ```

1. <span data-ttu-id="40f2a-184">Aktualizujte aplikaci v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="40f2a-184">Refresh the app in the browser.</span></span> <span data-ttu-id="40f2a-185">Zobrazit nová stránka seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="40f2a-185">See the new Todo page.</span></span>

    ![Počáteční Blazor todo](https://user-images.githubusercontent.com/1874516/39509907-bb27e77a-4d9c-11e8-91e7-ea1e7c01729e.png)

1. <span data-ttu-id="40f2a-187">Přidat *TodoItem.cs* souboru do kořenového adresáře projektu k uložení třídu k vyjádření položky seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="40f2a-187">Add a *TodoItem.cs* file to the root of the project to hold a class to represent the todo items.</span></span>

1. <span data-ttu-id="40f2a-188">Pomocí následujících C# kód `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="40f2a-188">Use the following C# code for the `TodoItem` class.</span></span>

    ```csharp
    public class TodoItem
    {
        public string Title { get; set; }
        public bool IsDone { get; set; }
    }
    ```

1. <span data-ttu-id="40f2a-189">Přejděte zpět `Todo` komponentu v *Todo.cshtml* a přidejte pole pro úloh, ať už v `@functions` bloku.</span><span class="sxs-lookup"><span data-stu-id="40f2a-189">Go back to the `Todo` component in *Todo.cshtml* and add a field for the todos in a `@functions` block.</span></span> <span data-ttu-id="40f2a-190">`Todo` Součásti používá toto pole pro uchování stavu pro seznam úkolů.</span><span class="sxs-lookup"><span data-stu-id="40f2a-190">The `Todo` component uses this field to maintain the state of the todo list.</span></span>

    ```cshtml
    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. <span data-ttu-id="40f2a-191">Přidat neuspořádaný seznam značek a `foreach` smyčky k vykreslení každé položky todo jako položku seznamu.</span><span class="sxs-lookup"><span data-stu-id="40f2a-191">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>
    ```

1. <span data-ttu-id="40f2a-192">Aplikace vyžaduje pro přidání do seznamu úloh, ať už prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="40f2a-192">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="40f2a-193">Přidání textového zadání a tlačítka v seznamu níže.</span><span class="sxs-lookup"><span data-stu-id="40f2a-193">Add a text input and a button below the list.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>

    <input placeholder="Something todo" />
    <button>Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. <span data-ttu-id="40f2a-194">Aktualizujte prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="40f2a-194">Refresh the browser.</span></span>

    ![Přidání tlačítka todo](https://user-images.githubusercontent.com/1874516/39512402-bc88ab46-4da5-11e8-9e3f-87b875b56383.png)

    <span data-ttu-id="40f2a-196">Nic se nestane, když **přidat todo** se vybere tlačítko, protože žádná obslužná rutina události je svázanou s tlačítka.</span><span class="sxs-lookup"><span data-stu-id="40f2a-196">Nothing happens when the **Add todo** button is selected because no event handler is wired up to the button.</span></span>

1. <span data-ttu-id="40f2a-197">Přidat `AddTodo` metodu `Todo` komponenty a zaregistrujte ho pro tlačítko klikne pomocí `onclick` atribut.</span><span class="sxs-lookup"><span data-stu-id="40f2a-197">Add an `AddTodo` method to the `Todo` component and register it for button clicks using the `onclick` attribute.</span></span>

    ```cshtml
    <input placeholder="Something todo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();

        void AddTodo()
        {
            // Todo: Add the todo
        }
    }
    ```

    <span data-ttu-id="40f2a-198">`AddTodo` C# Metoda je volána při každém výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="40f2a-198">The `AddTodo` C# method is called every time the button is selected.</span></span>

1. <span data-ttu-id="40f2a-199">Pokud chcete získat název nové položky seznamu úkolů, přidejte `newTodo` řetězec pole a navázat jej na hodnotu text input pomocí `bind` atribut.</span><span class="sxs-lookup"><span data-stu-id="40f2a-199">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute.</span></span>

    ```csharp
    IList<TodoItem> todos = new List<TodoItem>();
    string newTodo;
    ```

    ```cshtml
    <input placeholder="Something todo" bind="@newTodo" />
    ```

1. <span data-ttu-id="40f2a-200">Aktualizace `AddTodo` způsob, jak přidat `TodoItem` se zadaným názvem do seznamu.</span><span class="sxs-lookup"><span data-stu-id="40f2a-200">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="40f2a-201">Nezapomeňte na hodnotu smazat, textového zadání tak, že nastavíte `newTodo` na prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="40f2a-201">Don't forget to clear the value of the text input by setting `newTodo` to an empty string.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

1. <span data-ttu-id="40f2a-202">Aktualizujte prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="40f2a-202">Refresh the browser.</span></span> <span data-ttu-id="40f2a-203">Přidání některých úloh, ať už do seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="40f2a-203">Add some todos to the todo list.</span></span>

    ![Přidání úloh, ať už](https://user-images.githubusercontent.com/1874516/39512531-2d2ff62e-4da6-11e8-8b83-291b0efc821b.png)

1. <span data-ttu-id="40f2a-205">A konečně k čemu slouží seznamy úkolů bez zaškrtávací políčka?</span><span class="sxs-lookup"><span data-stu-id="40f2a-205">Lastly, what's a todo list without check boxes?</span></span> <span data-ttu-id="40f2a-206">Text názvu pro každou položku seznamu úkolů lze upravovat stejně.</span><span class="sxs-lookup"><span data-stu-id="40f2a-206">The title text for each todo item can be made editable as well.</span></span> <span data-ttu-id="40f2a-207">Přidat vstup zaškrtávacího políčka a text input pro každou položku seznamu úkolů a jejich hodnoty pro vytvoření vazby `Title` a `IsDone` vlastnosti, v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="40f2a-207">Add a check box input and text input for each todo item and bind their values to the `Title` and `IsDone` properties, respectively.</span></span>

    ```cshtml
    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>
    ```

1. <span data-ttu-id="40f2a-208">Chcete-li ověřit, že tyto hodnoty jsou vázány, aktualizujte `h1` záhlaví zobrazíte počet nesplněné položky seznamu úkolů ještě neskončili (`IsDone` je `false`).</span><span class="sxs-lookup"><span data-stu-id="40f2a-208">To verify that these values are bound, update the `h1` header to show a count of the number of todo items that are not yet done (`IsDone` is `false`).</span></span>

    ```cshtml
    <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
    ```

1. <span data-ttu-id="40f2a-209">Dokončené `Todo` součásti by měl vypadat přibližně takto:</span><span class="sxs-lookup"><span data-stu-id="40f2a-209">The completed `Todo` component should look like this:</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

<span data-ttu-id="40f2a-210">Aktualizujte aplikaci v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="40f2a-210">Refresh the app in the browser.</span></span> <span data-ttu-id="40f2a-211">Přidejte několik položek todo.</span><span class="sxs-lookup"><span data-stu-id="40f2a-211">Try adding some todo items.</span></span>

![Dokončení Blazor seznam úkolů](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

## <a name="publish-and-deploy"></a><span data-ttu-id="40f2a-213">Publikování a nasazení</span><span class="sxs-lookup"><span data-stu-id="40f2a-213">Publish and deploy</span></span>

<span data-ttu-id="40f2a-214">Při používání sady Visual Studio, proveďte následující kroky a publikujte aplikaci seznamu úkolů Blazor do Azure:</span><span class="sxs-lookup"><span data-stu-id="40f2a-214">When using Visual Studio, perform the following steps to publish the Todo Blazor app to Azure:</span></span>

1. <span data-ttu-id="40f2a-215">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="40f2a-215">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="40f2a-216">V **vyberte cíl publikování** dialogového okna, vyberte **služby App Service** a **vytvořit nový**.</span><span class="sxs-lookup"><span data-stu-id="40f2a-216">In the **Pick a publish target** dialog, select **App Service** and **Create New**.</span></span> <span data-ttu-id="40f2a-217">Vyberte **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="40f2a-217">Select **Publish**.</span></span>

    ![Vyberte cíl publikování.](build-your-first-blazor-app/_static/blazor-publish-pick-target.png)

1. <span data-ttu-id="40f2a-219">V **vytvořit službu App Service** dialogového okna, vyberte název aplikace a vyberte předplatné, skupinu prostředků a plán hostování.</span><span class="sxs-lookup"><span data-stu-id="40f2a-219">In the **Create App Service** dialog, choose a name for the app and select the subscription, resource group, and hosting plan.</span></span> <span data-ttu-id="40f2a-220">Vyberte **vytvořit** vytvořit službu app service a publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="40f2a-220">Select **Create** to create the app service and publish the app.</span></span>

    ![Vytvořit službu app service](build-your-first-blazor-app/_static/blazor-publish-create-appservice2.png)

<span data-ttu-id="40f2a-222">Počkejte minutu, aby pro aplikaci k nasazení.</span><span class="sxs-lookup"><span data-stu-id="40f2a-222">Wait a minute or so for the app to be deployed.</span></span>

<span data-ttu-id="40f2a-223">Aplikace by teď měla být spuštěná v Azure.</span><span class="sxs-lookup"><span data-stu-id="40f2a-223">The app should now be running in Azure.</span></span> <span data-ttu-id="40f2a-224">Označit položky seznamu úkolů k sestavení první aplikace Blazor jako *provádí*.</span><span class="sxs-lookup"><span data-stu-id="40f2a-224">Mark the todo item to build your first Blazor app as *done*.</span></span> <span data-ttu-id="40f2a-225">Skvělá práce!</span><span class="sxs-lookup"><span data-stu-id="40f2a-225">Nice job!</span></span>

![Blazor v Azure](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

> [!NOTE]
> <span data-ttu-id="40f2a-227">Pokud ne pomocí sady Visual Studio, publikujte aplikaci Blazor na příkazovém řádku ve Windows, macOS nebo Linuxu:</span><span class="sxs-lookup"><span data-stu-id="40f2a-227">If not using Visual Studio, publish the Blazor app at a command prompt on Windows, macOS, or Linux:</span></span>
>
> ```console
> dotnet publish -c Release
> ```
>
> <span data-ttu-id="40f2a-228">Nasazení se vytvoří v */bin/vydání/\<Cílová architektura > / publish* složky.</span><span class="sxs-lookup"><span data-stu-id="40f2a-228">The deployment is created in the */bin/Release/\<target-framework>/publish* folder.</span></span> <span data-ttu-id="40f2a-229">Přesunout obsah *publikovat* složky na serveru nebo hostující služby.</span><span class="sxs-lookup"><span data-stu-id="40f2a-229">Move the contents of the *publish* folder to the server or hosting service.</span></span>
>
> <span data-ttu-id="40f2a-230">Další informace najdete v tématu [hostitele a nasadit](xref:host-and-deploy/razor-components/index#publish-the-app) tématu.</span><span class="sxs-lookup"><span data-stu-id="40f2a-230">For more information, see the [Host and deploy](xref:host-and-deploy/razor-components/index#publish-the-app) topic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40f2a-231">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="40f2a-231">Additional resources</span></span>

<span data-ttu-id="40f2a-232">Pro více součástí Blazor ukázkovou aplikaci, přečtěte si [ukázka Flight Finder](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) na Githubu.</span><span class="sxs-lookup"><span data-stu-id="40f2a-232">For a more involved Blazor sample app, check out the [Flight Finder sample](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) on GitHub.</span></span>

![Blazor Flight Finder](https://msdnshared.blob.core.windows.net/media/2018/03/blazor-flight-finder.png)
