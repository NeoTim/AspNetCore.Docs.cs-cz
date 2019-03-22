---
title: Začínáme s Blazor
author: guardrex
description: Zjistěte, jak začít pracovat s Blazor vytvořením a úpravou Blazor projektu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: spa/blazor/get-started
ms.openlocfilehash: f46bd9af0f0762e794349d4e98de5c086a690d72
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327226"
---
# <a name="get-started-with-blazor"></a><span data-ttu-id="26761-103">Začínáme s Blazor</span><span class="sxs-lookup"><span data-stu-id="26761-103">Get started with Blazor</span></span>

<span data-ttu-id="26761-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="26761-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="26761-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26761-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="26761-106">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="26761-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="26761-107">Chcete-li vytvořit svůj první projekt Blazor v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="26761-107">To create your first Blazor project in Visual Studio:</span></span>

1. <span data-ttu-id="26761-108">Nainstalujte nejnovější [Blazor rozšíření](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="26761-108">Install the latest [Blazor extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="26761-109">Tento krok zpřístupní Blazor šablony sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="26761-109">This step makes Blazor templates available to Visual Studio.</span></span>
1. <span data-ttu-id="26761-110">Šablony Blazor zpřístupníte pro použití s .NET Core CLI spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="26761-110">Make the Blazor templates available for use with the .NET Core CLI by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```

1. <span data-ttu-id="26761-111">Vyberte **souboru** > **nový projekt** > **webové** > **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="26761-111">Select **File** > **New Project** > **Web** > **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="26761-112">Ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány v horní části.</span><span class="sxs-lookup"><span data-stu-id="26761-112">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="26761-113">Zvolte **Blazor** šablony a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="26761-113">Choose the **Blazor** template and select **OK**.</span></span>
1. <span data-ttu-id="26761-114">Stisknutím klávesy **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="26761-114">Press **F5** to run the app.</span></span>

<span data-ttu-id="26761-115">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="26761-115">Congratulations!</span></span> <span data-ttu-id="26761-116">Právě jste spustili svou první aplikaci Blazor!</span><span class="sxs-lookup"><span data-stu-id="26761-116">You just ran your first Blazor app!</span></span>

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Blazor project in Visual Studio Code:

1. Execute the following command in a command shell:

   ```console
   dotnet new blazor -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Visual Studio code offers to create assets to build and debug the app, which includes the *tasks.json* and *launch.json* files. Select **Yes** to add the assets.

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Blazor app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Blazor project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **Blazor** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Blazor app!
-->

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="26761-117">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="26761-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="26761-118">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="26761-118">Prerequisites:</span></span>

* [<span data-ttu-id="26761-119">.NET core SDK 3.0 ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="26761-119">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="26761-120">Přidejte do ní šablony Blazor spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="26761-120">Add the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```

1. <span data-ttu-id="26761-121">Vytvořte svůj první projekt Blazor v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="26761-121">Create your first Blazor project in a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="26761-122">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="26761-122">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="26761-123">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="26761-123">Congratulations!</span></span> <span data-ttu-id="26761-124">Právě jste spustili svou první aplikaci Blazor!</span><span class="sxs-lookup"><span data-stu-id="26761-124">You just ran your first Blazor app!</span></span>

---

## <a name="blazor-project"></a><span data-ttu-id="26761-125">Blazor projektu</span><span class="sxs-lookup"><span data-stu-id="26761-125">Blazor project</span></span>

<span data-ttu-id="26761-126">Při spuštění aplikace jsou k dispozici z karty na bočním panelu více stránek:</span><span class="sxs-lookup"><span data-stu-id="26761-126">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="26761-127">Domů</span><span class="sxs-lookup"><span data-stu-id="26761-127">Home</span></span>
* <span data-ttu-id="26761-128">Čítač</span><span class="sxs-lookup"><span data-stu-id="26761-128">Counter</span></span>
* <span data-ttu-id="26761-129">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="26761-129">Fetch data</span></span>

<span data-ttu-id="26761-130">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="26761-130">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="26761-131">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale Blazor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="26761-131">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

<span data-ttu-id="26761-132">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="26761-132">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

<span data-ttu-id="26761-133">Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="26761-133">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="26761-134">Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="26761-134">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="26761-135">Pokaždé, když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="26761-135">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="26761-136">`onclick` Událost se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="26761-136">The `onclick` event is fired.</span></span>
* <span data-ttu-id="26761-137">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="26761-137">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="26761-138">`currentCount` Se zvýší.</span><span class="sxs-lookup"><span data-stu-id="26761-138">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="26761-139">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="26761-139">The component is rendered again.</span></span>

<span data-ttu-id="26761-140">Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="26761-140">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="26761-141">Přidáte součást do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="26761-141">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="26761-142">Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="26761-142">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="26761-143">Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="26761-143">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="26761-144">V *Pages/Index.cshtml*, nahraďte výzvy průzkumu s komponentou čítače:</span><span class="sxs-lookup"><span data-stu-id="26761-144">In *Pages/Index.cshtml*, replace the Survey Prompt component with a Counter component:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

<span data-ttu-id="26761-145">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="26761-145">Run the app.</span></span> <span data-ttu-id="26761-146">Na domovskou stránku má svůj vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="26761-146">The homepage has its own counter.</span></span>

<span data-ttu-id="26761-147">Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:</span><span class="sxs-lookup"><span data-stu-id="26761-147">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="26761-148">Přidání vlastnosti pro `IncrementAmount` dekorován `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="26761-148">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="26761-149">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="26761-149">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="26761-150">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="26761-150">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

<span data-ttu-id="26761-151">Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="26761-151">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="26761-152">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="26761-152">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

<span data-ttu-id="26761-153">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="26761-153">Run the app.</span></span> <span data-ttu-id="26761-154">Na domovskou stránku má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="26761-154">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="26761-155">Další kroky</span><span class="sxs-lookup"><span data-stu-id="26761-155">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
