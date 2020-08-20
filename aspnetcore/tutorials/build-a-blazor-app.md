---
title: Sestavení Blazor aplikace seznamu úkolů
author: guardrex
description: Sestavte Blazor aplikaci krok za krokem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/30/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 769b3bda591252c51bec3ffd72a43eaa5929349e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630832"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>Sestavení Blazor aplikace seznamu úkolů

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

V tomto kurzu se dozvíte, jak vytvořit a upravit Blazor aplikaci. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření Blazor projektu aplikace seznamu úkolů
> * Upravit Razor součásti
> * Použití zpracování událostí a datové vazby v součástech
> * Použití směrování v Blazor aplikaci

Na konci tohoto kurzu budete mít funkční aplikaci seznamu úkolů.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a>Vytvoření aplikace seznamu úkolů Blazor

1. Vytvořte novou Blazor aplikaci s názvem `TodoList` v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Předchozí příkaz vytvoří složku s názvem `TodoList` pro uložení aplikace. Přejděte do složky adresáře `TodoList` pomocí následujícího příkazu:

   ```dotnetcli
   cd TodoList
   ```

1. Do této aplikace přidejte do `Todo` Razor složky novou komponentu `Pages` pomocí následujícího příkazu:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor názvy souborů součástí vyžadují první písmeno s velkými písmeny, proto ověřte, že `Todo` název souboru komponenty začíná velkým písmenem `T` .

1. V `Pages/Todo.razor` Zadejte počáteční označení pro komponentu:

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

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Vrátit se k `Todo` součásti ( `Pages/Todo.razor` ):

   * Přidejte pole pro položky ToDo v `@code` bloku. `Todo`Komponenta používá toto pole k údržbě stavu seznamu úkolů.
   * Přidejte neuspořádaný seznam značek a `foreach` smyčku pro vykreslení každé položky ToDo jako položky seznamu ( `<li>` ).

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu. Přidejte textové zadání ( `<input>` ) a tlačítko ( `<button>` ) pod Neseřazený seznam ( `<ul>...</ul>` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Znovu sestavte a spusťte aplikaci. Když **`Add todo`** je vybráno tlačítko, nic se nestane, protože obslužná rutina události není na tlačítko kabelem.

1. Přidejte `AddTodo` do `Todo` komponenty metodu a zaregistrujte ji pro výběry tlačítek pomocí `@onclick` atributu. `AddTodo`Metoda jazyka C# je volána, když je vybráno tlačítko:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Chcete-li získat název nové položky ToDo, přidejte `newTodo` pole řetězce v horní části `@code` bloku a vytvořte jeho svázání s hodnotou textového zadání pomocí `bind` atributu v `<input>` elementu:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aktualizujte `AddTodo` metodu tak, aby se do `TodoItem` seznamu přidal název se zadaným názvem. Vymažte hodnotu textového zadání nastavením `newTodo` na prázdný řetězec:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Znovu sestavte a spusťte aplikaci. Chcete-li otestovat nový kód, přidejte do seznamu TODO některé položky ToDo.

1. Text nadpisu pro každou položku TODO lze upravovat a zaškrtávací políčko může uživatelům pomáhat sledovat dokončené položky. Přidejte vstup zaškrtávacího políčka pro každou položku TODO a navažte její hodnotu na `IsDone` vlastnost. Změnit `@todo.Title` na `<input>` prvek vázaný na `@todo.Title` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Chcete-li ověřit, zda jsou tyto hodnoty vázány, aktualizujte `<h3>` záhlaví tak, aby zobrazovalo počet nedokončených položek TODO ( `IsDone` je `false` ).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Dokončená `Todo` součást ( `Pages/Todo.razor` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Znovu sestavte a spusťte aplikaci. Přidejte položky ToDo pro otestování nového kódu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření Blazor projektu aplikace seznamu úkolů
> * Upravit Razor součásti
> * Použití zpracování událostí a datové vazby v součástech
> * Použití směrování v Blazor aplikaci

Další informace o nástrojích pro ASP.NET Core Blazor :

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
