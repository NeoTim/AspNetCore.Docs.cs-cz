---
title: Začínáme s Razor komponenty
author: guardrex
description: Zjistěte, jak začít pracovat s komponentami Razor vytvořením a úpravou Razor součástí projektu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/13/2019
uid: razor-components/get-started
ms.openlocfilehash: 86427f9d8a6bc70a65f58ff1b9f8f37c536a97a6
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978333"
---
# <a name="get-started-with-razor-components"></a><span data-ttu-id="287e9-103">Začínáme s Razor komponenty</span><span class="sxs-lookup"><span data-stu-id="287e9-103">Get started with Razor Components</span></span>

<span data-ttu-id="287e9-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="287e9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="287e9-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="287e9-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="287e9-106">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="287e9-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="287e9-107">Chcete-li vytvořit svůj první projekt Razor komponenty v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="287e9-107">To create your first Razor Components project in Visual Studio:</span></span>

1. <span data-ttu-id="287e9-108">Vyberte **souboru** > **nový projekt** > **webové** > **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="287e9-108">Select **File** > **New Project** > **Web** > **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="287e9-109">Ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány v horní části.</span><span class="sxs-lookup"><span data-stu-id="287e9-109">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="287e9-110">Zvolte **Razor komponenty** šablony a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="287e9-110">Choose the **Razor Components** template and select **OK**.</span></span>
1. <span data-ttu-id="287e9-111">Stisknutím klávesy **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="287e9-111">Press **F5** to run the app.</span></span>

<span data-ttu-id="287e9-112">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="287e9-112">Congratulations!</span></span> <span data-ttu-id="287e9-113">Právě jste spustili svou první aplikaci Razor součásti!</span><span class="sxs-lookup"><span data-stu-id="287e9-113">You just ran your first Razor Components app!</span></span>

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

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="287e9-114">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="287e9-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="287e9-115">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="287e9-115">Prerequisites:</span></span>

* [<span data-ttu-id="287e9-116">.NET core SDK 3.0 ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="287e9-116">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="287e9-117">Chcete vytvořit svůj první projekt Razor součásti z příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="287e9-117">To create your first Razor Components project from a command shell:</span></span>

   ```console
   dotnet new razorcomponents -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="287e9-118">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="287e9-118">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="287e9-119">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="287e9-119">Congratulations!</span></span> <span data-ttu-id="287e9-120">Právě jste spustili svou první aplikaci Razor součásti!</span><span class="sxs-lookup"><span data-stu-id="287e9-120">You just ran your first Razor Components app!</span></span>

---

## <a name="razor-components-project"></a><span data-ttu-id="287e9-121">Projekt součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="287e9-121">Razor Components project</span></span>

<span data-ttu-id="287e9-122">Razor součásti jsou vytvořeny pomocí syntaxe Razor, ale jsou zkompilovány jinak než zobrazení Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="287e9-122">Razor Components are authored using Razor syntax but are compiled differently than Razor Pages and MVC views.</span></span> <span data-ttu-id="287e9-123">*.Razor* přípona souboru se používá k určení komponentu Razor.</span><span class="sxs-lookup"><span data-stu-id="287e9-123">The *.razor* file extension is used to specify a Razor Component.</span></span> <span data-ttu-id="287e9-124">Stránky Razor a MVC zobrazení dál používat *.cshtml* příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="287e9-124">Razor Pages and MVC views continue to use the *.cshtml* file extension.</span></span>

> [!NOTE]
> <span data-ttu-id="287e9-125">Dají se vytvářet komponenty Razor pomocí *.cshtml* příponu souboru, tak dlouho, dokud tyto soubory jsou označeny jako soubory součástí Razor pomocí `_RazorComponentInclude` vlastnosti Msbuildu.</span><span class="sxs-lookup"><span data-stu-id="287e9-125">Razor Components can be authored using the *.cshtml* file extension as long as those files are identified as Razor Component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="287e9-126">Například aplikace vytvořená pomocí šablony Razor komponenty Určuje, že všechny *.cshtml* soubory pod *součásti* složky mají být považována za Razor komponenty:</span><span class="sxs-lookup"><span data-stu-id="287e9-126">For example, an app created using the Razor Component template specifies that all *.cshtml* files under the *Components* folder should be treated as Razor Components:</span></span>
>
> ```xml
> <_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
> ```

<span data-ttu-id="287e9-127">Při spuštění aplikace jsou k dispozici z karty na bočním panelu více stránek:</span><span class="sxs-lookup"><span data-stu-id="287e9-127">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="287e9-128">Domů</span><span class="sxs-lookup"><span data-stu-id="287e9-128">Home</span></span>
* <span data-ttu-id="287e9-129">Čítač</span><span class="sxs-lookup"><span data-stu-id="287e9-129">Counter</span></span>
* <span data-ttu-id="287e9-130">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="287e9-130">Fetch data</span></span>

<span data-ttu-id="287e9-131">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="287e9-131">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="287e9-132">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti Razor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="287e9-132">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

<span data-ttu-id="287e9-133">*WebApplication1/Components/Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="287e9-133">*WebApplication1/Components/Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor)]

<span data-ttu-id="287e9-134">Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="287e9-134">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="287e9-135">Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="287e9-135">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="287e9-136">Pokaždé, když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="287e9-136">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="287e9-137">`onclick` Událost se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="287e9-137">The `onclick` event is fired.</span></span>
* <span data-ttu-id="287e9-138">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="287e9-138">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="287e9-139">`currentCount` Se zvýší.</span><span class="sxs-lookup"><span data-stu-id="287e9-139">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="287e9-140">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="287e9-140">The component is rendered again.</span></span>

<span data-ttu-id="287e9-141">Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="287e9-141">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="287e9-142">Přidáte součást do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="287e9-142">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="287e9-143">Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="287e9-143">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="287e9-144">Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="287e9-144">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="287e9-145">*WebApplication1/Components/Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="287e9-145">*WebApplication1/Components/Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="287e9-146">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="287e9-146">Run the app.</span></span> <span data-ttu-id="287e9-147">Na domovskou stránku má svůj vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="287e9-147">The homepage has its own counter.</span></span>

<span data-ttu-id="287e9-148">Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:</span><span class="sxs-lookup"><span data-stu-id="287e9-148">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="287e9-149">Přidání vlastnosti pro `IncrementAmount` dekorován `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="287e9-149">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="287e9-150">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="287e9-150">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="287e9-151">*WebApplication1/Components/Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="287e9-151">*WebApplication1/Components/Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=4,8)]

<span data-ttu-id="287e9-152">Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="287e9-152">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="287e9-153">*WebApplication1/Components/Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="287e9-153">*WebApplication1/Components/Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor)]

<span data-ttu-id="287e9-154">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="287e9-154">Run the app.</span></span> <span data-ttu-id="287e9-155">Na domovskou stránku má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="287e9-155">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="287e9-156">Další kroky</span><span class="sxs-lookup"><span data-stu-id="287e9-156">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
