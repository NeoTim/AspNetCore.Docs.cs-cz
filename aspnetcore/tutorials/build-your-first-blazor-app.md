---
title: Vytvoření první aplikace v Blazor
author: guardrex
description: Sestavte aplikaci pro Blazor krok za krokem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: cc7caa1ee01e0282024895ab35c5b9933b1504d0
ms.sourcegitcommit: eb2fe5ad2e82fab86ca952463af8d017ba659b25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2019
ms.locfileid: "73416167"
---
# <a name="build-your-first-blazor-app"></a>Vytvoření první aplikace v Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

V tomto kurzu se dozvíte, jak vytvořit a upravit aplikaci v Blazor.

Podle pokynů v článku <xref:blazor/get-started> vytvořte projekt Blazor pro tento kurz. Pojmenujte projekt *ToDoList*.

## <a name="build-components"></a>Komponenty sestavení

1. Ve složce *Pages (stránky* ) přejděte na jednotlivé tři stránky aplikace: domů, čítač a načíst data. Tyto stránky jsou implementovány pomocí indexu souborů komponent Razor *. Razor*, *Counter. Razor*a *FetchData. Razor*.

1. Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky. Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu. Pomocí Blazor můžete místo toho napsat C# .

1. Projděte si implementaci součásti `Counter` v souboru *Counter. Razor* .

   *Stránky/čítač. Razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Uživatelské rozhraní komponenty `Counter` je definováno pomocí jazyka HTML. Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor). Logika značek HTML a C# vykreslování jsou v době sestavení převedena na třídu komponenty. Název generované třídy .NET se shoduje s názvem souboru.

   Členy třídy komponenty jsou definovány v bloku `@code`. V bloku `@code` jsou pro zpracování událostí nebo pro definování jiné logiky komponent určeny stav součásti (vlastnosti, pole) a metody. Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.

   Když je vybrané tlačítko pro **kliknutí na mou adresu** :

   * Zavolala se registrovaná obslužná rutina `onclick` komponenty `Counter` (metoda `IncrementCount`).
   * Komponenta `Counter` znovu vygeneruje svůj strom vykreslování.
   * Nový strom vykreslování je porovnán s předchozím.
   * Jsou aplikovány pouze změny model DOM (Document Object Model) (DOM). Zobrazený počet je aktualizovaný.

1. Upravte C# logiku součásti `Counter` tak, aby byl přírůstek počtu vynásoben dvěma místo na jednom.

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Pokud chcete zobrazit změny, sestavte a spusťte aplikaci. Vyberte tlačítko pro **kliknutí na tlačítko** . Čítače se zvýší o dva.

## <a name="use-components"></a>Použití komponent

Zahrnutí komponenty do jiné komponenty pomocí syntaxe jazyka HTML.

1. Přidejte komponentu `Counter` do komponenty `Index` aplikace přidáním prvku `<Counter />` do součásti `Index` (*index. Razor*).

   Pokud pro toto prostředí používáte Blazor WebAssembly, komponenta `Index` používá komponentu `SurveyPrompt`. Nahraďte prvek `<SurveyPrompt>` prvkem `<Counter />`. Pokud pro toto prostředí používáte aplikaci Blazor Server, přidejte do komponenty `Index` prvek `<Counter />`:

   *Pages/index. Razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Znovu sestavte a spusťte aplikaci. Komponenta `Index` má vlastní čítač.

## <a name="component-parameters"></a>Parametry součásti

Komponenty mohou mít také parametry. Parametry komponenty jsou definovány pomocí veřejných vlastností třídy Component s atributem `[Parameter]`. Použijte atributy k určení argumentů pro komponentu v kódu.

1. Aktualizujte kód `@code` C# komponenty:

   * Přidejte vlastnost Public `IncrementAmount` s atributem `[Parameter]`.
   * Změňte metodu `IncrementCount` tak, aby při zvyšování hodnoty `currentCount` používala `IncrementAmount`.

   *Stránky/čítač. Razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. Zadejte parametr `IncrementAmount` v prvku `<Counter>` komponenty `Index` s použitím atributu. Nastavte hodnotu pro zvýšení čítače o deset.

   *Pages/index. Razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Znovu načtěte komponentu `Index`. Čítač se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** . Čítač v komponentě `Counter` se stále zvyšuje o 1.

## <a name="route-to-components"></a>Směrování na součásti

Direktiva `@page` v horní části souboru *Counter. Razor* určuje, že komponenta `Counter` je koncový bod směrování. Komponenta `Counter` zpracovává požadavky odeslané do `/counter`. Bez direktivy `@page` součást nezpracovává směrované požadavky, ale komponentu mohou i nadále používat jiné komponenty.

## <a name="dependency-injection"></a>Injektáž závislostí

### <a name="blazor-server-experience"></a>Prostředí serveru Blazor

Pokud pracujete s aplikací serveru Blazor, je služba `WeatherForecastService` v `Startup.ConfigureServices` registrována jako typ [singleton](xref:fundamentals/dependency-injection#service-lifetimes) . Instance služby je k dispozici v celé aplikaci prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

Direktiva `@inject` se používá k vložení instance služby `WeatherForecastService` do komponenty `FetchData`.

*Stránky/FetchData. Razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Komponenta `FetchData` používá vloženou službu jako `ForecastService` k načtení pole objektů `WeatherForecast`:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Prostředí WebAssembly Blazor

Pokud pracujete s aplikací Blazor WebAssembly, je `HttpClient` vloženo pro získání dat předpovědi počasí ze souboru *počasí. JSON* ve složce *wwwroot/Sample-data* .

*Stránky/FetchData. Razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

K vykreslení každé instance prognózy jako řádku v tabulce dat o počasí se používá smyčka [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) :

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Sestavení seznamu TODO

Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.

1. Do aplikace ve složce *Pages* přidejte prázdný soubor s názvem *todo. Razor* :

1. Zadejte počáteční označení pro komponentu:

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. Přidejte komponentu `Todo` do navigačního panelu.

   Součást `NavMenu` (*Shared/NavMenu. Razor*) se používá v rozložení aplikace. Rozložení jsou komponenty, které umožňují vyhnout se duplikaci obsahu v aplikaci.

   Přidejte `<NavLink>` elementu pro komponentu `Todo` přidáním následujícího označení položky seznamu pod existující položky seznamu v souboru *Shared/NavMenu. Razor* :

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Znovu sestavte a spusťte aplikaci. Přejděte na stránku Nová TODO a potvrďte, že odkaz na komponentu `Todo` funguje.

1. Do kořenového adresáře projektu přidejte soubor *TodoItem.cs* , který bude obsahovat třídu, která představuje položku todo. Pro třídu `TodoItem` C# použijte následující kód:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Vraťte se do součásti `Todo` (*Pages/todo. Razor*):

   * Přidejte pole pro položky ToDo v bloku `@code`. Komponenta `Todo` používá toto pole k údržbě stavu seznamu úkolů.
   * Přidejte neuspořádané označení seznamu a smyčku `foreach` pro vykreslení každé položky ToDo jako položky seznamu (`<li>`).

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu. Přidejte textové zadání (`<input>`) a tlačítko (`<button>`) pod Neseřazený seznam (`<ul>...</ul>`):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Znovu sestavte a spusťte aplikaci. Když je vybráno tlačítko **Přidat TODO** , nic se nestane, protože obslužná rutina události není na tlačítko kabelem.

1. Přidejte metodu `AddTodo` do komponenty `Todo` a zaregistrujte ji pro výběry tlačítek pomocí atributu `@onclick`. Pokud je vybráno C# tlačítko, je volána metoda `AddTodo`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Chcete-li získat název nové položky ToDo, přidejte pole řetězce `newTodo` v horní části bloku `@code` a vytvořte jeho vazby k hodnotě textového zadání pomocí atributu `bind` v elementu `<input>`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aktualizujte metodu `AddTodo` a přidejte do seznamu `TodoItem` se zadaným názvem. Vymažte hodnotu textového zadání nastavením `newTodo` na prázdný řetězec:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Znovu sestavte a spusťte aplikaci. Chcete-li otestovat nový kód, přidejte do seznamu TODO některé položky ToDo.

1. Text nadpisu pro každou položku TODO lze upravovat a zaškrtávací políčko může uživatelům pomáhat sledovat dokončené položky. Přidejte vstup zaškrtávacího políčka pro každou položku TODO a navažte její hodnotu na vlastnost `IsDone`. Změňte `@todo.Title` na prvek `<input>` vázaný na `@todo.Title`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Chcete-li ověřit, zda jsou tyto hodnoty vázány, aktualizujte hlavičku `<h1>`, aby se zobrazil počet nedokončených položek TODO (`IsDone` je `false`).

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. Dokončená součást `Todo` (*Pages/todo. Razor*):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Znovu sestavte a spusťte aplikaci. Přidejte položky ToDo pro otestování nového kódu.

> [!div class="nextstepaction"]
> <xref:blazor/components>
