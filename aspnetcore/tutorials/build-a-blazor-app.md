---
title: Sestavení Blazor aplikace seznamu úkolů
author: guardrex
description: Sestavte Blazor aplikaci krok za krokem.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/30/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: b58c0c3db685d0a5b082aaf2eb434635bd10e2eb
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444041"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="f81da-103">Sestavení Blazor aplikace seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="f81da-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="f81da-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f81da-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f81da-105">V tomto kurzu se dozvíte, jak vytvořit a upravit Blazor aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f81da-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="f81da-106">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="f81da-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f81da-107">Vytvoření Blazor projektu aplikace seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="f81da-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="f81da-108">Upravit Razor součásti</span><span class="sxs-lookup"><span data-stu-id="f81da-108">Modify Razor components</span></span>
> * <span data-ttu-id="f81da-109">Použití zpracování událostí a datové vazby v součástech</span><span class="sxs-lookup"><span data-stu-id="f81da-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="f81da-110">Použití směrování v Blazor aplikaci</span><span class="sxs-lookup"><span data-stu-id="f81da-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="f81da-111">Na konci tohoto kurzu budete mít funkční aplikaci seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="f81da-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f81da-112">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="f81da-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="f81da-113">Vytvoření aplikace seznamu úkolů Blazor</span><span class="sxs-lookup"><span data-stu-id="f81da-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="f81da-114">Vytvořte novou Blazor aplikaci s názvem `TodoList` v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="f81da-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="f81da-115">Předchozí příkaz vytvoří složku s názvem `TodoList` pro uložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f81da-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="f81da-116">Přejděte do složky adresáře `TodoList` pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="f81da-116">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="f81da-117">Do této aplikace přidejte do `Todo` Razor složky novou komponentu `Pages` pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="f81da-117">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="f81da-118">Razornázvy souborů součástí vyžadují první písmeno s velkými písmeny, proto ověřte, že `Todo` název souboru komponenty začíná velkým písmenem `T` .</span><span class="sxs-lookup"><span data-stu-id="f81da-118">Razor component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="f81da-119">V `Pages/Todo.razor` Zadejte počáteční označení pro komponentu:</span><span class="sxs-lookup"><span data-stu-id="f81da-119">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="f81da-120">Přidejte `Todo` komponentu do navigačního panelu.</span><span class="sxs-lookup"><span data-stu-id="f81da-120">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="f81da-121">`NavMenu`Součást ( `Shared/NavMenu.razor` ) se používá v rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f81da-121">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="f81da-122">Rozložení jsou komponenty, které umožňují vyhnout se duplikaci obsahu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f81da-122">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="f81da-123">Přidejte `<NavLink>` element pro komponentu přidáním `Todo` následujícího označení položky seznamu pod existující položky seznamu v `Shared/NavMenu.razor` souboru:</span><span class="sxs-lookup"><span data-stu-id="f81da-123">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="f81da-124">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f81da-124">Rebuild and run the app.</span></span> <span data-ttu-id="f81da-125">Přejděte na stránku Nová TODO a potvrďte, že odkaz na `Todo` komponentu funguje.</span><span class="sxs-lookup"><span data-stu-id="f81da-125">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="f81da-126">Do `TodoItem.cs` kořenu projektu přidejte soubor, který bude obsahovat třídu, která představuje položku todo.</span><span class="sxs-lookup"><span data-stu-id="f81da-126">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="f81da-127">Pro třídu použijte následující kód jazyka C# `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="f81da-127">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="f81da-128">Vrátit se k `Todo` součásti ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="f81da-128">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="f81da-129">Přidejte pole pro položky ToDo v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="f81da-129">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="f81da-130">`Todo`Komponenta používá toto pole k údržbě stavu seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="f81da-130">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="f81da-131">Přidejte neuspořádaný seznam značek a `foreach` smyčku pro vykreslení každé položky ToDo jako položky seznamu ( `<li>` ).</span><span class="sxs-lookup"><span data-stu-id="f81da-131">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="f81da-132">Aplikace vyžaduje prvky uživatelského rozhraní pro přidání položek TODO do seznamu.</span><span class="sxs-lookup"><span data-stu-id="f81da-132">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="f81da-133">Přidejte textové zadání ( `<input>` ) a tlačítko ( `<button>` ) pod Neseřazený seznam ( `<ul>...</ul>` ):</span><span class="sxs-lookup"><span data-stu-id="f81da-133">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="f81da-134">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f81da-134">Rebuild and run the app.</span></span> <span data-ttu-id="f81da-135">Když **`Add todo`** je vybráno tlačítko, nic se nestane, protože obslužná rutina události není na tlačítko kabelem.</span><span class="sxs-lookup"><span data-stu-id="f81da-135">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="f81da-136">Přidejte `AddTodo` do `Todo` komponenty metodu a zaregistrujte ji pro výběry tlačítek pomocí `@onclick` atributu.</span><span class="sxs-lookup"><span data-stu-id="f81da-136">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="f81da-137">`AddTodo`Metoda jazyka C# je volána, když je vybráno tlačítko:</span><span class="sxs-lookup"><span data-stu-id="f81da-137">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="f81da-138">Chcete-li získat název nové položky ToDo, přidejte `newTodo` pole řetězce v horní části `@code` bloku a vytvořte jeho svázání s hodnotou textového zadání pomocí `bind` atributu v `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="f81da-138">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="f81da-139">Aktualizujte `AddTodo` metodu tak, aby se do `TodoItem` seznamu přidal název se zadaným názvem.</span><span class="sxs-lookup"><span data-stu-id="f81da-139">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="f81da-140">Vymažte hodnotu textového zadání nastavením `newTodo` na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="f81da-140">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="f81da-141">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f81da-141">Rebuild and run the app.</span></span> <span data-ttu-id="f81da-142">Chcete-li otestovat nový kód, přidejte do seznamu TODO některé položky ToDo.</span><span class="sxs-lookup"><span data-stu-id="f81da-142">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="f81da-143">Text nadpisu pro každou položku TODO lze upravovat a zaškrtávací políčko může uživatelům pomáhat sledovat dokončené položky.</span><span class="sxs-lookup"><span data-stu-id="f81da-143">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="f81da-144">Přidejte vstup zaškrtávacího políčka pro každou položku TODO a navažte její hodnotu na `IsDone` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f81da-144">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="f81da-145">Změnit `@todo.Title` na `<input>` prvek vázaný na `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="f81da-145">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="f81da-146">Chcete-li ověřit, zda jsou tyto hodnoty vázány, aktualizujte `<h3>` záhlaví tak, aby zobrazovalo počet nedokončených položek TODO ( `IsDone` je `false` ).</span><span class="sxs-lookup"><span data-stu-id="f81da-146">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="f81da-147">Dokončená `Todo` součást ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="f81da-147">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="f81da-148">Znovu sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f81da-148">Rebuild and run the app.</span></span> <span data-ttu-id="f81da-149">Přidejte položky ToDo pro otestování nového kódu.</span><span class="sxs-lookup"><span data-stu-id="f81da-149">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f81da-150">Další kroky</span><span class="sxs-lookup"><span data-stu-id="f81da-150">Next steps</span></span>

<span data-ttu-id="f81da-151">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="f81da-151">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f81da-152">Vytvoření Blazor projektu aplikace seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="f81da-152">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="f81da-153">Upravit Razor součásti</span><span class="sxs-lookup"><span data-stu-id="f81da-153">Modify Razor components</span></span>
> * <span data-ttu-id="f81da-154">Použití zpracování událostí a datové vazby v součástech</span><span class="sxs-lookup"><span data-stu-id="f81da-154">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="f81da-155">Použití směrování v Blazor aplikaci</span><span class="sxs-lookup"><span data-stu-id="f81da-155">Use routing in a Blazor app</span></span>

<span data-ttu-id="f81da-156">Další informace o nástrojích pro ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="f81da-156">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
