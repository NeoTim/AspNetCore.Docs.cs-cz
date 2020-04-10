---
title: Začínáme s ASP.NET CoreBlazor
author: guardrex
description: Můžete začít Blazor s Blazor tím, že vytvoříte aplikaci s nástroji, které si vyberete.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: e9e6eeeb1d29aa529e43d75f5d3951d2c4384d7e
ms.sourcegitcommit: 4506a8f71ece921010ad6b7edebc8b200618f40d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "81002909"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="e0ac1-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e0ac1-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="e0ac1-104">[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e0ac1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="e0ac1-105">Chcete-li začít s Blazorem, postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e0ac1-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0ac1-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e0ac1-107">Chcete-li vytvářet aplikace Blazor Server, nainstalujte nejnovější verzi [Visual Studia 2019](https://visualstudio.microsoft.com/downloads/) s **ASP.NET a zatížením vývoje webu.**</span><span class="sxs-lookup"><span data-stu-id="e0ac1-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="e0ac1-108">Chcete-li vytvořit aplikace Blazor Server a Blazor WebAssembly, nainstalujte nejnovější náhled [Visual Studia 2019](https://visualstudio.microsoft.com/vs/preview/) s **ASP.NET a zatížením vývoje webu.**</span><span class="sxs-lookup"><span data-stu-id="e0ac1-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="e0ac1-109">Informace o dvou modelech Blazor hosting, *Blazor WebAssembly* <xref:blazor/hosting-models>a *Blazor Server*, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e0ac1-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="e0ac1-110">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="e0ac1-110">Create a new project.</span></span>

1. <span data-ttu-id="e0ac1-111">Vyberte **Aplikaci Blazor**.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-111">Select **Blazor App**.</span></span> <span data-ttu-id="e0ac1-112">Vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-112">Select **Next**.</span></span>

1. <span data-ttu-id="e0ac1-113">Zadejte název projektu do pole **Název projektu** nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-113">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="e0ac1-114">Potvrďte, že položka **Umístění** je správná, nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-114">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="e0ac1-115">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-115">Select **Create**.</span></span>

1. <span data-ttu-id="e0ac1-116">Pro prostředí Blazor WebAssembly (Visual Studio 16.6 Preview 2 nebo novější) zvolte šablonu **aplikace Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="e0ac1-116">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="e0ac1-117">Pro prostředí Blazor Server (Visual Studio 16.4 nebo novější) zvolte šablonu **aplikace Blazor Server.**</span><span class="sxs-lookup"><span data-stu-id="e0ac1-117">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="e0ac1-118">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-118">Select **Create**.</span></span>

1. <span data-ttu-id="e0ac1-119">Stisknutím <kbd>klávesy Ctrl</kbd>+<kbd>F5</kbd> aplikaci spusťte.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-119">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e0ac1-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0ac1-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="e0ac1-121">Nainstalujte sadu [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="e0ac1-121">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="e0ac1-122">Volitelně nainstalujte šablonu náhledu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-122">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="e0ac1-123">K použití šablony 3.2 Preview 3 Blazor WebAssembly je **vyžadována** sada [.NET Core SDK verze 3.1.201 nebo novější.](https://dotnet.microsoft.com/download/dotnet-core/3.1)</span><span class="sxs-lookup"><span data-stu-id="e0ac1-123">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="e0ac1-124">Potvrďte nainstalovanou verzi sady .NET Core SDK spuštěním `dotnet --version` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-124">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="e0ac1-125">Nainstalujte [kód sady Visual Studio](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="e0ac1-125">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="e0ac1-126">Nainstalujte nejnovější [c# pro rozšíření kódu Sady Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a rozšíření [debugger u v jazyce JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-126">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="e0ac1-127">Pro prostředí Serveru Blazor proveďte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="e0ac1-128">Pro prostředí Blazor WebAssembly proveďte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-128">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="e0ac1-129">Informace o dvou modelech Blazor hosting, *Blazor Server* a <xref:blazor/hosting-models> *Blazor WebAssembly*, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e0ac1-129">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="e0ac1-130">Otevřete složku *WebApplication1* v kódu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-130">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="e0ac1-131">IDE požadavky, které přidáte prostředky k sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-131">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="e0ac1-132">Vyberte **ano**.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-132">Select **Yes**.</span></span>

1. <span data-ttu-id="e0ac1-133">S Blazor Server, spusťte aplikaci pomocí ladicího programu Visual Studio Kód.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-133">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="e0ac1-134">S Blazor WebAssembly spusťte aplikaci pomocí konfigurace spuštění **.NET Core Launch (Blazor Standalone)** a poté spusťte prohlížeč pomocí **webové sestavy .NET Core Debug Blazor v** konfiguraci spuštění Chromu (vyžaduje Chrome).</span><span class="sxs-lookup"><span data-stu-id="e0ac1-134">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="e0ac1-135">Další informace naleznete v tématu <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-135">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="e0ac1-136">V prohlížeči přejděte na . `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="e0ac1-136">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e0ac1-137">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e0ac1-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e0ac1-138">Blazor Server je podporován v Sadě Visual Studio pro Mac.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-138">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="e0ac1-139">Blazor WebAssembly není v tuto chvíli podporována.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-139">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="e0ac1-140">Chcete-li vytvořit aplikace Blazor WebAssembly v systému macOS, postupujte podle pokynů na kartě **.NET Core CLI.**</span><span class="sxs-lookup"><span data-stu-id="e0ac1-140">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="e0ac1-141">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="e0ac1-141">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="e0ac1-142">Vyberte **Soubor** > **nové řešení** nebo vytvořit nový **projekt**.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-142">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="e0ac1-143">Na postranním panelu vyberte **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-143">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="e0ac1-144">Vyberte šablonu **aplikace Blazor Server.**</span><span class="sxs-lookup"><span data-stu-id="e0ac1-144">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="e0ac1-145">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-145">Select **Create**.</span></span>

   <span data-ttu-id="e0ac1-146">Informace o hostitelském modelu serveru Blazor naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-146">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="e0ac1-147">Nastavte **cílovou architekturu** na **.NET Core 3.1** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-147">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="e0ac1-148">V poli **Název projektu** pojmenujte aplikaci `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="e0ac1-149">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-149">Select **Create**.</span></span>

1. <span data-ttu-id="e0ac1-150">Chcete-li spustit aplikaci *bez ladicího programu ,* vyberte **spustit** > **spustit bez ladění** .</span><span class="sxs-lookup"><span data-stu-id="e0ac1-150">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="e0ac1-151">Spusťte aplikaci s **startem ladění** spustit aplikaci *s ladicím programem*.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-151">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="e0ac1-152">Pokud se zobrazí výzva, která důvěryhodně důvěřuje certifikátu vývoje, důvěřujte certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e0ac1-153">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="e0ac1-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="e0ac1-154">Nainstalujte sadu [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="e0ac1-154">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="e0ac1-155">Volitelně nainstalujte šablonu náhledu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-155">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="e0ac1-156">K použití šablony 3.2 Preview 3 Blazor WebAssembly je **vyžadována** sada [.NET Core SDK verze 3.1.201 nebo novější.](https://dotnet.microsoft.com/download/dotnet-core/3.1)</span><span class="sxs-lookup"><span data-stu-id="e0ac1-156">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="e0ac1-157">Potvrďte nainstalovanou verzi sady .NET Core SDK spuštěním `dotnet --version` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-157">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="e0ac1-158">Pro prostředí Serveru Blazor proveďte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-158">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="e0ac1-159">Pro prostředí Blazor WebAssembly proveďte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-159">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="e0ac1-160">Informace o dvou modelech Blazor hosting, *Blazor Server* a <xref:blazor/hosting-models> *Blazor WebAssembly*, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e0ac1-160">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="e0ac1-161">V prohlížeči přejděte na . `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="e0ac1-161">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="e0ac1-162">Více stránek je k dispozici na kartách v postranním panelu:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-162">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="e0ac1-163">Domů</span><span class="sxs-lookup"><span data-stu-id="e0ac1-163">Home</span></span>
* <span data-ttu-id="e0ac1-164">Čítač</span><span class="sxs-lookup"><span data-stu-id="e0ac1-164">Counter</span></span>
* <span data-ttu-id="e0ac1-165">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="e0ac1-165">Fetch data</span></span>

<span data-ttu-id="e0ac1-166">Na stránce Čítač vyberte tlačítko Klikněte na **mě,** chcete-li čítač narůst bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-166">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="e0ac1-167">Zvýšení čítače na webové stránce obvykle vyžaduje psaní Blazor JavaScriptu, ale s můžete použít C#.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-167">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="e0ac1-168">*Stránky/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-168">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="e0ac1-169">Požadavek `/counter` v prohlížeči, jak `@page` je určeno směrnice v `Counter` horní části, způsobí, že komponenta vykreslit jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-169">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="e0ac1-170">Součásti vykreslování do reprezentace v paměti stromu vykreslení, které pak lze použít k aktualizaci ui flexibilní majestátu a efektivním způsobem.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-170">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="e0ac1-171">Pokaždé, když je vybráno tlačítko **Klikni na mě:**</span><span class="sxs-lookup"><span data-stu-id="e0ac1-171">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="e0ac1-172">Událost `onclick` je aktivována.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-172">The `onclick` event is fired.</span></span>
* <span data-ttu-id="e0ac1-173">Metoda `IncrementCount` je volána.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-173">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="e0ac1-174">Je `currentCount` se zpřísněn.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-174">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="e0ac1-175">Součást je vykreslenznovu.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-175">The component is rendered again.</span></span>

<span data-ttu-id="e0ac1-176">Runtime porovná nový obsah s předchozím obsahem a použije pouze změněný obsah na objektový model dokumentu (DOM).</span><span class="sxs-lookup"><span data-stu-id="e0ac1-176">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="e0ac1-177">Přidejte komponentu do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-177">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="e0ac1-178">Můžete například `Counter` přidat komponentu na domovskou stránku aplikace přidáním `<Counter />` prvku do komponenty. `Index`</span><span class="sxs-lookup"><span data-stu-id="e0ac1-178">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="e0ac1-179">*Stránky/Index.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-179">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="e0ac1-180">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-180">Run the app.</span></span> <span data-ttu-id="e0ac1-181">Domovská stránka má svůj vlastní `Counter` čítač poskytovaný komponentou.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-181">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="e0ac1-182">Parametry komponenty jsou určeny pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), které umožňují nastavit vlastnosti podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-182">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="e0ac1-183">Chcete-li do `Counter` komponenty přidat parametr, `@code` aktualizujte blok komponenty:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-183">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="e0ac1-184">Přidejte veřejnou `IncrementAmount` vlastnost `[Parameter]` s atributem.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-184">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="e0ac1-185">Změňte `IncrementCount` metodu, `IncrementAmount` která se `currentCount`má použít při zvyšování hodnoty .</span><span class="sxs-lookup"><span data-stu-id="e0ac1-185">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="e0ac1-186">*Stránky/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-186">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="e0ac1-187">Zadejte `IncrementAmount` prvek `Index` v `<Counter>` elementu komponenty pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-187">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="e0ac1-188">*Stránky/Index.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="e0ac1-188">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="e0ac1-189">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-189">Run the app.</span></span> <span data-ttu-id="e0ac1-190">Komponenta `Index` má svůj vlastní čítač, který se pohybuje o deset pokaždé, když je vybráno tlačítko **Klikněte na mě.**</span><span class="sxs-lookup"><span data-stu-id="e0ac1-190">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="e0ac1-191">Komponenta `Counter` (*Counter.razor*) pokračuje `/counter` v přírůstcích o jednu.</span><span class="sxs-lookup"><span data-stu-id="e0ac1-191">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e0ac1-192">Další kroky</span><span class="sxs-lookup"><span data-stu-id="e0ac1-192">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="e0ac1-193">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e0ac1-193">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
