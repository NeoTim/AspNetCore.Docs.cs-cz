---
title: Začínáme s Razor komponenty
author: guardrex
description: Zjistěte, jak začít pracovat s komponentami Razor vytvořením a úpravou Razor součástí projektu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/03/2019
uid: razor-components/get-started
ms.openlocfilehash: a9ada603e5ed4e0e75c4aebc5105c331118666e6
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159321"
---
# <a name="get-started-with-razor-components"></a><span data-ttu-id="f0e6f-103">Začínáme s Razor komponenty</span><span class="sxs-lookup"><span data-stu-id="f0e6f-103">Get started with Razor Components</span></span>

<span data-ttu-id="f0e6f-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f0e6f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f0e6f-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0e6f-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f0e6f-106">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="f0e6f-107">Chcete-li vytvořit svůj první projekt Razor komponenty v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-107">To create your first Razor Components project in Visual Studio:</span></span>

1. <span data-ttu-id="f0e6f-108">Vyberte **souboru** > **nový projekt** > **webové** > **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-108">Select **File** > **New Project** > **Web** > **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="f0e6f-109">Ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány v horní části.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-109">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="f0e6f-110">Zvolte **Razor komponenty** šablony a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-110">Choose the **Razor Components** template and select **OK**.</span></span>

   ![Dialogové okno nové aplikace](https://msdnshared.blob.core.windows.net/media/2019/01/razor-components-template.png)

1. <span data-ttu-id="f0e6f-112">Stisknutím klávesy **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-112">Press **F5** to run the app.</span></span>

<span data-ttu-id="f0e6f-113">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="f0e6f-113">Congratulations!</span></span> <span data-ttu-id="f0e6f-114">Právě jste spustili svou první aplikaci Razor součásti!</span><span class="sxs-lookup"><span data-stu-id="f0e6f-114">You just ran your first Razor Components app!</span></span>

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Razor Components project in Visual Studio Code:

1. Execute the following command from a command shell:

   ```console
   dotnet new razorcomponents -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Add a *.vscode* folder.

1. Add a *tasks.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/tasks.json)]

1. Add a *launch.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/launch.json)]

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Razor Components app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.


[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Razor Components project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **ASP.NET Core Razor Components** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Razor Components app!
-->

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f0e6f-115">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="f0e6f-115">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f0e6f-116">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-116">Prerequisites:</span></span>

* [<span data-ttu-id="f0e6f-117">.NET core SDK 3.0 ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="f0e6f-117">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="f0e6f-118">Chcete vytvořit svůj první projekt Razor součásti z příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-118">To create your first Razor Components project from a command shell:</span></span>

   ```console
   dotnet new razorcomponents -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="f0e6f-119">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-119">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="f0e6f-120">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="f0e6f-120">Congratulations!</span></span> <span data-ttu-id="f0e6f-121">Právě jste spustili svou první aplikaci Razor součásti!</span><span class="sxs-lookup"><span data-stu-id="f0e6f-121">You just ran your first Razor Components app!</span></span>

---

## <a name="razor-components-project"></a><span data-ttu-id="f0e6f-122">Projekt součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="f0e6f-122">Razor Components project</span></span>

<span data-ttu-id="f0e6f-123">Vytvořený pomocí šablony Razor komponenty řešení obsahuje dva projekty:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-123">The solution created by the Razor Components template contains two projects:</span></span>

* <span data-ttu-id="f0e6f-124">*WebApplication1.Server* &ndash; serverový projekt je projekt ASP.NET Core nastavení pro hostování aplikace součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-124">*WebApplication1.Server* &ndash; The server project is an ASP.NET Core project set up to host the Razor Components app.</span></span>
* <span data-ttu-id="f0e6f-125">*WebApplication1.App* &ndash; na straně klienta webového projektu uživatelského rozhraní, která používá součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-125">*WebApplication1.App* &ndash; The client-side web UI project that uses Razor Components.</span></span>

<span data-ttu-id="f0e6f-126">Logika uživatelského rozhraní *WebApplication1.App* projektu je oddělené od zbytku aplikace kvůli technická omezení v ASP.NET Core 3.0 ve verzi Preview 2.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-126">The UI logic in the *WebApplication1.App* project is separated from the rest of the app due to a technical limitation in ASP.NET Core 3.0 Preview 2.</span></span> <span data-ttu-id="f0e6f-127">Přípona souboru Razor (*.cshtml*) použít pro Razor komponenty se také používá pro zobrazení Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-127">The Razor file extension (*.cshtml*) used for Razor Components is also used for Razor Pages and MVC views.</span></span> <span data-ttu-id="f0e6f-128">V současné době Razor součásti a Razor Pages/MVC mají různé kompilace modely, tak soubory Razor Razor komponenty jsou udržovány odděleně.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-128">Currently, Razor Components and Razor Pages/MVC have different compilation models, so the Razor Components Razor files are kept separate.</span></span> <span data-ttu-id="f0e6f-129">V budoucí verzi preview, plánujeme zavést novou příponu souboru pro Razor součásti (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="f0e6f-129">In a future preview, we plan to introduce a new file extension for Razor Components (*.razor*).</span></span> <span data-ttu-id="f0e6f-130">Součásti, stránky a zobrazení se hostovat *ve stejném projektu*.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-130">Components, pages, and views will be hosted *in the same project*.</span></span>

<span data-ttu-id="f0e6f-131">Při spuštění aplikace jsou k dispozici z karty na bočním panelu více stránek:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-131">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="f0e6f-132">Domů</span><span class="sxs-lookup"><span data-stu-id="f0e6f-132">Home</span></span>
* <span data-ttu-id="f0e6f-133">Čítač</span><span class="sxs-lookup"><span data-stu-id="f0e6f-133">Counter</span></span>
* <span data-ttu-id="f0e6f-134">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="f0e6f-134">Fetch data</span></span>

<span data-ttu-id="f0e6f-135">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-135">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="f0e6f-136">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti Razor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-136">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

<span data-ttu-id="f0e6f-137">*WebApplication1.App/Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-137">*WebApplication1.App/Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

<span data-ttu-id="f0e6f-138">Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-138">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="f0e6f-139">Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-139">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="f0e6f-140">Pokaždé, když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-140">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="f0e6f-141">`onclick` Událost se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-141">The `onclick` event is fired.</span></span>
* <span data-ttu-id="f0e6f-142">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-142">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="f0e6f-143">`currentCount` Se zvýší.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-143">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="f0e6f-144">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-144">The component is rendered again.</span></span>

<span data-ttu-id="f0e6f-145">Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="f0e6f-145">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="f0e6f-146">Přidáte součást do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-146">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="f0e6f-147">Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-147">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="f0e6f-148">Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-148">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="f0e6f-149">*WebApplication1.App/Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-149">*WebApplication1.App/Pages/Index.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

<span data-ttu-id="f0e6f-150">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-150">Run the app.</span></span> <span data-ttu-id="f0e6f-151">Na domovskou stránku má svůj vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-151">The homepage has its own counter.</span></span>

<span data-ttu-id="f0e6f-152">Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-152">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="f0e6f-153">Přidání vlastnosti pro `IncrementAmount` dekorován `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-153">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="f0e6f-154">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-154">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="f0e6f-155">*WebApplication1.App/Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-155">*WebApplication1.App/Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

<span data-ttu-id="f0e6f-156">Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-156">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="f0e6f-157">*WebApplication1.App/Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f0e6f-157">*WebApplication1.App/Pages/Index.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

<span data-ttu-id="f0e6f-158">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-158">Run the app.</span></span> <span data-ttu-id="f0e6f-159">Na domovskou stránku má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="f0e6f-159">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f0e6f-160">Další kroky</span><span class="sxs-lookup"><span data-stu-id="f0e6f-160">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
