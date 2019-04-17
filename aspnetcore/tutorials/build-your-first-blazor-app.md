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
# <a name="build-your-first-blazor-app"></a>Vytvořte svoji první aplikaci Blazor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

V tomto kurzu se dozvíte, jak vytvářet aplikace pomocí syntaxe Razor komponenty na straně serveru nebo Blazor na straně klienta.

Pro prostředí pomocí technologie ASP.NET Core Razor součástmi (*doporučuje*):

* Postupujte podle *prostředí Razor komponenty* podle pokynů uvedených v <xref:blazor/get-started#server-side-razor-components-experience> k vytvoření projektu založeného na součásti syntaxe Razor.
* Pojmenujte projekt `RazorComponents`.

Pro prostředí pomocí Blazor:

* Postupujte podle *Blazor prostředí* podle pokynů uvedených v <xref:blazor/get-started#client-side-blazor-experience> k vytvoření projektu založeného na Blazor.
* Pojmenujte projekt `Blazor`.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([stažení](xref:index#how-to-download-a-sample)). Naleznete v následujících tématech pro požadavky:

## <a name="build-components"></a>Sestavení komponent

1. Přejděte do všech tří stránek vaší aplikace v *součásti/stránky* složky (*stránky* v Blazor): Domů čítač a načíst data. Tyto stránky jsou implementovány v souborech Razor komponenty: *Index.Razor*, *Counter.razor*, a *FetchData.razor*. (Blazor dál používat *.cshtml* příponu souboru: *Index.cshtml*, *Counter.cshtml*, a *FetchData.cshtml*.)

1. Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky. Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale Blazor poskytuje lepší přístup pomocí C#.

1. Vyzkoušení implementace čítač součástí *Counter.razor* souboru.

   *Components/Pages/Counter.razor* (*Pages/Counter.cshtml* v Blazor):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Uživatelské rozhraní komponenty čítač je definován v jazyce HTML. Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor). Značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty v okamžiku sestavení. Název generované třídy .NET odpovídá názvu souboru.

   Členy třídy komponenty jsou definovány v `@functions` bloku. V `@functions` blokovat, stav komponent (vlastnosti, pole) a metody jsou určené pro zpracování událostí nebo definování dalších součástí logiky. Tyto členy, se použije jako součást logiky komponenty vykreslování a pro zpracování událostí.

   Když **klikněte na mě** výběru tlačítka:

   * Součást čítače zaregistrované `onclick` obslužná rutina volána ( `IncrementCount` metoda).
   * Součást čítače obnoví jeho vykreslení stromu.
   * Nový vykreslovací stromu je ve srovnání s předchozím histogramem.
   * Se použijí pouze změny do modelu Document Object Model (DOM). Počet zobrazených se aktualizuje.

1. Upravit C# logiky součást čítače aby přírůstek počtu dvou místo jednoho.

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Znovu sestavte a spusťte aplikaci, aby se změny projevily. Vyberte **klikněte na mě** tlačítko a zvýší čítač ve dvou.

## <a name="use-components"></a>Použití komponent

Zahrnout součásti do jiné součásti pomocí syntaxe HTML.

1. Přidat součást čítače pro součást aplikace indexu (domů) tak, že přidáte `<Counter />` – element pro součást indexu.

   Pokud používáte Blazor pro toto prostředí, průzkum výzvy součásti (`<SurveyPrompt>` element) je v komponentě indexu. Nahraďte `<SurveyPrompt>` křížkem `<Counter>` elementu.

   *Components/Pages/Index.razor* (*Pages/Index.cshtml* v Blazor):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index.razor?highlight=7)]

1. Znovu sestavte a spusťte aplikaci. Domovská stránka obsahuje vlastní čítače.

## <a name="component-parameters"></a>Parametry komponenty

Součástí mohou mít také parametry. Parametry komponenty jsou definovány pomocí vlastnosti neveřejné na komponentní třída dekorován `[Parameter]`. Atributy můžete zadat argumenty pro komponentu v kódu.

1. Aktualizace komponenty `@functions` C# kódu:

   * Přidat `IncrementAmount` vlastnost upravené pomocí `[Parameter]` atribut.
   * Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.

   *Components/Pages/Counter.razor* (*Pages/Counter.cshtml* v Blazor):

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Counter.razor?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element. Nastavte hodnotu čítače přírůstku deset.

   *Components/Pages/Index.razor* (*Pages/Index.cshtml* v Blazor):

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Index.razor?highlight=7)]

1. Znovu načte na domovské stránce. Hodnota čítače se zvýší o hodnotu deset pokaždé, když **klikněte na mě** výběru tlačítka. Čítače na stránku zvýší čítač jednou.

## <a name="route-to-components"></a>Směrovat do komponenty

`@page` Direktiv v horní části *Counter.razor* soubor Určuje, že tato součást je koncový bod směrování. Součást čítače zpracovává požadavky odeslané na `/Counter`. Bez `@page` direktiv, komponenta nebude zpracovávat směrování žádostí, ale součást je stále možné ostatními komponentami.

## <a name="dependency-injection"></a>Injektáž závislostí

Služby zaregistrované v kontejneru aplikace služby jsou k dispozici na komponenty prostřednictvím [injektáž závislostí (DI)](xref:fundamentals/dependency-injection). Vložit do součástí s použitím služby `@inject` směrnice.

Prozkoumejte direktivy FetchData součásti v ukázkové aplikaci.

V ukázkové aplikaci Razor komponenty `WeatherForecastService` není registrován jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes), takže jedna instance služby je k dispozici v rámci aplikace. `@inject` Směrnice slouží k vložení instance `WeatherForecastService` služby do komponenty.

*Components/Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Komponenta FetchData používá vložený služby jako `ForecastService`, k načtení pole `WeatherForecast` objekty:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

Ve verzi Blazor ukázkové aplikace `HttpClient` se vloží získávat data předpovědi počasí z *weather.json* soubor *wwwroot/ukázková data* složky:

*Pages/FetchData.cshtml*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=7)]

V obou ukázkové aplikace [ @foreach ](/dotnet/csharp/language-reference/keywords/foreach-in) smyčky se použije k vykreslení každou prognózy instanci jako řadu data o počasí v tabulce:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Vytvoření seznamu úkolů

Přidáte novou součást aplikaci, která implementuje seznam úkolů.

1. Přidáte prázdný soubor na ukázkovou aplikaci:

   * Pro prostředí Razor součásti přidat *Todo.razor* do souboru *součásti/stránky* složky.
   * Prostředí Blazor přidat *Todo.cshtml* do souboru *stránky* složky.

1. Zadejte počáteční značka pro komponentu:

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. Přidáte součást Todo do navigačního panelu.

   Komponenta NavMenu (*Components/Shared/NavMenu.razor* nebo *Shared/NavMenu.cshtml* v Blazor) se používá v rozložení aplikace. Rozložení jsou komponenty, které umožňují, aby se zabránilo duplicitě obsahu v aplikaci. Další informace naleznete v tématu <xref:blazor/layouts>.

   Přidat `<NavLink>` pro komponentu Todo tak, že přidáte následující značky položky seznamu níže existující položky seznamu v *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* v Blazor) souboru:

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Znovu sestavte a spusťte aplikaci. Na stránce Nový Todo potvrďte, že odkaz na komponentu Todo funguje.

1. Přidat *TodoItem.cs* souboru do kořenového adresáře projektu k uložení třídu, která představuje položku seznamu úkolů. Pomocí následujících C# kód `TodoItem` třídy:

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/TodoItem.cs)]

1. Vraťte se do komponenty Todo (*Components/Pages/Todo.razor* nebo *Pages/Todo.cshtml* v Blazor):

   * Přidání pole pro úloh, ať už v `@functions` bloku. Todo součásti používá toto pole pro uchování stavu pro seznam úkolů.
   * Přidat neuspořádaný seznam značek a `foreach` smyčky k vykreslení každé položky todo jako položku seznamu.

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Aplikace vyžaduje pro přidání do seznamu úloh, ať už prvky uživatelského rozhraní. Přidání textového zadání a tlačítka v seznamu níže:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Znovu sestavte a spusťte aplikaci. Když **přidat todo** se vybere tlačítko, nic se nestane, protože obslužná rutina události není svázanou tlačítka.

1. Přidat `AddTodo` metodu pro součást Todo a zaregistrujte ho pro tlačítko klikne pomocí `onclick` atribut:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   `AddTodo` C# Metoda je volána při výběru tlačítka.

1. Pokud chcete získat název nové položky seznamu úkolů, přidejte `newTodo` řetězec pole a navázat jej na hodnotu text input pomocí `bind` atribut:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. Aktualizace `AddTodo` způsob, jak přidat `TodoItem` se zadaným názvem do seznamu. Smazat hodnotu textového zadání tak, že nastavíte `newTodo` na prázdný řetězec:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Znovu sestavte a spusťte aplikaci. Přidání některých úloh, ať už do seznamu k testování nového kódu.

1. Text nadpisu pro každou položku seznamu úkolů lze upravovat a zaškrtávací políčko může pomoci udržovat přehled o dokončené položky uživatele. Přidat vstup zaškrtněte políčko u každé položky todo a její hodnotu na vytvoření vazby `IsDone` vlastnost. Změna `@todo.Title` do `<input>` prvek vázán na `@todo.Title`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Chcete-li ověřit, že tyto hodnoty jsou vázány, aktualizujte `<h1>` záhlaví zobrazíte počet nesplněné položky seznamu úkolů nejsou kompletní (`IsDone` je `false`).

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. Dokončené komponenty Todo (*Components/Pages/Todo.razor* nebo *Pages/Todo.cshtml* v Blazor):

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Todo.razor)]

1. Znovu sestavte a spusťte aplikaci. Přidání položky seznamu úkolů k testování nového kódu.

## <a name="publish-and-deploy-the-app"></a>Publikování a nasazení aplikace

Publikování aplikace, najdete v článku <xref:host-and-deploy/blazor/index>.
