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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 892663a533a207df84b0fce9af259a7dc212bc9b
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292773"
---
# <a name="build-your-first-blazor-app"></a>Sestavení první Blazor aplikace

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

V tomto kurzu se dozvíte, jak vytvořit a upravit Blazor aplikaci. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření Blazor projektu aplikace seznamu úkolů
> * Upravit Razor součásti
> * Použití zpracování událostí a datové vazby v součástech
> * Použití vkládání závislostí (DI) a směrování v Blazor aplikaci

Na konci tohoto kurzu budete mít funkční aplikaci seznamu úkolů.

## <a name="build-components"></a>Komponenty sestavení

1. Pokud <xref:blazor/get-started> chcete vytvořit Blazor projekt pro tento kurz, postupujte podle pokynů v článku. Pojmenujte projekt `ToDoList` .

1. Ve složce přejděte na každou ze tří stránek aplikace `Pages` : `Home` , `Counter` , a `Fetch data` . Tyto stránky jsou implementovány pomocí Razor souborů komponenty `Index.razor` , `Counter.razor` a `FetchData.razor` .

1. Na `Counter` stránce vyberte tlačítko pro zvýšení čítače bez aktualizace stránky. Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu. Pomocí Blazor můžete místo toho napsat C#.

1. Prověřte implementaci `Counter` komponenty v `Counter.razor` souboru.

   `Pages/Counter.razor`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Uživatelské rozhraní `Counter` komponenty je definováno pomocí jazyka HTML. Dynamická logika vykreslování (například cykly, podmíněné výrazy, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem [Razor](xref:mvc/views/razor) . Značky HTML značek a vykreslování v jazyce C# jsou převedeny na třídu komponenty v době sestavení. Název generované třídy .NET se shoduje s názvem souboru.

   Členy třídy komponenty jsou definovány v `@code` bloku. V `@code` bloku jsou pro zpracování událostí nebo pro definování jiné logiky komponent určeny stav součásti (vlastnosti, pole) a metody. Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.

   Když je vybráno tlačítko přírůstek čítače:

   * `Counter`Registrovaná `onclick` obslužná rutina komponenty je volána ( `IncrementCount` Metoda).
   * `Counter`Komponenta znovu vygeneruje svůj strom vykreslování.
   * Nový strom vykreslování je porovnán s předchozím.
   * Jsou aplikovány pouze změny model DOM (Document Object Model) (DOM). Zobrazený počet je aktualizovaný.

1. Upravte logiku komponenty jazyka C# `Counter` tak, aby byl přírůstek počtu vynásoben dvěma místomi jednoho.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Pokud chcete zobrazit změny, sestavte a spusťte aplikaci. Vyberte tlačítko. Čítače se zvýší o dva.

## <a name="use-components"></a>Použití komponent

Zahrnutí komponenty do jiné komponenty pomocí syntaxe jazyka HTML.

1. Přidejte `Counter` komponentu do `Index` komponenty aplikace přidáním `<Counter />` elementu do `Index` součásti ( `Index.razor` ).

   Pokud Blazor pro toto prostředí používáte WebAssembly, `SurveyPrompt` Komponenta je používána komponentou `Index` . Nahraďte `<SurveyPrompt>` element elementem `<Counter />` . Pokud Blazor pro toto prostředí používáte serverovou aplikaci, přidejte `<Counter />` element do `Index` komponenty:

   `Pages/Index.razor`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Znovu sestavte a spusťte aplikaci. `Index`Komponenta má vlastní čítač.

## <a name="component-parameters"></a>Parametry součásti

Komponenty mohou mít také parametry. Parametry komponenty jsou definovány pomocí veřejných vlastností třídy Component s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributem. Použijte atributy k určení argumentů pro komponentu v kódu.

1. Aktualizujte `@code` kód C# komponenty následujícím způsobem:

   * Přidejte veřejnou `IncrementAmount` vlastnost s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributem.
   * Změňte `IncrementCount` metodu na použití `IncrementAmount` vlastnosti při zvyšování hodnoty `currentCount` .

   `Pages/Counter.razor`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Zadejte `IncrementAmount` parametr v `Index` `<Counter>` elementu komponenty pomocí atributu. Nastavte hodnotu pro zvýšení čítače o deset.

   `Pages/Index.razor`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Znovu načtěte `Index` komponentu. Čítač se zvýší o deset pokaždé, když je vybráno tlačítko. Čítač v součásti se `Counter` stále zvyšuje o jednu.

## <a name="route-to-components"></a>Směrování na součásti

`@page`Direktiva v horní části `Counter.razor` souboru určuje, že `Counter` součást je koncový bod směrování. `Counter`Komponenta zpracovává požadavky odeslané na `/counter` . Bez `@page` direktivy nezpracovávají součásti směrované požadavky, ale komponentu mohou i nadále používat jiné komponenty.

## <a name="dependency-injection"></a>Injektáž závislostí

### <a name="blazor-server-experience"></a>BlazorProstředí serveru

Pokud pracujete se Blazor serverovou aplikací, `WeatherForecastService` služba je v nástroji registrována jako typ [singleton](xref:fundamentals/dependency-injection#service-lifetimes) `Startup.ConfigureServices` . Instance služby je k dispozici v celé aplikaci prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

[`@inject`](xref:mvc/views/razor#inject)Direktiva slouží k vložení instance `WeatherForecastService` služby do `FetchData` komponenty.

`Pages/FetchData.razor`:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

`FetchData`Komponenta používá vloženou službu jako `ForecastService` k načtení pole `WeatherForecast` objektů:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>BlazorProstředí WebAssembly

Při práci s Blazor aplikací WebAssembly <xref:System.Net.Http.HttpClient> je vloženo pro získání dat předpovědi počasí ze `weather.json` souboru ve `wwwroot/sample-data` složce.

`Pages/FetchData.razor`:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

[`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)Smyčka se používá k vykreslení každé instance prognózy jako řádku v tabulce dat o počasí:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Sestavení seznamu TODO

Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.

1. Do aplikace přidejte do `Todo` Razor ní novou komponentu `Pages` . Pokud používáte aplikaci Visual Studio, klikněte pravým tlačítkem myši na `Pages` složku a vyberte možnost **Přidat**  >  **novou položku**  >  ** Razor **. Pojmenujte soubor součásti `Todo.razor` . V jiných vývojových prostředích přidejte do `Pages` složky s názvem prázdný soubor `Todo.razor` . Razornázvy souborů součástí vyžadují první písmeno s velkými písmeny, proto ověřte, že `Todo` název souboru komponenty začíná velkým písmenem `T` .

1. Zadejte počáteční označení pro komponentu:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Přidejte `Todo` komponentu do navigačního panelu.

   `NavMenu`Součást ( `Shared/NavMenu.razor` ) se používá v rozložení aplikace. Rozložení jsou komponenty, které umožňují vyhnout se duplikaci obsahu v aplikaci.

   Přidejte `<NavLink>` element pro komponentu přidáním `Todo` následujícího označení položky seznamu pod existující položky seznamu v `Shared/NavMenu.razor` souboru:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Znovu sestavte a spusťte aplikaci. Přejděte na stránku Nová TODO a potvrďte, že odkaz na `Todo` komponentu funguje.

1. Do `TodoItem.cs` kořenu projektu přidejte soubor, který bude obsahovat třídu, která představuje položku todo. Pro třídu použijte následující kód jazyka C# `TodoItem` :

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Vrátit se k `Todo` součásti ( `Pages/Todo.razor` ):

   * Přidejte pole pro položky ToDo v `@code` bloku. `Todo`Komponenta používá toto pole k údržbě stavu seznamu úkolů.
   * Přidejte neuspořádaný seznam značek a `foreach` smyčku pro vykreslení každé položky ToDo jako položky seznamu ( `<li>` ).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu. Přidejte textové zadání ( `<input>` ) a tlačítko ( `<button>` ) pod Neseřazený seznam ( `<ul>...</ul>` ):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Znovu sestavte a spusťte aplikaci. Když **`Add todo`** je vybráno tlačítko, nic se nestane, protože obslužná rutina události není na tlačítko kabelem.

1. Přidejte `AddTodo` do `Todo` komponenty metodu a zaregistrujte ji pro výběry tlačítek pomocí `@onclick` atributu. `AddTodo`Metoda jazyka C# je volána, když je vybráno tlačítko:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Chcete-li získat název nové položky ToDo, přidejte `newTodo` pole řetězce v horní části `@code` bloku a vytvořte jeho svázání s hodnotou textového zadání pomocí `bind` atributu v `<input>` elementu:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aktualizujte `AddTodo` metodu tak, aby se do `TodoItem` seznamu přidal název se zadaným názvem. Vymažte hodnotu textového zadání nastavením `newTodo` na prázdný řetězec:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Znovu sestavte a spusťte aplikaci. Chcete-li otestovat nový kód, přidejte do seznamu TODO některé položky ToDo.

1. Text nadpisu pro každou položku TODO lze upravovat a zaškrtávací políčko může uživatelům pomáhat sledovat dokončené položky. Přidejte vstup zaškrtávacího políčka pro každou položku TODO a navažte její hodnotu na `IsDone` vlastnost. Změnit `@todo.Title` na `<input>` prvek vázaný na `@todo.Title` :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Chcete-li ověřit, zda jsou tyto hodnoty vázány, aktualizujte `<h3>` záhlaví tak, aby zobrazovalo počet nedokončených položek TODO ( `IsDone` je `false` ).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Dokončená `Todo` součást ( `Pages/Todo.razor` ):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Znovu sestavte a spusťte aplikaci. Přidejte položky ToDo pro otestování nového kódu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření Blazor projektu aplikace seznamu úkolů
> * Upravit Razor součásti
> * Použití zpracování událostí a datové vazby v součástech
> * Použití vkládání závislostí (DI) a směrování v Blazor aplikaci

Naučte se vytvářet a používat komponenty:

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
