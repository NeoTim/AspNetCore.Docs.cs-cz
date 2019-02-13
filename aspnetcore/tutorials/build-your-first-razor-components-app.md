---
title: Vytvořte svoji první aplikaci součásti syntaxe Razor
author: guardrex
description: Vytvoření podrobné Razor komponent aplikace a seznamte se základními koncepty součásti syntaxe Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
uid: tutorials/first-razor-components-app
ms.openlocfilehash: 0c3dd2366581d73bad44e2911602e13c6c0daf9a
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159340"
---
# <a name="build-your-first-razor-components-app"></a>Vytvořte svoji první aplikaci součásti syntaxe Razor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

V tomto kurzu se dozvíte, jak vytvářet aplikace s komponentami Razor a ukazuje základní koncepty součásti syntaxe Razor. Můžete využívat tento kurz pomocí obou součásti Razor na základě projektu (podporované v .NET Core 3.0 nebo novější) nebo pomocí aplikace project na základě Blazor (podporované v budoucích verzích .NET Core).

Pro prostředí pomocí technologie ASP.NET Core Razor součásti (*doporučuje*):

* Postupujte podle pokynů v <xref:razor-components/get-started> k vytvoření projektu založeného na součásti syntaxe Razor.
* Pojmenujte projekt `RazorComponents`.
* Řešení pro více projektů je vytvořen z šablony Razor komponenty. Projekt součásti Razor je generován jako *RazorComponents.App*.

Pro prostředí pomocí Blazor:

* Postupujte podle pokynů v <xref:spa/blazor/get-started> k vytvoření projektu založeného na Blazor.
* Pojmenujte projekt `Blazor`.
* Řešení jednoho projektu je vytvořen z šablony Blazor.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([stažení](xref:index#how-to-download-a-sample)). Naleznete v následujících tématech pro požadavky:

## <a name="build-components"></a>Sestavení komponent

1. Přejděte do všech tří stránek vaší aplikace: Domů čítač a načíst data. Tyto stránky jsou implementovány v souborech Razor v *stránky* složky: *Index.cshtml*, *Counter.cshtml*, a *FetchData.cshtml*.

1. Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky. Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti Razor poskytuje lepší přístup pomocí C#.

1. Vyzkoušení implementace čítač součástí *Counter.cshtml* souboru.

   *Pages/Counter.cshtml*:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter1.cshtml)]

   Uživatelské rozhraní komponenty čítač je definován v jazyce HTML. Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor). Značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty v okamžiku sestavení. Název generované třídy .NET odpovídá názvu souboru.

   Členy třídy komponenty jsou definovány v `@functions` bloku. V `@functions` blokovat, stav komponent (vlastnosti, pole) a metody jsou určené pro zpracování událostí nebo definování dalších součástí logiky. Tyto členy, se použije jako součást logiky komponenty vykreslování a pro zpracování událostí.

   Když **klikněte na mě** výběru tlačítka:

   * Součást čítače zaregistrované `onclick` obslužná rutina volána ( `IncrementCount` metoda).
   * Součást čítače obnoví jeho vykreslení stromu.
   * Nový vykreslovací stromu je ve srovnání s předchozím histogramem.
   * Se použijí pouze změny do modelu Document Object Model (DOM). Počet zobrazených se aktualizuje.

1. Upravit C# logiky součást čítače aby přírůstek počtu dvou místo jednoho.

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter2.cshtml?highlight=14)]

1. Znovu sestavte a spusťte aplikaci, aby se změny projevily. Vyberte **klikněte na mě** tlačítko a zvýší čítač ve dvou.

## <a name="use-components"></a>Použití komponent

Zahrnout součásti do jiné součásti pomocí syntaxe HTML.

1. Přidat součást čítače pro součást aplikace indexu (Domovská stránka) tak, že přidáte `<Counter />` – element pro součást indexu.

   Pokud používáte Blazor pro toto prostředí, průzkum výzvy součásti (`<SurveyPrompt>` element) je v komponentě indexu. Nahraďte `<SurveyPrompt>` křížkem `<Counter>` elementu.

   *Pages/Index.cshtml*:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Index.cshtml?highlight=7)]

1. Znovu sestavte a spusťte aplikaci. Domovská stránka obsahuje vlastní čítače.

## <a name="component-parameters"></a>Parametry komponenty

Součástí mohou mít také parametry. Parametry komponenty jsou definovány pomocí vlastnosti neveřejné na komponentní třída dekorován `[Parameter]`. Atributy můžete zadat argumenty pro komponentu v kódu.

1. Aktualizace komponenty `@functions` C# kódu:

   * Přidat `IncrementAmount` vlastnost upravené pomocí `[Parameter]` atribut.
   * Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.

   *Pages/Counter.cshtml*:

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Counter.cshtml?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element. Nastavte hodnotu čítače přírůstku deset.

   *Pages/Index.cshtml*:

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Index.cshtml?highlight=7)]

1. Načtěte tuto stránku. Domovská stránka čítače se zvýší o hodnotu deset pokaždé, když **klikněte na mě** výběru tlačítka. Čítače na *čítač* stránce zvýší o jedna.

## <a name="route-to-components"></a>Směrovat do komponenty

`@page` Direktiv v horní části *Counter.cshtml* soubor Určuje, že tato součást je koncový bod směrování. Součást čítače zpracovává požadavky odeslané na `/Counter`. Bez `@page` direktiv, komponenta nebude zpracovávat směrování žádostí, ale součást je stále možné ostatními komponentami.

## <a name="dependency-injection"></a>Injektáž závislostí

Služby zaregistrované v kontejneru aplikace služby jsou k dispozici na komponenty prostřednictvím [injektáž závislostí (DI)](xref:fundamentals/dependency-injection). Vložit do součástí s použitím služby `@inject` směrnice.

Prozkoumat direktivy FetchData součásti (*Pages/FetchData.cshtml*). `@inject` Směrnice slouží k vložení instance `WeatherForecastService` služby do komponenty:

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=3)]

`WeatherForecastService` Není registrován jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes), takže jedna instance služby je k dispozici v rámci aplikace.

Komponenta FetchData používá vložený služby jako `ForecastService`, k načtení pole `WeatherForecast` objekty:

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData2.cshtml?highlight=6)]

A [ @foreach ](/dotnet/csharp/language-reference/keywords/foreach-in) smyčky se použije k vykreslení každou prognózy instanci jako řadu data o počasí v tabulce:

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData3.cshtml?highlight=11-19)]

## <a name="build-a-todo-list"></a>Vytvoření seznamu úkolů

Přidejte novou stránku do aplikace, která implementuje seznam úkolů.

1. Přidat prázdný soubor *stránky* složku s názvem *Todo.cshtml*.

1. Na stránce zadejte počáteční značky:

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. Přidáte stránku Todo do navigačního panelu.

   Komponenta NavMenu (*Shared/NavMenu.csthml*) se používá v rozložení aplikace. Rozložení jsou komponenty, které umožňují, aby se zabránilo duplicitě obsahu v aplikaci. Další informace naleznete v tématu <xref:razor-components/layouts>.

   Přidat `<NavLink>` Todo stránky tak, že přidáte následující značky položky seznamu níže existující položky seznamu v *Shared/NavMenu.csthml* souboru:

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Znovu sestavte a spusťte aplikaci. Na stránce Nový Todo potvrďte, že odkaz na stránku Todo funguje.

1. Přidat *TodoItem.cs* souboru do kořenového adresáře projektu k uložení třídu, která představuje položku seznamu úkolů. Pomocí následujících C# kód `TodoItem` třídy:

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/TodoItem.cs)]

1. Vraťte se do komponenty Todo (*Todo.cshtml*):

   * Přidání pole pro úloh, ať už v `@functions` bloku. Todo součásti používá toto pole pro uchování stavu pro seznam úkolů.
   * Přidat neuspořádaný seznam značek a `foreach` smyčky k vykreslení každé položky todo jako položku seznamu.

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData4.cshtml?highlight=5-10,12-14)]

1. Aplikace vyžaduje pro přidání do seznamu úloh, ať už prvky uživatelského rozhraní. Přidání textového zadání a tlačítka v seznamu níže:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData5.cshtml?highlight=12-13)]

1. Znovu sestavte a spusťte aplikaci. Když **přidat todo** se vybere tlačítko, nic se nestane, protože obslužná rutina události není svázanou tlačítka.

1. Přidat `AddTodo` metodu pro součást Todo a zaregistrujte ho pro tlačítko klikne pomocí `onclick` atribut:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData6.cshtml?highlight=2,7-10)]

   `AddTodo` C# Metoda je volána při výběru tlačítka.

1. Pokud chcete získat název nové položky seznamu úkolů, přidejte `newTodo` řetězec pole a navázat jej na hodnotu text input pomocí `bind` atribut:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData7.cshtml?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo" />
   ```

1. Aktualizace `AddTodo` způsob, jak přidat `TodoItem` se zadaným názvem do seznamu. Smazat hodnotu textového zadání tak, že nastavíte `newTodo` na prázdný řetězec:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData8.cshtml?highlight=19-26)]

1. Znovu sestavte a spusťte aplikaci. Přidání některých úloh, ať už do seznamu k testování nového kódu.

1. Text nadpisu pro každou položku seznamu úkolů lze upravovat a zaškrtávací políčko může pomoci udržovat přehled o dokončené položky uživatele. Přidat vstup zaškrtněte políčko u každé položky todo a její hodnotu na vytvoření vazby `IsDone` vlastnost. Změna `@todo.Title` do `<input>` prvek vázán na `@todo.Title`:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData9.cshtml?highlight=5-6)]

1. Chcete-li ověřit, že tyto hodnoty jsou vázány, aktualizujte `<h1>` záhlaví zobrazíte počet nesplněné položky seznamu úkolů nejsou kompletní (`IsDone` je `false`).

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. Dokončené komponenty Todo (*Todo.cshtml*):

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Todo.cshtml)]

1. Znovu sestavte a spusťte aplikaci. Přidání položky seznamu úkolů k testování nového kódu.

## <a name="publish-and-deploy-the-app"></a>Publikování a nasazení aplikace

Publikování aplikace, najdete v článku <xref:host-and-deploy/razor-components/index#publish-the-app>.
