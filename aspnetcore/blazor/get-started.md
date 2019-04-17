---
title: Začínáme s Blazor
author: guardrex
description: Zjistěte, jak začít pracovat s Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: blazor/get-started
ms.openlocfilehash: cc68e1c58af607f840b952f033d3f3b0e54e6cf4
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614812"
---
# <a name="get-started-with-blazor"></a><span data-ttu-id="6c5aa-103">Začínáme s Blazor</span><span class="sxs-lookup"><span data-stu-id="6c5aa-103">Get started with Blazor</span></span>

<span data-ttu-id="6c5aa-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6c5aa-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6c5aa-105">Začínáme s Blazor následujících následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-105">Get started with Blazor following either of the following experiences:</span></span>

* [<span data-ttu-id="6c5aa-106">Komponenty na straně serveru Razor</span><span class="sxs-lookup"><span data-stu-id="6c5aa-106">Server-side Razor Components</span></span>](#server-side-razor-components-experience)
* [<span data-ttu-id="6c5aa-107">Blazor na straně klienta</span><span class="sxs-lookup"><span data-stu-id="6c5aa-107">Client-side Blazor</span></span>](#client-side-blazor-experience)

## <a name="server-side-razor-components-experience"></a><span data-ttu-id="6c5aa-108">Prostředí Razor komponenty na straně serveru</span><span class="sxs-lookup"><span data-stu-id="6c5aa-108">Server-side Razor Components experience</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c5aa-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c5aa-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c5aa-110">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-110">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="6c5aa-111">Chcete-li vytvořit svůj první projekt Razor komponenty v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-111">To create your first Razor Components project in Visual Studio:</span></span>

1. <span data-ttu-id="6c5aa-112">Nainstalujte nejnovější [.NET Core 3.0 ve verzi Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-112">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>
1. <span data-ttu-id="6c5aa-113">Umožní sadě Visual Studio pomocí sady SDK ve verzi preview:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-113">Enable Visual Studio to use preview SDKs:</span></span>
   1. <span data-ttu-id="6c5aa-114">Otevřít **nástroje** > **možnosti** v panelu nabídek.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-114">Open **Tools** > **Options** in the menu bar.</span></span>
   1. <span data-ttu-id="6c5aa-115">Otevřít **projekty a řešení** uzlu.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-115">Open the **Projects and Solutions** node.</span></span> <span data-ttu-id="6c5aa-116">Otevřít **.NET Core** kartu.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-116">Open the **.NET Core** tab.</span></span>
   1. <span data-ttu-id="6c5aa-117">Zaškrtněte políčko u **pomocí verze Preview sady .NET Core SDK**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-117">Check the box for **Use previews of the .NET Core SDK**.</span></span> <span data-ttu-id="6c5aa-118">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-118">Select **OK**.</span></span>
1. <span data-ttu-id="6c5aa-119">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-119">Create a new project.</span></span>
1. <span data-ttu-id="6c5aa-120">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-120">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="6c5aa-121">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-121">Select **Next**.</span></span>
1. <span data-ttu-id="6c5aa-122">Zadejte název **název projektu** pole.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-122">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="6c5aa-123">Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-123">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6c5aa-124">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-124">Select **Create**.</span></span>
1. <span data-ttu-id="6c5aa-125">Ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány v horní části.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-125">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="6c5aa-126">Zvolte **Razor komponenty** šablony a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-126">Choose the **Razor Components** template and select **Create**.</span></span>
1. <span data-ttu-id="6c5aa-127">Stisknutím klávesy **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-127">Press **F5** to run the app.</span></span>

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

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:blazor/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Razor Components project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **ASP.NET Core Razor Components** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

-->

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6c5aa-128">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="6c5aa-128">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6c5aa-129">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-129">Prerequisites:</span></span>

* [<span data-ttu-id="6c5aa-130">.NET core SDK 3.0 ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="6c5aa-130">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="6c5aa-131">Chcete vytvořit svůj první projekt Razor součásti z příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-131">To create your first Razor Components project from a command shell:</span></span>

   ```console
   dotnet new razorcomponents -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="6c5aa-132">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-132">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="6c5aa-133">Razor součásti jsou vytvořeny pomocí syntaxe Razor, ale jsou zkompilovány jinak než zobrazení Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-133">Razor Components are authored using Razor syntax but are compiled differently than Razor Pages and MVC views.</span></span> <span data-ttu-id="6c5aa-134">*.Razor* přípona souboru se používá k určení komponentu Razor.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-134">The *.razor* file extension is used to specify a Razor Component.</span></span> <span data-ttu-id="6c5aa-135">Stránky Razor a MVC zobrazení dál používat *.cshtml* příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-135">Razor Pages and MVC views continue to use the *.cshtml* file extension.</span></span>

> [!NOTE]
> <span data-ttu-id="6c5aa-136">Dají se vytvářet komponenty Razor pomocí *.cshtml* příponu souboru, tak dlouho, dokud tyto soubory jsou označeny jako soubory součástí Razor pomocí `_RazorComponentInclude` vlastnosti Msbuildu.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-136">Razor Components can be authored using the *.cshtml* file extension as long as those files are identified as Razor Component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="6c5aa-137">Například aplikace vytvořená pomocí šablony Razor komponenty Určuje, že všechny *.cshtml* soubory pod *součásti* složky mají být považována za Razor komponenty:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-137">For example, an app created using the Razor Component template specifies that all *.cshtml* files under the *Components* folder should be treated as Razor Components:</span></span>
>
> ```xml
> <_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
> ```

<span data-ttu-id="6c5aa-138">Při spuštění aplikace jsou k dispozici z karty na bočním panelu více stránek:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-138">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="6c5aa-139">Domů</span><span class="sxs-lookup"><span data-stu-id="6c5aa-139">Home</span></span>
* <span data-ttu-id="6c5aa-140">Čítač</span><span class="sxs-lookup"><span data-stu-id="6c5aa-140">Counter</span></span>
* <span data-ttu-id="6c5aa-141">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="6c5aa-141">Fetch data</span></span>

<span data-ttu-id="6c5aa-142">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-142">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="6c5aa-143">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti Razor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-143">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

<span data-ttu-id="6c5aa-144">*WebApplication1/Components/Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-144">*WebApplication1/Components/Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor)]

<span data-ttu-id="6c5aa-145">Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-145">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="6c5aa-146">Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-146">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="6c5aa-147">Pokaždé, když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-147">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="6c5aa-148">`onclick` Událost se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-148">The `onclick` event is fired.</span></span>
* <span data-ttu-id="6c5aa-149">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-149">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="6c5aa-150">`currentCount` Se zvýší.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-150">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="6c5aa-151">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-151">The component is rendered again.</span></span>

<span data-ttu-id="6c5aa-152">Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="6c5aa-152">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="6c5aa-153">Přidáte součást do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-153">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="6c5aa-154">Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-154">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="6c5aa-155">Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-155">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="6c5aa-156">*WebApplication1/Components/Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-156">*WebApplication1/Components/Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="6c5aa-157">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-157">Run the app.</span></span> <span data-ttu-id="6c5aa-158">Na domovskou stránku má svůj vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-158">The homepage has its own counter.</span></span>

<span data-ttu-id="6c5aa-159">Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-159">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="6c5aa-160">Přidání vlastnosti pro `IncrementAmount` dekorován `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-160">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="6c5aa-161">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-161">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="6c5aa-162">*WebApplication1/Components/Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-162">*WebApplication1/Components/Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=4-5,9)]

<span data-ttu-id="6c5aa-163">Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-163">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="6c5aa-164">*WebApplication1/Components/Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-164">*WebApplication1/Components/Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor)]

<span data-ttu-id="6c5aa-165">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-165">Run the app.</span></span> <span data-ttu-id="6c5aa-166">Na domovskou stránku má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-166">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="client-side-blazor-experience"></a><span data-ttu-id="6c5aa-167">Blazor prostředí na straně klienta</span><span class="sxs-lookup"><span data-stu-id="6c5aa-167">Client-side Blazor experience</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c5aa-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c5aa-168">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c5aa-169">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-169">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="6c5aa-170">Chcete-li vytvořit svůj první projekt Blazor v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-170">To create your first Blazor project in Visual Studio:</span></span>

1. <span data-ttu-id="6c5aa-171">Nainstalujte nejnovější [.NET Core 3.0 ve verzi Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-171">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>
1. <span data-ttu-id="6c5aa-172">Umožní sadě Visual Studio pomocí sady SDK ve verzi preview:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-172">Enable Visual Studio to use preview SDKs:</span></span>
   1. <span data-ttu-id="6c5aa-173">Otevřít **nástroje** > **možnosti** v panelu nabídek.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-173">Open **Tools** > **Options** in the menu bar.</span></span>
   1. <span data-ttu-id="6c5aa-174">Otevřít **projekty a řešení** uzlu.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-174">Open the **Projects and Solutions** node.</span></span> <span data-ttu-id="6c5aa-175">Otevřít **.NET Core** kartu.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-175">Open the **.NET Core** tab.</span></span>
   1. <span data-ttu-id="6c5aa-176">Zaškrtněte políčko u **pomocí verze Preview sady .NET Core SDK**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-176">Check the box for **Use previews of the .NET Core SDK**.</span></span> <span data-ttu-id="6c5aa-177">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-177">Select **OK**.</span></span>
1. <span data-ttu-id="6c5aa-178">Nainstalujte nejnovější [Blazor rozšíření](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-178">Install the latest [Blazor extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="6c5aa-179">Tento krok zpřístupní Blazor šablony sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-179">This step makes Blazor templates available to Visual Studio.</span></span>
1. <span data-ttu-id="6c5aa-180">Šablony Blazor zpřístupníte pro použití s .NET Core CLI spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-180">Make the Blazor templates available for use with the .NET Core CLI by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```

1. <span data-ttu-id="6c5aa-181">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-181">Create a new project.</span></span>
1. <span data-ttu-id="6c5aa-182">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-182">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="6c5aa-183">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-183">Select **Next**.</span></span>
1. <span data-ttu-id="6c5aa-184">Zadejte název **název projektu** pole.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-184">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="6c5aa-185">Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-185">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6c5aa-186">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-186">Select **Create**.</span></span>
1. <span data-ttu-id="6c5aa-187">Ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány v horní části.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-187">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="6c5aa-188">Vyberte **Blazor** šablony a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-188">Select the **Blazor** template and select **Create**.</span></span>
1. <span data-ttu-id="6c5aa-189">Stisknutím klávesy **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-189">Press **F5** to run the app.</span></span>

<span data-ttu-id="6c5aa-190">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="6c5aa-190">Congratulations!</span></span> <span data-ttu-id="6c5aa-191">Právě jste spustili svou první aplikaci Blazor!</span><span class="sxs-lookup"><span data-stu-id="6c5aa-191">You just ran your first Blazor app!</span></span>

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

Use the [.NET Core CLI version of this topic](xref:blazor/get-started?tabs=netcore-cli) on macOS.

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

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6c5aa-192">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="6c5aa-192">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6c5aa-193">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-193">Prerequisites:</span></span>

* [<span data-ttu-id="6c5aa-194">.NET core SDK 3.0 ve verzi Preview</span><span class="sxs-lookup"><span data-stu-id="6c5aa-194">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="6c5aa-195">Přidejte do ní šablony Blazor spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-195">Add the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```

1. <span data-ttu-id="6c5aa-196">Vytvořte svůj první projekt Blazor v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-196">Create your first Blazor project in a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="6c5aa-197">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-197">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="6c5aa-198">Blahopřejeme!</span><span class="sxs-lookup"><span data-stu-id="6c5aa-198">Congratulations!</span></span> <span data-ttu-id="6c5aa-199">Právě jste spustili svou první aplikaci Blazor!</span><span class="sxs-lookup"><span data-stu-id="6c5aa-199">You just ran your first Blazor app!</span></span>

---

<span data-ttu-id="6c5aa-200">Při spuštění aplikace jsou k dispozici z karty na bočním panelu více stránek:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-200">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="6c5aa-201">Domů</span><span class="sxs-lookup"><span data-stu-id="6c5aa-201">Home</span></span>
* <span data-ttu-id="6c5aa-202">Čítač</span><span class="sxs-lookup"><span data-stu-id="6c5aa-202">Counter</span></span>
* <span data-ttu-id="6c5aa-203">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="6c5aa-203">Fetch data</span></span>

<span data-ttu-id="6c5aa-204">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-204">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="6c5aa-205">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale Blazor poskytuje lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-205">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

<span data-ttu-id="6c5aa-206">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-206">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

<span data-ttu-id="6c5aa-207">Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-207">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="6c5aa-208">Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-208">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="6c5aa-209">Pokaždé, když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-209">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="6c5aa-210">`onclick` Událost se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-210">The `onclick` event is fired.</span></span>
* <span data-ttu-id="6c5aa-211">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-211">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="6c5aa-212">`currentCount` Se zvýší.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-212">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="6c5aa-213">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-213">The component is rendered again.</span></span>

<span data-ttu-id="6c5aa-214">Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="6c5aa-214">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="6c5aa-215">Přidáte součást do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-215">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="6c5aa-216">Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-216">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="6c5aa-217">Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-217">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="6c5aa-218">V *Pages/Index.cshtml*, nahraďte výzvy průzkumu s komponentou čítače:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-218">In *Pages/Index.cshtml*, replace the Survey Prompt component with a Counter component:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

<span data-ttu-id="6c5aa-219">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-219">Run the app.</span></span> <span data-ttu-id="6c5aa-220">Na domovskou stránku má svůj vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-220">The homepage has its own counter.</span></span>

<span data-ttu-id="6c5aa-221">Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-221">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="6c5aa-222">Přidání vlastnosti pro `IncrementAmount` dekorován `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-222">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="6c5aa-223">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-223">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="6c5aa-224">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-224">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4-5,9)]

<span data-ttu-id="6c5aa-225">Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-225">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="6c5aa-226">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6c5aa-226">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

<span data-ttu-id="6c5aa-227">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-227">Run the app.</span></span> <span data-ttu-id="6c5aa-228">Na domovskou stránku má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="6c5aa-228">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6c5aa-229">Další kroky</span><span class="sxs-lookup"><span data-stu-id="6c5aa-229">Next steps</span></span>

<xref:tutorials/first-blazor-app>
