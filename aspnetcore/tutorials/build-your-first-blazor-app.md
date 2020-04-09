---
title: Vytvoření první Blazor aplikace
author: guardrex
description: Vytvořte Blazor aplikaci krok za krokem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
uid: tutorials/first-blazor-app
ms.openlocfilehash: 138057c2ceb9ed01bdf958c01f5cf2275387df23
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989435"
---
# <a name="build-your-first-opno-locblazor-app"></a>Vytvoření první Blazor aplikace

[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

V tomto kurzu se můžete Blazor stavit a upravit aplikaci.

## <a name="build-components"></a>Vytváření součástí

1. Postupujte podle <xref:blazor/get-started> pokynů v Blazor článku k vytvoření projektu pro tento kurz. Název projektu *ToDoList*.

1. Přejděte na všechny tři stránky aplikace ve složce *Stránky:* Domů, Čítač a Načíst data. Tyto stránky jsou implementovány razor dílčí soubory *Index.razor*, *Counter.razor*, a *FetchData.razor*.

1. Na stránce Čítač vyberte tlačítko Klikněte na **mě,** chcete-li čítač narůst bez aktualizace stránky. Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu. S Blazor, můžete napsat C# místo.

1. Zkontrolujte implementaci `Counter` součásti v souboru *Counter.razor.*

   *Stránky/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   UI komponenty `Counter` je definováno pomocí HTML. Logika dynamického vykreslování (například smyčky, podmínky, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem [Razor](xref:mvc/views/razor). Logika html a vykreslování Jazyka C# jsou převedeny na třídu komponenty v době sestavení. Název generované třídy .NET odpovídá názvu souboru.

   Členové třídy komponent jsou `@code` definováni v bloku. V `@code` bloku jsou pro zpracování událostí nebo pro definování jiné logiky komponenty určeny stav komponenty (vlastnosti, pole) a metody. Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.

   Když je vybráno tlačítko **Klikněte na mě:**

   * Je `Counter` volána `onclick` registrovaná obslužná rutina komponenty `IncrementCount` (metoda).
   * Komponenta `Counter` regeneruje svůj strom vykreslení.
   * Nový strom vykreslení je porovnán s předchozím stromem.
   * Použijí se pouze změny objektového modelu dokumentu (DOM). Zobrazený počet je aktualizován.

1. Upravte logiku C# komponenty `Counter` tak, aby se počet zvýšil o dva místo jednoho.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Znovu sestavte a spusťte aplikaci, abyste viděli změny. Vyberte **tlačítko Kliknout na tlačítko** . Čítač se ztoho čítač utápějí o dvě.

## <a name="use-components"></a>Použití součástí

Zahrnout komponentu do jiné součásti pomocí syntaxe HTML.

1. Přidejte `Counter` komponentu do `Index` součásti aplikace `<Counter />` přidáním `Index` prvku do komponenty (*Index.razor*).

   Pokud pro toto prostředí používáte Blazor `SurveyPrompt` WebAssembly, komponenta je komponentou `Index` používána. Nahraďte `<SurveyPrompt>` prvek `<Counter />` elementem. Pokud pro toto Blazor prostředí používáte aplikaci `<Counter />` Server, `Index` přidejte prvek do komponenty:

   *Stránky/Index.holicí strojek*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Znovu sestavte a spusťte aplikaci. Komponenta `Index` má svůj vlastní čítač.

## <a name="component-parameters"></a>Parametry komponenty

Komponenty mohou mít také parametry. Parametry komponenty jsou definovány pomocí veřejných vlastností třídy komponenty s atributem. `[Parameter]` Pomocí atributů můžete zadat argumenty pro komponentu ve značkách.

1. Aktualizujte kód `@code` C# komponenty takto:

   * Přidejte `IncrementAmount` veřejnou `[Parameter]` vlastnost s atributem.
   * Změňte `IncrementCount` metodu, `IncrementAmount` která má být `currentCount`používána vlastnost při zvyšování hodnoty .

   *Stránky/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Zadejte `IncrementAmount` parametr `Index` v elementu komponenty `<Counter>` pomocí atributu. Nastavte hodnotu na zvýšení čítače o deset.

   *Stránky/Index.holicí strojek*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Znovu načtěte `Index` součást. Čítač se při každém výběru tlačítka **Klikni mi** pozbytčí se deset. Čítač `Counter` v součásti se nadále zvedá o jednu.

## <a name="route-to-components"></a>Směrovat na součásti

Direktiva `@page` v horní části souboru `Counter` *Counter.razor* určuje, že komponenta je koncový bod směrování. Součást `Counter` zpracovává požadavky odeslané `/counter`společnosti . Bez `@page` směrnice součást nezpracovává směrované požadavky, ale komponenta může být stále používána jinými součástmi.

## <a name="dependency-injection"></a>Injektáž závislosti

### <a name="opno-locblazor-server-experience"></a>BlazorServerové prostředí

Pokud pracujete Blazor s aplikací `WeatherForecastService` Server, služba je registrována jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes) v aplikaci `Startup.ConfigureServices`. Instance služby je k dispozici v celé aplikaci prostřednictvím [vkládání závislostí (DI)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

Směrnice `@inject` se používá k vložení `WeatherForecastService` instance služby do komponenty. `FetchData`

*Stránky/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Komponenta `FetchData` používá vstřikované služby, jako `ForecastService`, načíst pole `WeatherForecast` objektů:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a>BlazorWebassembly zkušenosti

Pokud pracujete Blazor s aplikací `HttpClient` WebAssembly, je vložen získat data předpověď počasí ze souboru *weather.json* ve složce *wwwroot/sample-data.*

*Stránky/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Smyčka [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) se používá k vykreslení každé instance prognózy jako řádku v tabulce dat o počasí:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Sestavení seznamu úkolů

Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.

1. Přidejte `Todo` do aplikace novou komponentu Razor ve složce *Stránky.* V sadě Visual Studio klepněte pravým tlačítkem myši na složku **Stránky** a vyberte **přidat** > **komponentu Razor položky** > **Razor Component**. Pojmenujte soubor komponenty *Todo.razor*. V jiných vývojových prostředích přidejte prázdný soubor do složky **Stránky** s názvem *Todo.razor*.

1. Zadejte počáteční značky pro komponentu:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Přidejte `Todo` komponentu na navigační panel.

   Komponenta `NavMenu` (*Shared/NavMenu.razor*) se používá v rozložení aplikace. Rozložení jsou součásti, které umožňují vyhnout se duplikaci obsahu v aplikaci.

   Přidejte `<NavLink>` prvek `Todo` pro komponentu přidáním následujících značek položek seznamu pod existující položky seznamu v souboru *Shared/NavMenu.razor:*

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Znovu sestavte a spusťte aplikaci. Navštivte novou stránku Todo a potvrďte, že odkaz na komponentu `Todo` funguje.

1. Přidejte *TodoItem.cs* soubor do kořenového adresáře projektu pro uložení třídy, která představuje položku todo. Pro `TodoItem` třídu použijte následující kód jazyka C#:

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Zpět na `Todo` komponentu *(Pages/Todo.razor*):

   * Přidejte pole pro položky `@code` todo v bloku. Komponenta `Todo` používá toto pole k zachování stavu seznamu úkolů.
   * Přidejte neuspořádané značky seznamu a `foreach` smyčku, která`<li>`vykreslí každou položku úkolů jako položku seznamu ( ).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Aplikace vyžaduje prvky uživatelského rozhraní pro přidávání položek úkolů do seznamu. Přidejte textový`<input>`vstup ( )`<button>`a tlačítko (`<ul>...</ul>`) pod neuspořádaný seznam ( ):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Znovu sestavte a spusťte aplikaci. Když je vybráno tlačítko **Přidat todo,** nic se nestane, protože obslužná rutina události není připojena k tlačítku.

1. Přidejte `AddTodo` metodu `Todo` do komponenty a zaregistrujte `@onclick` ji pro výběry tlačítek pomocí atributu. Metoda `AddTodo` Jazyka C# je volána, když je tlačítko vybráno:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Chcete-li získat název nové položky `newTodo` todo, přidejte `@code` pole řetězce v horní části bloku a `bind` svázat `<input>` ji s hodnotou vstupu textu pomocí atributu v elementu:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aktualizujte `AddTodo` metodu `TodoItem` a přidejte do seznamu zadaný název. Zrušte hodnotu vstupu textu `newTodo` nastavením prázdného řetězce:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Znovu sestavte a spusťte aplikaci. Přidejte některé položky úkolů do seznamu úkolů a otestujte nový kód.

1. Název textu pro každou položku úkolů lze upravit a zaškrtávací políčko může uživateli pomoci sledovat dokončené položky. Přidejte vstup zaškrtávacího políčka pro `IsDone` každou položku todo a spojte její hodnotu s vlastností. Změna `@todo.Title` prvku `<input>` vázaného `@todo.Title`na :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Chcete-li ověřit, zda jsou `<h3>` tyto hodnoty svázány, aktualizujte záhlaví tak,`IsDone` aby `false`zobrazovala počet položek, které nejsou dokončeny ( je ).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Dokončená `Todo` komponenta (*Pages/Todo.razor*):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Znovu sestavte a spusťte aplikaci. Přidejte položky todo k testování nového kódu.

> [!div class="nextstepaction"]
> <xref:blazor/components>
