---
title: Vytvoření první aplikace Blazor
author: guardrex
description: Sestavte Blazor aplikaci krok za krokem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/13/2020
no-loc:
- Blazor
uid: tutorials/first-blazor-app
ms.openlocfilehash: 8b3802a6ffe3613e5d4ca65c57fafc3f404c8329
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434496"
---
# <a name="build-your-first-opno-locblazor-app"></a>Vytvoření první aplikace Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

V tomto kurzu se dozvíte, jak vytvořit a upravit aplikaci Blazor.

## <a name="build-components"></a>Komponenty sestavení

1. Pokud chcete vytvořit projekt Blazor pro tento kurz, postupujte podle pokynů v článku o <xref:blazor/get-started>. Pojmenujte projekt *ToDoList*.

1. Ve složce *Pages (stránky* ) přejděte na jednotlivé tři stránky aplikace: domů, čítač a načíst data. Tyto stránky jsou implementovány pomocí indexu souborů komponent Razor *. Razor*, *Counter. Razor*a *FetchData. Razor*.

1. Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky. Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu. Pomocí Blazormůžete místo toho napsat C# .

1. Projděte si implementaci `Counter` komponenty v souboru *Counter. Razor* .

   *Stránky/čítač. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Uživatelské rozhraní součásti `Counter` je definováno pomocí jazyka HTML. Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor). Logika značek HTML a C# vykreslování jsou v době sestavení převedena na třídu komponenty. Název generované třídy .NET se shoduje s názvem souboru.

   Členy třídy komponenty jsou definovány v `@code`ovém bloku. V bloku `@code` jsou pro zpracování událostí nebo pro definování jiné logiky komponent určeny stav součásti (vlastnosti, pole) a metody. Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.

   Když je vybrané tlačítko pro **kliknutí na mou adresu** :

   * Je volána obslužná rutina registrované `onclick` `Counter` součásti (metoda `IncrementCount`).
   * Komponenta `Counter` znovu vygeneruje svůj strom vykreslování.
   * Nový strom vykreslování je porovnán s předchozím.
   * Jsou aplikovány pouze změny model DOM (Document Object Model) (DOM). Zobrazený počet je aktualizovaný.

1. Upravte C# logiku součásti `Counter` tak, aby byl přírůstek počtu vynásoben dvěma místo na jednom.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Pokud chcete zobrazit změny, sestavte a spusťte aplikaci. Vyberte tlačítko pro **kliknutí na tlačítko** . Čítače se zvýší o dva.

## <a name="use-components"></a>Použití komponent

Zahrnutí komponenty do jiné komponenty pomocí syntaxe jazyka HTML.

1. Přidejte komponentu `Counter` do komponenty `Index` aplikace přidáním prvku `<Counter />` do součásti `Index` (*index. Razor*).

   Pokud pro toto prostředí používáte Blazor WebAssembly, používá součást `Index` `SurveyPrompt` komponentu. Nahraďte prvek `<SurveyPrompt>` prvkem `<Counter />`. Pokud pro toto prostředí používáte aplikaci Blazor serveru, přidejte `<Counter />` prvek do `Index` komponenty:

   *Pages/index. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Znovu sestavte a spusťte aplikaci. Komponenta `Index` má vlastní čítač.

## <a name="component-parameters"></a>Parametry součásti

Komponenty mohou mít také parametry. Parametry komponenty jsou definovány pomocí veřejných vlastností třídy Component s atributem `[Parameter]`. Použijte atributy k určení argumentů pro komponentu v kódu.

1. Aktualizujte kód `@code` C# komponenty následujícím způsobem:

   * Přidejte vlastnost Public `IncrementAmount` s atributem `[Parameter]`.
   * Změňte metodu `IncrementCount` tak, aby při zvyšování hodnoty `currentCount`používala vlastnost `IncrementAmount`.

   *Stránky/čítač. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Zadejte `IncrementAmount` parametr v prvku `<Counter>` komponenty `Index` pomocí atributu. Nastavte hodnotu pro zvýšení čítače o deset.

   *Pages/index. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Znovu načtěte `Index` komponentu. Čítač se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** . Čítač ve `Counter` komponentě se dál zvyšuje o 1.

## <a name="route-to-components"></a>Směrování na součásti

Direktiva `@page` v horní části souboru *Counter. Razor* určuje, že `Counter` komponenta je koncový bod směrování. Komponenta `Counter` zpracovává požadavky odeslané do `/counter`. Bez direktivy `@page` nezpracovává komponenta směrované požadavky, ale komponentu mohou i nadále používat jiné komponenty.

## <a name="dependency-injection"></a>Injektáž závislosti

### <a name="opno-locblazor-server-experience"></a>prostředí serveru Blazor

Pokud pracujete s aplikací Blazor serveru, služba `WeatherForecastService` je v `Startup.ConfigureServices`registrovaná jako typ [singleton](xref:fundamentals/dependency-injection#service-lifetimes) . Instance služby je k dispozici v celé aplikaci prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

Direktiva `@inject` slouží k vložení instance služby `WeatherForecastService` do komponenty `FetchData`.

*Stránky/FetchData. Razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Komponenta `FetchData` používá vloženou službu jako `ForecastService`k načtení pole objektů `WeatherForecast`:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a>Blazor prostředí WebAssembly

Pokud pracujete s Blazor aplikace WebAssembly, `HttpClient` je vloženo pro získání dat předpovědi počasí ze souboru *počasí. JSON* ve složce *wwwroot/Sample-data* .

*Stránky/FetchData. Razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Smyčka [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) slouží k vykreslení každé instance prognózy jako řádku v tabulce dat o počasí:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Sestavení seznamu TODO

Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.

1. Do aplikace ve složce *Pages* přidejte novou `Todo` komponentu Razor. V aplikaci Visual Studio klikněte pravým tlačítkem myši na složku **stránky** a vyberte **Přidat** > **novou položku** > **součásti Razor**. Pojmenujte soubor komponenty *todo. Razor*. V jiných vývojových prostředích přidejte prázdný soubor do složky **Pages** s názvem *todo. Razor*.

1. Zadejte počáteční označení pro komponentu:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Přidejte komponentu `Todo` do navigačního panelu.

   Komponenta `NavMenu` (*Shared/NavMenu. Razor*) se používá v rozložení aplikace. Rozložení jsou komponenty, které umožňují vyhnout se duplikaci obsahu v aplikaci.

   Přidejte prvek `<NavLink>` pro komponentu `Todo` přidáním následujícího označení položky seznamu pod existující položky seznamu v souboru *Shared/NavMenu. Razor* :

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Znovu sestavte a spusťte aplikaci. Přejděte na stránku Nová TODO a potvrďte, že odkaz na `Todo` komponentu funguje.

1. Do kořenového adresáře projektu přidejte soubor *TodoItem.cs* , který bude obsahovat třídu, která představuje položku todo. Pro třídu `TodoItem` C# použijte následující kód:

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Vraťte se na součást `Todo` (*Pages/todo. Razor*):

   * Přidejte pole pro položky ToDo do `@code`ového bloku. Komponenta `Todo` používá toto pole k údržbě stavu seznamu úkolů.
   * Přidejte neuspořádané označení seznamu a `foreach` cyklus pro vykreslení každé položky ToDo jako položky seznamu (`<li>`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu. Přidejte textové zadání (`<input>`) a tlačítko (`<button>`) pod Neseřazený seznam (`<ul>...</ul>`):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Znovu sestavte a spusťte aplikaci. Když je vybráno tlačítko **Přidat TODO** , nic se nestane, protože obslužná rutina události není na tlačítko kabelem.

1. Přidejte metodu `AddTodo` do komponenty `Todo` a zaregistrujte ji pro výběry tlačítek pomocí atributu `@onclick`. Metoda `AddTodo` C# je volána, když je vybráno tlačítko:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Chcete-li získat název nové položky ToDo, přidejte do horní části bloku `@code` pole `newTodo` řetězec a navažte jej na hodnotu zadávání textu pomocí atributu `bind` v elementu `<input>`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aktualizujte metodu `AddTodo`, chcete-li přidat `TodoItem` se zadaným názvem do seznamu. Vymažte hodnotu textového zadání nastavením `newTodo` na prázdný řetězec:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Znovu sestavte a spusťte aplikaci. Chcete-li otestovat nový kód, přidejte do seznamu TODO některé položky ToDo.

1. Text nadpisu pro každou položku TODO lze upravovat a zaškrtávací políčko může uživatelům pomáhat sledovat dokončené položky. Přidejte vstup zaškrtávacího políčka pro každou položku TODO a navažte její hodnotu na vlastnost `IsDone`. Změňte `@todo.Title` na `<input>` prvek vázaný na `@todo.Title`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Chcete-li ověřit, zda jsou tyto hodnoty svázané, aktualizujte hlavičku `<h1>` tak, aby zobrazovala počet nedokončených položek TODO (`IsDone` je `false`).

   ```razor
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. Dokončená součást `Todo` (*Pages/todo. Razor*):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Znovu sestavte a spusťte aplikaci. Přidejte položky ToDo pro otestování nového kódu.

> [!div class="nextstepaction"]
> <xref:blazor/components>
