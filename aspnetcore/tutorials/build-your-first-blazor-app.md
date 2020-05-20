---
title: ' Vytvoření první Blazor aplikace ' Autor: Popis: ' vytvoření Blazor aplikace krok za krokem. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---
# <a name="build-your-first-blazor-app"></a>Sestavení první Blazor aplikace

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

V tomto kurzu se dozvíte, jak vytvořit a upravit Blazor aplikaci. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření Blazor projektu aplikace seznamu úkolů
> * Upravit Razor součásti
> * Použití zpracování událostí a datové vazby v součástech
> * Použití vkládání závislostí (DI) a směrování v Blazor aplikaci

Na konci tohoto kurzu budete mít funkční chatovací aplikaci.

## <a name="build-components"></a>Komponenty sestavení

1. Pokud <xref:blazor/get-started> chcete vytvořit Blazor projekt pro tento kurz, postupujte podle pokynů v článku. Pojmenujte projekt *ToDoList*.

1. Ve složce *Pages (stránky* ) přejděte na jednotlivé tři stránky aplikace: domů, čítač a načíst data. Tyto stránky jsou implementovány pomocí Razor indexu souborů komponenty *index. Razor*, *Counter. Razor*a *FetchData. Razor*.

1. Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky. Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu. Pomocí Blazor můžete místo toho napsat C#.

1. Projděte si implementaci `Counter` komponenty v souboru *Counter. Razor* .

   *Stránky/čítač. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Uživatelské rozhraní `Counter` komponenty je definováno pomocí jazyka HTML. Dynamická logika vykreslování (například cykly, podmíněné výrazy, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem [Razor](xref:mvc/views/razor) . Značky HTML značek a vykreslování v jazyce C# jsou převedeny na třídu komponenty v době sestavení. Název generované třídy .NET se shoduje s názvem souboru.

   Členy třídy komponenty jsou definovány v `@code` bloku. V `@code` bloku jsou pro zpracování událostí nebo pro definování jiné logiky komponent určeny stav součásti (vlastnosti, pole) a metody. Tyto členy se pak používají jako součást logiky vykreslování komponenty a pro zpracování událostí.

   Když je vybrané tlačítko pro **kliknutí na mou adresu** :

   * `Counter`Registrovaná `onclick` obslužná rutina komponenty je volána ( `IncrementCount` Metoda).
   * `Counter`Komponenta znovu vygeneruje svůj strom vykreslování.
   * Nový strom vykreslování je porovnán s předchozím.
   * Jsou aplikovány pouze změny model DOM (Document Object Model) (DOM). Zobrazený počet je aktualizovaný.

1. Upravte logiku komponenty jazyka C# `Counter` tak, aby byl přírůstek počtu vynásoben dvěma místomi jednoho.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Pokud chcete zobrazit změny, sestavte a spusťte aplikaci. Vyberte tlačítko pro **kliknutí na tlačítko** . Čítače se zvýší o dva.

## <a name="use-components"></a>Použití komponent

Zahrnutí komponenty do jiné komponenty pomocí syntaxe jazyka HTML.

1. Přidejte `Counter` komponentu do `Index` komponenty aplikace přidáním `<Counter />` prvku do `Index` komponenty (*index. Razor*).

   Pokud Blazor pro toto prostředí používáte WebAssembly, `SurveyPrompt` Komponenta je používána komponentou `Index` . Nahraďte `<SurveyPrompt>` element elementem `<Counter />` . Pokud Blazor pro toto prostředí používáte serverovou aplikaci, přidejte `<Counter />` element do `Index` komponenty:

   *Pages/index. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Znovu sestavte a spusťte aplikaci. `Index`Komponenta má vlastní čítač.

## <a name="component-parameters"></a>Parametry součásti

Komponenty mohou mít také parametry. Parametry komponenty jsou definovány pomocí veřejných vlastností třídy Component s `[Parameter]` atributem. Použijte atributy k určení argumentů pro komponentu v kódu.

1. Aktualizujte `@code` kód C# komponenty následujícím způsobem:

   * Přidejte veřejnou `IncrementAmount` vlastnost s `[Parameter]` atributem.
   * Změňte `IncrementCount` metodu na použití `IncrementAmount` vlastnosti při zvyšování hodnoty `currentCount` .

   *Stránky/čítač. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Zadejte `IncrementAmount` parametr v `Index` `<Counter>` elementu komponenty pomocí atributu. Nastavte hodnotu pro zvýšení čítače o deset.

   *Pages/index. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Znovu načtěte `Index` komponentu. Čítač se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** . Čítač v součásti se `Counter` stále zvyšuje o jednu.

## <a name="route-to-components"></a>Směrování na součásti

`@page`Direktiva v horní části souboru *Counter. Razor* určuje, že `Counter` Komponenta je koncový bod směrování. `Counter`Komponenta zpracovává požadavky odeslané na `/counter` . Bez `@page` direktivy nezpracovávají součásti směrované požadavky, ale komponentu mohou i nadále používat jiné komponenty.

## <a name="dependency-injection"></a>Injektáž závislostí

### <a name="blazor-server-experience"></a>BlazorProstředí serveru

Pokud pracujete se Blazor serverovou aplikací, `WeatherForecastService` služba je v nástroji registrována jako typ [singleton](xref:fundamentals/dependency-injection#service-lifetimes) `Startup.ConfigureServices` . Instance služby je k dispozici v celé aplikaci prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

`@inject`Direktiva slouží k vložení instance `WeatherForecastService` služby do `FetchData` komponenty.

*Stránky/FetchData. Razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

`FetchData`Komponenta používá vloženou službu jako `ForecastService` k načtení pole `WeatherForecast` objektů:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>BlazorProstředí WebAssembly

Při práci s Blazor aplikací WebAssembly `HttpClient` je vloženo pro získání dat předpovědi počasí ze souboru *počasí. JSON* ve složce *wwwroot/Sample-data* .

*Stránky/FetchData. Razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

[`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)Smyčka se používá k vykreslení každé instance prognózy jako řádku v tabulce dat o počasí:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Sestavení seznamu TODO

Přidejte do aplikace novou komponentu, která implementuje jednoduchý seznam úkolů.

1. Přidejte `Todo` Razor do aplikace novou komponentu ve složce *Pages* . V aplikaci Visual Studio klikněte pravým tlačítkem myši na složku **stránky** a vyberte možnost **Přidat**  >  **novou položku**  >  ** Razor Komponenta**. Pojmenujte soubor komponenty *todo. Razor*. V jiných vývojových prostředích přidejte prázdný soubor do složky **Pages** s názvem *todo. Razor*.

1. Zadejte počáteční označení pro komponentu:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Přidejte `Todo` komponentu do navigačního panelu.

   `NavMenu`Komponenta (*Shared/NavMenu. Razor*) se používá v rozložení aplikace. Rozložení jsou komponenty, které umožňují vyhnout se duplikaci obsahu v aplikaci.

   Přidejte `<NavLink>` element pro komponentu přidáním `Todo` následujícího označení položky seznamu pod existující položky seznamu v souboru *Shared/NavMenu. Razor* :

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Znovu sestavte a spusťte aplikaci. Přejděte na stránku Nová TODO a potvrďte, že odkaz na `Todo` komponentu funguje.

1. Do kořenového adresáře projektu přidejte soubor *TodoItem.cs* , který bude obsahovat třídu, která představuje položku todo. Pro třídu použijte následující kód jazyka C# `TodoItem` :

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Vraťte se do `Todo` komponenty (*Pages/todo. Razor*):

   * Přidejte pole pro položky ToDo v `@code` bloku. `Todo`Komponenta používá toto pole k údržbě stavu seznamu úkolů.
   * Přidejte neuspořádaný seznam značek a `foreach` smyčku pro vykreslení každé položky ToDo jako položky seznamu ( `<li>` ).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu. Přidejte textové zadání ( `<input>` ) a tlačítko ( `<button>` ) pod Neseřazený seznam ( `<ul>...</ul>` ):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Znovu sestavte a spusťte aplikaci. Když je vybráno tlačítko **Přidat TODO** , nic se nestane, protože obslužná rutina události není na tlačítko kabelem.

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

1. Dokončená `Todo` součást (*Pages/todo. Razor*):

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
> <xref:blazor/components>
