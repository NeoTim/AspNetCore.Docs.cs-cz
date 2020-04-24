---
title: Začínáme s ASP.NET CoreBlazor
author: guardrex
description: Začněte Blazor tím, že sestavíte Blazor aplikaci s vámi zvoleným nástrojem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111068"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="cdec4-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="cdec4-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="cdec4-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cdec4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="cdec4-105">Pokud chcete začít používat Blazor, postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="cdec4-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdec4-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdec4-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cdec4-107">Chcete-li vytvořit aplikace serveru Blazor, nainstalujte nejnovější verzi sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="cdec4-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="cdec4-108">Chcete-li vytvořit Blazor Server a Blazor aplikace WebAssembly, nainstalujte nejnovější verzi Preview sady [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoj pro ASP.NET a web** .</span><span class="sxs-lookup"><span data-stu-id="cdec4-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="cdec4-109">Informace o dvou modelech hostování Blazor, *Blazor WebAssembly* a *Blazor serveru*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="cdec4-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cdec4-110">Nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Preview spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="cdec4-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. <span data-ttu-id="cdec4-111">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="cdec4-111">Create a new project.</span></span>

1. <span data-ttu-id="cdec4-112">Vyberte **aplikaci Blazor**.</span><span class="sxs-lookup"><span data-stu-id="cdec4-112">Select **Blazor App**.</span></span> <span data-ttu-id="cdec4-113">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="cdec4-113">Select **Next**.</span></span>

1. <span data-ttu-id="cdec4-114">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="cdec4-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="cdec4-115">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="cdec4-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="cdec4-116">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cdec4-116">Select **Create**.</span></span>

1. <span data-ttu-id="cdec4-117">Pro prostředí WebAssembly Blazor (Visual Studio 16,6 Preview 2 nebo novější) vyberte šablonu **aplikace Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="cdec4-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="cdec4-118">Pro prostředí serveru Blazor (Visual Studio 16,4 nebo novější) vyberte šablonu **aplikace serveru Blazor** .</span><span class="sxs-lookup"><span data-stu-id="cdec4-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="cdec4-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cdec4-119">Select **Create**.</span></span>

1. <span data-ttu-id="cdec4-120">Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd>+<kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="cdec4-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cdec4-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdec4-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="cdec4-122">Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="cdec4-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="cdec4-123">Volitelně můžete nainstalovat šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Preview spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="cdec4-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="cdec4-124">[.NET Core SDK verze 3.1.201 nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1) je **vyžadována** pro použití šablony sestavení Blazor verze 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="cdec4-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="cdec4-125">Ověřte nainstalovanou verzi .NET Core SDK spuštěním `dotnet --version` příkazu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="cdec4-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="cdec4-126">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="cdec4-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="cdec4-127">Nainstalujte nejnovější [rozšíření C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a [ladicí program JavaScript (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavením na `true`.</span><span class="sxs-lookup"><span data-stu-id="cdec4-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="cdec4-128">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cdec4-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="cdec4-129">Pro prostředí WebAssembly Blazor spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="cdec4-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="cdec4-130">Informace o dvou modelech hostování Blazor, *serveru Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="cdec4-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cdec4-131">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cdec4-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="cdec4-132">Rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="cdec4-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="cdec4-133">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="cdec4-133">Select **Yes**.</span></span>

1. <span data-ttu-id="cdec4-134">Se serverem Blazor spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cdec4-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="cdec4-135">V případě Blazor WebAssembly spusťte aplikaci pomocí **spouštěcího rozhraní .NET Core (Blazor Standalone)** a pak spusťte prohlížeč pomocí **webového sestavení .NET Core Blazor web Assembly v konfiguraci spuštění aplikace Chrome** (vyžaduje Chrome).</span><span class="sxs-lookup"><span data-stu-id="cdec4-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="cdec4-136">Další informace naleznete v tématu <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="cdec4-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="cdec4-137">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="cdec4-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cdec4-138">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cdec4-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cdec4-139">Blazor Server je podporován v Visual Studio pro Mac.</span><span class="sxs-lookup"><span data-stu-id="cdec4-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="cdec4-140">WebAssembly Blazor se v tuto chvíli nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="cdec4-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="cdec4-141">Pokud chcete v macOS sestavovat Blazor aplikace WebAssembly, postupujte podle pokynů na kartě **.NET Core CLI** .</span><span class="sxs-lookup"><span data-stu-id="cdec4-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="cdec4-142">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="cdec4-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="cdec4-143">Vyberte **soubor** > **nové řešení** nebo vytvořte **Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="cdec4-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="cdec4-144">Na bočním panelu vyberte**aplikace** **.NET Core** > .</span><span class="sxs-lookup"><span data-stu-id="cdec4-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="cdec4-145">Vyberte šablonu **aplikace Blazor serveru** .</span><span class="sxs-lookup"><span data-stu-id="cdec4-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="cdec4-146">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cdec4-146">Select **Create**.</span></span>

   <span data-ttu-id="cdec4-147">Informace o modelu hostování serveru Blazor naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="cdec4-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cdec4-148">Nastavte **cílovou architekturu** na **.NET Core 3,1** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="cdec4-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="cdec4-149">Do pole **název projektu** název aplikace `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="cdec4-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="cdec4-150">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cdec4-150">Select **Create**.</span></span>

1. <span data-ttu-id="cdec4-151">Chcete-li spustit aplikaci *bez ladicího programu*, vyberte možnost **Spustit** > **běh bez ladění** .</span><span class="sxs-lookup"><span data-stu-id="cdec4-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="cdec4-152">Spusťte aplikaci s použitím **Spustit ladění** a spusťte aplikaci *pomocí ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="cdec4-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="cdec4-153">Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="cdec4-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cdec4-154">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdec4-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="cdec4-155">Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="cdec4-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="cdec4-156">Volitelně můžete nainstalovat šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Preview spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="cdec4-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="cdec4-157">[.NET Core SDK verze 3.1.201 nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1) je **vyžadována** pro použití šablony sestavení Blazor verze 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="cdec4-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="cdec4-158">Ověřte nainstalovanou verzi .NET Core SDK spuštěním `dotnet --version` příkazu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="cdec4-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="cdec4-159">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="cdec4-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="cdec4-160">Pro prostředí WebAssembly Blazor spusťte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="cdec4-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="cdec4-161">Informace o dvou modelech hostování Blazor, *serveru Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="cdec4-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cdec4-162">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="cdec4-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="cdec4-163">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="cdec4-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="cdec4-164">Domů</span><span class="sxs-lookup"><span data-stu-id="cdec4-164">Home</span></span>
* <span data-ttu-id="cdec4-165">Čítač</span><span class="sxs-lookup"><span data-stu-id="cdec4-165">Counter</span></span>
* <span data-ttu-id="cdec4-166">Načíst data</span><span class="sxs-lookup"><span data-stu-id="cdec4-166">Fetch data</span></span>

<span data-ttu-id="cdec4-167">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="cdec4-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="cdec4-168">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít jazyk C#.</span><span class="sxs-lookup"><span data-stu-id="cdec4-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="cdec4-169">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="cdec4-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="cdec4-170">Požadavek na `/counter` v prohlížeči, jak je uveden v `@page` direktivě nahoře, způsobí, že `Counter` komponenta vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="cdec4-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="cdec4-171">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cdec4-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="cdec4-172">Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :</span><span class="sxs-lookup"><span data-stu-id="cdec4-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="cdec4-173">`onclick` Událost je aktivována.</span><span class="sxs-lookup"><span data-stu-id="cdec4-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="cdec4-174">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="cdec4-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="cdec4-175">Dojde `currentCount` k zvýšení.</span><span class="sxs-lookup"><span data-stu-id="cdec4-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="cdec4-176">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="cdec4-176">The component is rendered again.</span></span>

<span data-ttu-id="cdec4-177">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="cdec4-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="cdec4-178">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="cdec4-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="cdec4-179">Například přidejte `Counter` komponentu do domovské stránky aplikace přidáním `<Counter />` elementu do `Index` komponenty.</span><span class="sxs-lookup"><span data-stu-id="cdec4-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="cdec4-180">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="cdec4-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="cdec4-181">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cdec4-181">Run the app.</span></span> <span data-ttu-id="cdec4-182">Domovská stránka má vlastní počítadlo poskytované `Counter` komponentou.</span><span class="sxs-lookup"><span data-stu-id="cdec4-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="cdec4-183">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="cdec4-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="cdec4-184">Chcete-li přidat parametr do `Counter` komponenty, aktualizujte `@code` blok komponenty:</span><span class="sxs-lookup"><span data-stu-id="cdec4-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="cdec4-185">Přidejte veřejnou vlastnost pro `IncrementAmount` s `[Parameter]` atributem.</span><span class="sxs-lookup"><span data-stu-id="cdec4-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="cdec4-186">Změňte `IncrementCount` metodu na použití `IncrementAmount` při zvyšování hodnoty. `currentCount`</span><span class="sxs-lookup"><span data-stu-id="cdec4-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="cdec4-187">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="cdec4-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="cdec4-188">Zadejte `IncrementAmount` v `Index` `<Counter>` elementu komponenty pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="cdec4-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="cdec4-189">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="cdec4-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="cdec4-190">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cdec4-190">Run the app.</span></span> <span data-ttu-id="cdec4-191">`Index` Komponenta má vlastní čítač, který se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** .</span><span class="sxs-lookup"><span data-stu-id="cdec4-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="cdec4-192">Komponenta `Counter` (*Counter. Razor*) se `/counter` stále zvyšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="cdec4-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cdec4-193">Další kroky</span><span class="sxs-lookup"><span data-stu-id="cdec4-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="cdec4-194">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="cdec4-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
