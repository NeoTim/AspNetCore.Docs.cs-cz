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
# <a name="build-your-first-blazor-app"></a>Vytvořte svoji první aplikaci Blazor

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

V tomto kurzu se sestavení krok za krokem Blazor aplikace a rychle se naučíte základní funkce rozhraní komponenty Razor.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([stažení](xref:index#how-to-download-a-sample)). Najdete v článku [Začínáme](xref:razor-components/get-started) tématu pro požadavky.

Vytvoření projektu v sadě Visual Studio:

1. Vyberte **Soubor** > **Nový** > **Projekt**. Vyberte **webové** > **webová aplikace ASP.NET Core**. Pojmenujte projekt "BlazorApp1" v **název** pole. Vyberte **OK**.

    ![Nový projekt ASP.NET Core](build-your-first-blazor-app/_static/new-aspnet-core-project.png)

1. **Nová webová aplikace ASP.NET Core** se zobrazí dialogové okno. Ujistěte se, že **.NET Core** je vybraný v horní části. Vyberte **ASP.NET Core 2.1**. Zvolte **Blazor** šablony a vyberte **OK**.

    ![Dialogové okno Nový Blazor aplikace](build-your-first-blazor-app/_static/new-blazor-app-dialog.png)

1. Po vytvoření projektu stiskněte **Ctrl-F5** a spusťte tak aplikaci *bez ladicího programu*. Spouštění v ladicím programu (**F5**) není v tuto chvíli nepodporuje.

> [!NOTE]
> Pokud ne pomocí sady Visual Studio, vytvořit aplikaci Blazor na příkazovém řádku ve Windows, macOS nebo Linuxu:
>
> ```console
> dotnet new --install "Microsoft.AspNetCore.Blazor.Templates"
> dotnet new blazor -o BlazorApp1
> cd BlazorApp1
> dotnet run
> ```
>
> Přejděte do aplikace pomocí adresy místního hostitele a portu zadaném v výstup okna konzoly po `dotnet run` provádí. Použití **Ctrl-C** v okně konzoly na ukončení aplikace.

Blazor aplikace běží v prohlížeči:

![Domovská stránka Blazor aplikace](https://user-images.githubusercontent.com/1874516/39509497-5515c3ea-4d9b-11e8-887f-019ea4fdb3ee.png)

## <a name="build-components"></a>Sestavení komponent

1. Přejděte do všech tří stránek vaší aplikace: Domů čítač a načíst data.

    Tyto tři stránky jsou implementované v tři soubory Razor *stránky* složky: *Index.cshtml*, *Counter.cshtml*, a *FetchData.cshtml*. Každý z těchto souborů implementuje komponentu, která se má zkompilovat a spustit na straně klienta v prohlížeči.

1. Klikněte na tlačítko na stránce čítače.

    ![Domovská stránka Blazor aplikace](https://user-images.githubusercontent.com/1874516/39509525-6e367c66-4d9b-11e8-9978-e52a9750c34b.png)

    Pokaždé, když výběru tlačítka čítače se zvýší, bez aktualizace stránky. Za normálních okolností tento druh chování na straně klienta probíhá v jazyce JavaScript; ale tady je implementována v C# a .NET pomocí `Counter` komponenty.

1. Podívejte se na provádění `Counter` v komponentu *Counter.cshtml* souboru:

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

    V uživatelském rozhraní `Counter` komponenta je definován pomocí normální HTML. Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor). Značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty v okamžiku sestavení. Název generované třídy .NET odpovídá názvu souboru.

    Členy třídy komponenty jsou definovány v `@functions` bloku. V `@functions` blokovat, stav komponent (vlastnosti, pole) a metod se dá nastavit pro zpracování událostí nebo definování dalších součástí logiky. Tyto členy můžete použije jako součást logiky komponenty vykreslování a pro zpracování událostí.

    Při výběru tlačítka `Counter` zaregistrované komponenty `onclick` obslužná rutina volána ( `IncrementCount` – metoda) a `Counter` obnoví komponenty jeho vykreslení stromu. Blazor porovná větve vykreslení oproti předchozímu a platí všechny změny v prohlížeči Document Object Model (DOM). Počet zobrazených se aktualizuje.

1. Aktualizujte kód pro `Counter` součást nejvyšší úrovně záhlaví více *zajímavých*.

    ```cshtml
    @page "/counter"
    <h1><em>Counter!!</em></h1>
    ```

1. Také upravit C# logiku `Counter` součást přírůstek počtu ve dvou místo jednoho.

    ```cshtml
    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount += 2;
        }
    }
    ```

1. Aktualizujte čítače stránku v prohlížeči, aby se změny projevily.

    ![Zajímavé čítače](https://user-images.githubusercontent.com/1874516/39509668-e8949a92-4d9b-11e8-91e9-b6a494695d92.png)

## <a name="use-components"></a>Použití komponent

Po definování komponenty součást je možné provádět další komponenty. Kód pro použití komponenty vypadá jako značku jazyka HTML, kde název značky je typ součásti.

1. Přidat `Counter` komponentu na domovskou stránku aplikace (*Index.cshtml*).

    ```cshtml
    @page "/"

    <h1>Hello, world!</h1>

    Welcome to your new app.

    <SurveyPrompt Title="How is Blazor working for you?" />

    <Counter />
    ```

1. Aktualizujte domovskou stránku v prohlížeči. Všimněte si samostatnou instanci `Counter` komponentu na domovské stránce.

    ![Blazor Domovská stránka s čítačem](https://user-images.githubusercontent.com/1874516/39509718-224483f6-4d9c-11e8-9030-b4c7228d669d.png)

## <a name="component-parameters"></a>Parametry komponenty

Součásti může mít také parametry, které jsou definovány pomocí vlastnosti privátní na komponentní třída dekorován `[Parameter]`. Atributy můžete zadat argumenty pro komponentu v kódu. 

1. Aktualizace `Counter` součást, kterou jste `IncrementAmount` parametr, který se výchozí hodnota je 1.

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
    > Ze sady Visual Studio, můžete rychle přidat parametr součásti s použitím `para` fragment kódu. Typ `para` a potom stiskněte klávesu `Tab` klíč dvakrát.

1. Na domovské stránce (*Index.cshtml*), změnit částku přírůstek `Counter` 10 nastavením atributu, který odpovídá názvu vlastnosti součásti pro `IncrementCount`.

    ```cshtml
    <Counter IncrementAmount="10" />
    ```

1. Načtěte tuto stránku.

    Čítače na domovskou stránku přírůstky teď o 10, zatímco čítače na stránce čítače pořád zvýší o 1.

    ![Počet Blazor podle deset](https://user-images.githubusercontent.com/1874516/39509798-618f0720-4d9c-11e8-9125-3d4c634dff46.png)

## <a name="route-to-components"></a>Směrovat do komponenty

`@page` Direktiv v horní části *Counter.cshtml* soubor Určuje, zda tato součást je stránka, ke které je možné směrovat požadavky. Konkrétně `Counter` komponenta zpracovává požadavky odeslané na `/Counter`. Bez `@page` směrnice, by komponenta zpracování směrované požadavků, ale součást může stále používat další komponenty.

## <a name="dependency-injection"></a>Injektáž závislostí

Služby zaregistrované pomocí poskytovatele služby app service jsou k dispozici na komponenty prostřednictvím [injektáž závislostí (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Služby můžete vloženy do komponenty pomocí `@inject` směrnice.

Podívejte se na provádění `FetchData` v komponentu *FetchData.cshtml*. `@inject` Směrnice slouží k vložení [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) instance do komponenty.

```cshtml
@page "/fetchdata"
@inject HttpClient Http
```

`FetchData` Komponenta používá vložené `HttpClient` k načtení dat JSON ze serveru, když komponenta je inicializována. Na pozadí `HttpClient` zadaný ve Blazor je implementovaná za běhu pomocí zprostředkovatele komunikace s objekty jazyka JavaScript pro volání prohlížeče načíst API k odeslání žádosti (z C#, je možné volat jakékoli knihovny nebo prohlížeč rozhraní API pro JavaScript). Načtená data je deserializovat do `forecasts` C# proměnnou pole `WeatherForecast` objekty.

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

A `@foreach` smyčky se použije k vykreslení každého prognózy instance jako řádek v tabulce počasí.

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

## <a name="build-a-todo-list"></a>Vytvoření seznamu úkolů

Přidejte novou stránku do aplikace, která implementuje seznam úkolů.

1. Prázdný textový soubor, který chcete přidat *stránky* složku s názvem *Todo.cshtml*.

1. Zadejte počáteční značka pro stránku.

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>
    ```

1. Přidat stránku Todo do navigačního panelu aktualizací *Shared/NavMenu.cshtml*. Přidat `NavLink` Todo stránky tak, že přidáte následující značky položky seznamu níže existující položky seznamu.

    ```cshtml
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="todo">
            <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
        </NavLink>
    </li>
    ```

1. Aktualizujte aplikaci v prohlížeči. Zobrazit nová stránka seznamu úkolů.

    ![Počáteční Blazor todo](https://user-images.githubusercontent.com/1874516/39509907-bb27e77a-4d9c-11e8-91e7-ea1e7c01729e.png)

1. Přidat *TodoItem.cs* souboru do kořenového adresáře projektu k uložení třídu k vyjádření položky seznamu úkolů.

1. Pomocí následujících C# kód `TodoItem` třídy.

    ```csharp
    public class TodoItem
    {
        public string Title { get; set; }
        public bool IsDone { get; set; }
    }
    ```

1. Přejděte zpět `Todo` komponentu v *Todo.cshtml* a přidejte pole pro úloh, ať už v `@functions` bloku. `Todo` Součásti používá toto pole pro uchování stavu pro seznam úkolů.

    ```cshtml
    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. Přidat neuspořádaný seznam značek a `foreach` smyčky k vykreslení každé položky todo jako položku seznamu.

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

1. Aplikace vyžaduje pro přidání do seznamu úloh, ať už prvky uživatelského rozhraní. Přidání textového zadání a tlačítka v seznamu níže.

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

1. Aktualizujte prohlížeč.

    ![Přidání tlačítka todo](https://user-images.githubusercontent.com/1874516/39512402-bc88ab46-4da5-11e8-9e3f-87b875b56383.png)

    Nic se nestane, když **přidat todo** se vybere tlačítko, protože žádná obslužná rutina události je svázanou s tlačítka.

1. Přidat `AddTodo` metodu `Todo` komponenty a zaregistrujte ho pro tlačítko klikne pomocí `onclick` atribut.

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

    `AddTodo` C# Metoda je volána při každém výběru tlačítka.

1. Pokud chcete získat název nové položky seznamu úkolů, přidejte `newTodo` řetězec pole a navázat jej na hodnotu text input pomocí `bind` atribut.

    ```csharp
    IList<TodoItem> todos = new List<TodoItem>();
    string newTodo;
    ```

    ```cshtml
    <input placeholder="Something todo" bind="@newTodo" />
    ```

1. Aktualizace `AddTodo` způsob, jak přidat `TodoItem` se zadaným názvem do seznamu. Nezapomeňte na hodnotu smazat, textového zadání tak, že nastavíte `newTodo` na prázdný řetězec.

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

1. Aktualizujte prohlížeč. Přidání některých úloh, ať už do seznamu úkolů.

    ![Přidání úloh, ať už](https://user-images.githubusercontent.com/1874516/39512531-2d2ff62e-4da6-11e8-8b83-291b0efc821b.png)

1. A konečně k čemu slouží seznamy úkolů bez zaškrtávací políčka? Text názvu pro každou položku seznamu úkolů lze upravovat stejně. Přidat vstup zaškrtávacího políčka a text input pro každou položku seznamu úkolů a jejich hodnoty pro vytvoření vazby `Title` a `IsDone` vlastnosti, v uvedeném pořadí.

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

1. Chcete-li ověřit, že tyto hodnoty jsou vázány, aktualizujte `h1` záhlaví zobrazíte počet nesplněné položky seznamu úkolů ještě neskončili (`IsDone` je `false`).

    ```cshtml
    <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
    ```

1. Dokončené `Todo` součásti by měl vypadat přibližně takto:

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

Aktualizujte aplikaci v prohlížeči. Přidejte několik položek todo.

![Dokončení Blazor seznam úkolů](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

## <a name="publish-and-deploy"></a>Publikování a nasazení

Při používání sady Visual Studio, proveďte následující kroky a publikujte aplikaci seznamu úkolů Blazor do Azure:

1. Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat**.

1. V **vyberte cíl publikování** dialogového okna, vyberte **služby App Service** a **vytvořit nový**. Vyberte **Publikovat**.

    ![Vyberte cíl publikování.](build-your-first-blazor-app/_static/blazor-publish-pick-target.png)

1. V **vytvořit službu App Service** dialogového okna, vyberte název aplikace a vyberte předplatné, skupinu prostředků a plán hostování. Vyberte **vytvořit** vytvořit službu app service a publikování aplikace.

    ![Vytvořit službu app service](build-your-first-blazor-app/_static/blazor-publish-create-appservice2.png)

Počkejte minutu, aby pro aplikaci k nasazení.

Aplikace by teď měla být spuštěná v Azure. Označit položky seznamu úkolů k sestavení první aplikace Blazor jako *provádí*. Skvělá práce!

![Blazor v Azure](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

> [!NOTE]
> Pokud ne pomocí sady Visual Studio, publikujte aplikaci Blazor na příkazovém řádku ve Windows, macOS nebo Linuxu:
>
> ```console
> dotnet publish -c Release
> ```
>
> Nasazení se vytvoří v */bin/vydání/\<Cílová architektura > / publish* složky. Přesunout obsah *publikovat* složky na serveru nebo hostující služby.
>
> Další informace najdete v tématu [hostitele a nasadit](xref:host-and-deploy/razor-components/index#publish-the-app) tématu.

## <a name="additional-resources"></a>Další zdroje

Pro více součástí Blazor ukázkovou aplikaci, přečtěte si [ukázka Flight Finder](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) na Githubu.

![Blazor Flight Finder](https://msdnshared.blob.core.windows.net/media/2018/03/blazor-flight-finder.png)
