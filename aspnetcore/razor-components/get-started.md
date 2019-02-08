---
title: Začínáme s Razor komponenty
author: guardrex
description: Zjistěte, jak začít pracovat s rozhraní komponenty Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/03/2019
uid: razor-components/get-started
ms.openlocfilehash: d35245135139adbb5c0cb63b7d8e309a74948da8
ms.sourcegitcommit: b72bbc9ae91e4bd37c9ea9b2d09ebf47afb25dd7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958106"
---
# <a name="get-started-with-razor-components"></a><span data-ttu-id="619a4-103">Začínáme s Razor komponenty</span><span class="sxs-lookup"><span data-stu-id="619a4-103">Get started with Razor Components</span></span>

<span data-ttu-id="619a4-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="619a4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="619a4-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="619a4-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="619a4-106">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="619a4-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="619a4-107">Chcete-li vytvořit svůj první projekt Razor komponenty v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="619a4-107">To create your first Razor Components project in Visual Studio:</span></span>

1. <span data-ttu-id="619a4-108">Vyberte **souboru** > **nový projekt** > **webové** > **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="619a4-108">Select **File** > **New Project** > **Web** > **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="619a4-109">Ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány v horní části.</span><span class="sxs-lookup"><span data-stu-id="619a4-109">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="619a4-110">Zvolte **Razor komponenty** šablony a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="619a4-110">Choose the **Razor Components** template and select **OK**.</span></span>

   ![Dialogové okno nové aplikace](https://msdnshared.blob.core.windows.net/media/2019/01/razor-components-template.png)

1. <span data-ttu-id="619a4-112">Stisknutím klávesy **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="619a4-112">Press **F5** to run the app.</span></span>

<span data-ttu-id="619a4-113">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="619a4-113">Congratulations!</span></span> <span data-ttu-id="619a4-114">Právě jste spustili svou první aplikaci Razor součásti!</span><span class="sxs-lookup"><span data-stu-id="619a4-114">You just ran your first Razor Components app!</span></span>

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

Congrats! You just ran your first Razor Components app!

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

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="619a4-115">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="619a4-115">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="619a4-116">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="619a4-116">Prerequisites:</span></span>

* [<span data-ttu-id="619a4-117">.NET core SDK 3.0 ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="619a4-117">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

<span data-ttu-id="619a4-118">Chcete vytvořit svůj první projekt Razor součásti z příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="619a4-118">To create your first Razor Components project from a command shell:</span></span>

```console
dotnet new razorcomponents -o WebApplication1
cd WebApplication1
dotnet run
```

<span data-ttu-id="619a4-119">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="619a4-119">Congratulations!</span></span> <span data-ttu-id="619a4-120">Právě jste spustili svou první aplikaci Razor součásti!</span><span class="sxs-lookup"><span data-stu-id="619a4-120">You just ran your first Razor Components app!</span></span>

---

## <a name="razor-components-project"></a><span data-ttu-id="619a4-121">Projekt součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="619a4-121">Razor Components project</span></span>

<span data-ttu-id="619a4-122">Vytvořený pomocí šablony Razor komponenty řešení obsahuje dva projekty:</span><span class="sxs-lookup"><span data-stu-id="619a4-122">The solution created by the Razor Components template contains two projects:</span></span>

* <span data-ttu-id="619a4-123">*WebApplication1.Server* &ndash; serverový projekt je projekt ASP.NET Core nastavení pro hostování aplikace součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="619a4-123">*WebApplication1.Server* &ndash; The server project is an ASP.NET Core project set up to host the Razor Components app.</span></span>
* <span data-ttu-id="619a4-124">*WebApplication1.App* &ndash; na straně klienta webového projektu uživatelského rozhraní, která používá součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="619a4-124">*WebApplication1.App* &ndash; The client-side web UI project that uses Razor Components.</span></span>

<span data-ttu-id="619a4-125">Logika uživatelského rozhraní *WebApplication1.App* projektu je oddělené od zbytku aplikace kvůli technická omezení v ASP.NET Core 3.0 ve verzi Preview 2.</span><span class="sxs-lookup"><span data-stu-id="619a4-125">The UI logic in the *WebApplication1.App* project is separated from the rest of the app due to a technical limitation in ASP.NET Core 3.0 Preview 2.</span></span> <span data-ttu-id="619a4-126">Přípona souboru Razor (*.cshtml*) použít pro Razor komponenty se také používá pro zobrazení Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="619a4-126">The Razor file extension (*.cshtml*) used for Razor Components is also used for Razor Pages and MVC views.</span></span> <span data-ttu-id="619a4-127">V současné době Razor součásti a Razor Pages/MVC mají různé kompilace modely, tak soubory Razor Razor komponenty jsou udržovány odděleně.</span><span class="sxs-lookup"><span data-stu-id="619a4-127">Currently, Razor Components and Razor Pages/MVC have different compilation models, so the Razor Components Razor files are kept separate.</span></span> <span data-ttu-id="619a4-128">V budoucí verzi preview, plánujeme zavést novou příponu souboru pro Razor součásti (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="619a4-128">In a future preview, we plan to introduce a new file extension for Razor Components (*.razor*).</span></span> <span data-ttu-id="619a4-129">Součásti, stránky a zobrazení se hostovat *ve stejném projektu*.</span><span class="sxs-lookup"><span data-stu-id="619a4-129">Components, pages, and views will be hosted *in the same project*.</span></span>

<span data-ttu-id="619a4-130">Při spuštění aplikace jsou k dispozici z karty na bočním panelu více stránek:</span><span class="sxs-lookup"><span data-stu-id="619a4-130">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="619a4-131">Domů</span><span class="sxs-lookup"><span data-stu-id="619a4-131">Home</span></span>
* <span data-ttu-id="619a4-132">Čítač</span><span class="sxs-lookup"><span data-stu-id="619a4-132">Counter</span></span>
* <span data-ttu-id="619a4-133">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="619a4-133">Fetch data</span></span>

<span data-ttu-id="619a4-134">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="619a4-134">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="619a4-135">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti Razor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="619a4-135">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

<span data-ttu-id="619a4-136">*WebApplication1.App/Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="619a4-136">*WebApplication1.App/Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

<span data-ttu-id="619a4-137">Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="619a4-137">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="619a4-138">Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="619a4-138">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="619a4-139">Pokaždé, když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="619a4-139">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="619a4-140">`onclick` Událost se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="619a4-140">The `onclick` event is fired.</span></span>
* <span data-ttu-id="619a4-141">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="619a4-141">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="619a4-142">`currentCount` Se zvýší.</span><span class="sxs-lookup"><span data-stu-id="619a4-142">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="619a4-143">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="619a4-143">The component is rendered again.</span></span>

<span data-ttu-id="619a4-144">Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="619a4-144">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="619a4-145">Přidáte součást do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="619a4-145">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="619a4-146">Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="619a4-146">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="619a4-147">Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="619a4-147">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="619a4-148">*WebApplication1.App/Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="619a4-148">*WebApplication1.App/Pages/Index.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

<span data-ttu-id="619a4-149">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="619a4-149">Run the app.</span></span> <span data-ttu-id="619a4-150">Na domovskou stránku má svůj vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="619a4-150">The homepage has its own counter.</span></span>

<span data-ttu-id="619a4-151">Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:</span><span class="sxs-lookup"><span data-stu-id="619a4-151">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="619a4-152">Přidání vlastnosti pro `IncrementAmount` dekorován `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="619a4-152">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="619a4-153">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="619a4-153">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="619a4-154">*WebApplication1.App/Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="619a4-154">*WebApplication1.App/Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

<span data-ttu-id="619a4-155">Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="619a4-155">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="619a4-156">*WebApplication1.App/Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="619a4-156">*WebApplication1.App/Pages/Index.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

<span data-ttu-id="619a4-157">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="619a4-157">Run the app.</span></span> <span data-ttu-id="619a4-158">Na domovskou stránku má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="619a4-158">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="619a4-159">Další kroky</span><span class="sxs-lookup"><span data-stu-id="619a4-159">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
