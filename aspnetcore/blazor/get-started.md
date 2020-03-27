---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením Blazor aplikace pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 9ebeb57d2fad7e4c288d61a46911f2bf64cac2fb
ms.sourcegitcommit: f3b1bcfd108e5d53f73abc0bf2555890869d953b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80320944"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="b185d-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b185d-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="b185d-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b185d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b185d-105">Pokud chcete začít používat Blazor, postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="b185d-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b185d-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b185d-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b185d-107">Pokud chcete vytvářet aplikace serveru Blazor, nainstalujte [Visual Studio 2019 verze 16,4 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="b185d-107">To create Blazor Server apps, install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="b185d-108">Chcete-li vytvořit Blazor Server a Blazor aplikace WebAssembly, nainstalujte Visual Studio 2019 16,6 Preview 2 nebo novější s úlohou **vývoj pro ASP.NET a web** .</span><span class="sxs-lookup"><span data-stu-id="b185d-108">To create Blazor Server and Blazor WebAssembly apps, install Visual Studio 2019 16.6 Preview 2 or later with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="b185d-109">Informace o dvou modelech hostování Blazor, *Blazor WebAssembly* a *Blazor serveru*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b185d-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b185d-110">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="b185d-110">Create a new project.</span></span>

1. <span data-ttu-id="b185d-111">Vyberte **aplikaci Blazor**.</span><span class="sxs-lookup"><span data-stu-id="b185d-111">Select **Blazor App**.</span></span> <span data-ttu-id="b185d-112">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="b185d-112">Select **Next**.</span></span>

1. <span data-ttu-id="b185d-113">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="b185d-113">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b185d-114">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="b185d-114">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="b185d-115">Vyberte **Create** (Vytvořit).</span><span class="sxs-lookup"><span data-stu-id="b185d-115">Select **Create**.</span></span>

1. <span data-ttu-id="b185d-116">Pro prostředí WebAssembly Blazor (Visual Studio 16,6 Preview 2 nebo novější) vyberte šablonu **aplikace Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="b185d-116">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b185d-117">Pro prostředí serveru Blazor (Visual Studio 16,4 nebo novější) vyberte šablonu **aplikace serveru Blazor** .</span><span class="sxs-lookup"><span data-stu-id="b185d-117">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="b185d-118">Vyberte **Create** (Vytvořit).</span><span class="sxs-lookup"><span data-stu-id="b185d-118">Select **Create**.</span></span>

1. <span data-ttu-id="b185d-119">Spusťte aplikaci stisknutím klávesy <kbd>Ctrl</kbd>+<kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="b185d-119">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b185d-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b185d-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b185d-121">Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="b185d-121">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="b185d-122">Volitelně můžete nainstalovat šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Preview spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="b185d-122">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="b185d-123">[.NET Core SDK verze 3.1.201 nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1) se **vyžaduje** pro použití šablony sestavení Blazor verze 3,2 Preview 3.</span><span class="sxs-lookup"><span data-stu-id="b185d-123">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="b185d-124">Ověřte nainstalovanou verzi .NET Core SDK spuštěním `dotnet --version` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b185d-124">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="b185d-125">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="b185d-125">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="b185d-126">Nainstalujte nejnovější [ C# rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a rozšíření [JavaScript Debugger (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="b185d-126">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="b185d-127">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b185d-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="b185d-128">Pro prostředí WebAssembly Blazor spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b185d-128">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="b185d-129">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b185d-129">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b185d-130">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b185d-130">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="b185d-131">Rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="b185d-131">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="b185d-132">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="b185d-132">Select **Yes**.</span></span>

1. <span data-ttu-id="b185d-133">Spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b185d-133">Run the app using the Visual Studio Code debugger.</span></span>

1. <span data-ttu-id="b185d-134">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b185d-134">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b185d-135">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="b185d-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b185d-136">Blazor Server je podporován v Visual Studio pro Mac.</span><span class="sxs-lookup"><span data-stu-id="b185d-136">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="b185d-137">WebAssembly Blazor se v tuto chvíli nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="b185d-137">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="b185d-138">Pokud chcete v macOS sestavovat Blazor aplikace WebAssembly, postupujte podle pokynů na kartě **.NET Core CLI** .</span><span class="sxs-lookup"><span data-stu-id="b185d-138">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="b185d-139">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="b185d-139">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="b185d-140">Vyberte **soubor** > **nové řešení** nebo vytvořte **Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="b185d-140">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="b185d-141">Na bočním panelu vyberte **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="b185d-141">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="b185d-142">Vyberte šablonu **aplikace Blazor serveru** .</span><span class="sxs-lookup"><span data-stu-id="b185d-142">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="b185d-143">Vyberte **Create** (Vytvořit).</span><span class="sxs-lookup"><span data-stu-id="b185d-143">Select **Create**.</span></span>

   <span data-ttu-id="b185d-144">Informace o modelu hostování serveru Blazor naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b185d-144">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b185d-145">Nastavte **cílovou architekturu** na **.NET Core 3,1** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="b185d-145">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="b185d-146">Do pole **název projektu** název aplikace `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="b185d-146">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="b185d-147">Vyberte **Create** (Vytvořit).</span><span class="sxs-lookup"><span data-stu-id="b185d-147">Select **Create**.</span></span>

1. <span data-ttu-id="b185d-148">Vyberte **spustit** > **Spustit bez ladění** pro spuštění aplikace *bez ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="b185d-148">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="b185d-149">Spusťte aplikaci s použitím **Spustit ladění** a spusťte aplikaci *pomocí ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="b185d-149">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="b185d-150">Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="b185d-150">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b185d-151">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="b185d-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="b185d-152">Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="b185d-152">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="b185d-153">Volitelně můžete nainstalovat šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Preview spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="b185d-153">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="b185d-154">[.NET Core SDK verze 3.1.201 nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1) se **vyžaduje** pro použití šablony sestavení Blazor verze 3,2 Preview 3.</span><span class="sxs-lookup"><span data-stu-id="b185d-154">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="b185d-155">Ověřte nainstalovanou verzi .NET Core SDK spuštěním `dotnet --version` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b185d-155">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="b185d-156">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="b185d-156">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b185d-157">Pro prostředí WebAssembly Blazor spusťte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b185d-157">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b185d-158">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b185d-158">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b185d-159">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b185d-159">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="b185d-160">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="b185d-160">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="b185d-161">Domů</span><span class="sxs-lookup"><span data-stu-id="b185d-161">Home</span></span>
* <span data-ttu-id="b185d-162">Čítač</span><span class="sxs-lookup"><span data-stu-id="b185d-162">Counter</span></span>
* <span data-ttu-id="b185d-163">Načíst data</span><span class="sxs-lookup"><span data-stu-id="b185d-163">Fetch data</span></span>

<span data-ttu-id="b185d-164">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="b185d-164">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="b185d-165">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít C#.</span><span class="sxs-lookup"><span data-stu-id="b185d-165">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="b185d-166">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b185d-166">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="b185d-167">Žádost o `/counter` v prohlížeči, jak je uvedeno v direktivě `@page` v horní části, způsobí, že komponenta `Counter` vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="b185d-167">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="b185d-168">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b185d-168">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="b185d-169">Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :</span><span class="sxs-lookup"><span data-stu-id="b185d-169">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="b185d-170">Událost `onclick` je aktivována.</span><span class="sxs-lookup"><span data-stu-id="b185d-170">The `onclick` event is fired.</span></span>
* <span data-ttu-id="b185d-171">Je volána metoda `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="b185d-171">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="b185d-172">`currentCount` se zvyšuje.</span><span class="sxs-lookup"><span data-stu-id="b185d-172">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="b185d-173">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="b185d-173">The component is rendered again.</span></span>

<span data-ttu-id="b185d-174">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="b185d-174">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="b185d-175">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="b185d-175">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="b185d-176">Přidejte například komponentu `Counter` do domovské stránky aplikace přidáním prvku `<Counter />` do komponenty `Index`.</span><span class="sxs-lookup"><span data-stu-id="b185d-176">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="b185d-177">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b185d-177">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="b185d-178">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b185d-178">Run the app.</span></span> <span data-ttu-id="b185d-179">Domovská stránka má svůj vlastní čítač poskytovaný komponentou `Counter`.</span><span class="sxs-lookup"><span data-stu-id="b185d-179">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="b185d-180">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="b185d-180">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="b185d-181">Chcete-li přidat parametr do součásti `Counter`, aktualizujte blok `@code` součásti:</span><span class="sxs-lookup"><span data-stu-id="b185d-181">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="b185d-182">Přidejte veřejnou vlastnost pro `IncrementAmount` s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="b185d-182">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="b185d-183">Změňte metodu `IncrementCount` na použití `IncrementAmount` při zvyšování hodnoty `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="b185d-183">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="b185d-184">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b185d-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="b185d-185">Určete `IncrementAmount` v prvku `<Counter>` komponenty `Index` pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="b185d-185">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="b185d-186">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b185d-186">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="b185d-187">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b185d-187">Run the app.</span></span> <span data-ttu-id="b185d-188">Komponenta `Index` má vlastní čítač, který při každém výběru tlačítka pro **kliknutí na tlačítko Další** zvyšuje o 10.</span><span class="sxs-lookup"><span data-stu-id="b185d-188">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="b185d-189">Komponenta `Counter` (*Counter. Razor*) se v `/counter` nadále zvětšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="b185d-189">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b185d-190">Další kroky</span><span class="sxs-lookup"><span data-stu-id="b185d-190">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="b185d-191">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b185d-191">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
