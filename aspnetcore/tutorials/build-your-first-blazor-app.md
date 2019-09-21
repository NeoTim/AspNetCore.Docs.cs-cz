---
title: Vytvoření první aplikace v Blazor
author: guardrex
description: Sestavte aplikaci pro Blazor krok za krokem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/15/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: 10feb5467a6a6b5a43e0df739fa72902af9854da
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168359"
---
# <a name="build-your-first-blazor-app"></a>Vytvoření první aplikace v Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

V tomto kurzu se dozvíte, jak vytvořit a upravit aplikaci v Blazor.

Podle pokynů v <xref:blazor/get-started> článku vytvořte projekt Blazor pro tento kurz. Pojmenujte projekt *ToDoList*.

## <a name="build-components"></a>Komponenty sestavení

1. Ve složce *Pages (stránky* ) vyhledejte jednotlivé tři stránky aplikace: Domů, čítač a načíst data. Tyto stránky jsou implementovány pomocí indexu souborů komponent Razor *. Razor*, *Counter. Razor*a *FetchData. Razor*.

1. Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky. Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale Blazor poskytuje lepší přístup pomocí C#.

1. Projděte si implementaci `Counter` komponenty v souboru *Counter. Razor* .

   *Stránky/čítač. Razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Uživatelské rozhraní `Counter` komponenty je definováno pomocí jazyka HTML. Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor). Logika značek HTML a C# vykreslování jsou v době sestavení převedena na třídu komponenty. Název generované třídy .NET se shoduje s názvem souboru.

   Členy třídy komponenty jsou definovány v `@code` bloku. `@code` V bloku jsou pro zpracování událostí nebo pro definování jiné logiky komponent určeny stav součásti (vlastnosti, pole) a metody. Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.

   Když je vybrané tlačítko pro **kliknutí na mou adresu** :

   * Registrovaná `Counter` obslužná`onclick`rutina komponenty je volána (metoda). `IncrementCount`
   * `Counter` Komponenta znovu vygeneruje svůj strom vykreslování.
   * Nový strom vykreslování je porovnán s předchozím.
   * Jsou aplikovány pouze změny model DOM (Document Object Model) (DOM). Zobrazený počet je aktualizovaný.

1. Upravte C# logiku `Counter` komponenty tak, aby byl přírůstek počtu o dva místo jednoho.

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Pokud chcete zobrazit změny, sestavte a spusťte aplikaci. Vyberte tlačítko pro **kliknutí na tlačítko** . Čítače se zvýší o dva.

## <a name="use-components"></a>Použití komponent

Zahrnutí komponenty do jiné komponenty pomocí syntaxe jazyka HTML.

1. `Index` Přidejtekomponentudo`Index` komponenty aplikace přidáním `<Counter />` prvku do komponenty (*index. Razor*). `Counter`

   Pokud pro toto prostředí používáte Blazor WebAssembly, `SurveyPrompt` komponenta se používá `Index` komponentou. Nahraďte `<Counter />`elementelementem. `<SurveyPrompt>` Pokud pro toto prostředí používáte aplikaci serveru Blazor, přidejte `<Counter />` element `Index` do komponenty:

   *Pages/index. Razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Znovu sestavte a spusťte aplikaci. `Index` Komponenta má vlastní čítač.

## <a name="component-parameters"></a>Parametry součásti

Komponenty mohou mít také parametry. Parametry komponenty jsou definovány pomocí veřejných vlastností třídy Component s `[Parameter]` atributem. Použijte atributy k určení argumentů pro komponentu v kódu.

1. Aktualizujte C# kód komponenty `@code` :

   * Přidejte veřejnou `IncrementAmount` vlastnost `[Parameter]` s atributem.
   * Změňte metodu na `IncrementAmount` použití`currentCount`při zvyšování hodnoty. `IncrementCount`

   *Stránky/čítač. Razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. Zadejte parametr v elementu`<Counter>`komponentypomocíatributu. `Index` `IncrementAmount` Nastavte hodnotu pro zvýšení čítače o deset.

   *Pages/index. Razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Znovu načtěte `Index` komponentu. Čítač se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** . Čítač v `Counter` součásti se stále zvyšuje o jednu.

## <a name="route-to-components"></a>Směrování na součásti

Direktiva v horní části `Counter` souboru *Counter. Razor* určuje, že komponenta je koncový bod směrování. `@page` Komponenta zpracovává požadavky odeslané na `/counter`. `Counter` `@page` Bez direktivy nezpracovávají součásti směrované požadavky, ale komponentu mohou i nadále používat jiné komponenty.

## <a name="dependency-injection"></a>Injektáž závislostí

Pokud pracujete s aplikací serveru Blazor, `WeatherForecastService` služba se zaregistruje jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes) v `Startup.ConfigureServices`. Instance služby je k dispozici v celé aplikaci prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

Direktiva slouží k vložení instance `WeatherForecastService` služby do `FetchData` komponenty. `@inject`

*Stránky/FetchData. Razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Komponenta používá vloženou službu jako `ForecastService` `WeatherForecast` k načtení pole objektů: `FetchData`

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

Pokud pracujete s aplikací Blazor WebAssembly, `HttpClient` je vložena pro získání dat předpovědi počasí ze souboru *počasí. JSON* ve složce *wwwroot/Sample-data* .

*Stránky/FetchData. Razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Smyčka foreach se používá k vykreslení každé instance prognózy jako řádku v tabulce dat o počasí: [ \@](/dotnet/csharp/language-reference/keywords/foreach-in)

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Sestavení seznamu TODO

Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.

1. Do aplikace ve složce *Pages* přidejte prázdný soubor s názvem *todo. Razor* :

1. Zadejte počáteční označení pro komponentu:

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. `Todo` Přidejte komponentu do navigačního panelu.

   Komponenta (*Shared/NavMenu. Razor*) se používá v rozložení aplikace. `NavMenu` Rozložení jsou komponenty, které umožňují vyhnout se duplikaci obsahu v aplikaci.

   Přidejte element pro komponentu přidáním následujícího označení položky seznamu pod existující položky seznamu v souboru *Shared/NavMenu. Razor:* `Todo` `<NavLink>`

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Znovu sestavte a spusťte aplikaci. Přejděte na stránku Nová TODO a potvrďte, že odkaz na `Todo` komponentu funguje.

1. Do kořenového adresáře projektu přidejte soubor *TodoItem.cs* , který bude obsahovat třídu, která představuje položku todo. Pro`TodoItem` třídu použijte C# následující kód:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Vraťte se do `Todo` komponenty (*Pages/todo. Razor*):

   * Přidejte pole pro položky ToDo v `@code` bloku. `Todo` Komponenta používá toto pole k údržbě stavu seznamu úkolů.
   * Přidejte neuspořádaný seznam značek a `foreach` smyčku pro vykreslení každé položky ToDo jako položky seznamu (`<li>`).

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu. Přidejte textové zadání (`<input>`) a tlačítko (`<button>`) pod Neseřazený seznam (`<ul>...</ul>`):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Znovu sestavte a spusťte aplikaci. Když je vybráno tlačítko **Přidat TODO** , nic se nestane, protože obslužná rutina události není na tlačítko kabelem.

1. Přidejte do komponenty metodu a zaregistrujte ji `@onclick` pro výběry tlačítek pomocí atributu. `AddTodo` `Todo` C# volána, když je vybráno tlačítko `AddTodo` :

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Chcete-li získat název nové položky ToDo, `newTodo` přidejte pole řetězce v horní `@code` části bloku a vytvořte jeho svázání s hodnotou textového zadání `<input>` pomocí `bind` atributu v elementu:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aktualizujte `TodoItem` metodu tak, aby se do seznamu přidal název se zadaným názvem. `AddTodo` Vymažte hodnotu textového zadání nastavením `newTodo` na prázdný řetězec:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Znovu sestavte a spusťte aplikaci. Chcete-li otestovat nový kód, přidejte do seznamu TODO některé položky ToDo.

1. Text nadpisu pro každou položku TODO lze upravovat a zaškrtávací políčko může uživatelům pomáhat sledovat dokončené položky. Přidejte vstup zaškrtávacího políčka pro každou položku TODO a navažte její hodnotu na `IsDone` vlastnost. Změnit `@todo.Title` `@todo.Title`na prvekvázanýna:`<input>`

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Chcete-li ověřit, zda jsou tyto hodnoty vázány, aktualizujte `<h1>` záhlaví tak, aby zobrazovalo počet nedokončených položek TODO (`IsDone` je `false`).

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. Dokončená `Todo` součást (*Pages/todo. Razor*):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Znovu sestavte a spusťte aplikaci. Přidejte položky ToDo pro otestování nového kódu.

> [!div class="nextstepaction"]
> <xref:blazor/components>
