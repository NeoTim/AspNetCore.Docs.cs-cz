---
title: Začínáme s ASP.NET CoreBlazor
author: guardrex
description: Můžete začít Blazor s Blazor tím, že vytvoříte aplikaci s nástroji, které si vyberete.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 7fe4fbb082f08d4f71684c836a826d8b6dd888f6
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488725"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="348be-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="348be-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="348be-104">[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="348be-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="348be-105">Chcete-li začít s Blazorem, postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="348be-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="348be-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="348be-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="348be-107">Chcete-li vytvářet aplikace Blazor Server, nainstalujte nejnovější verzi [Visual Studia 2019](https://visualstudio.microsoft.com/downloads/) s **ASP.NET a zatížením vývoje webu.**</span><span class="sxs-lookup"><span data-stu-id="348be-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="348be-108">Chcete-li vytvořit aplikace Blazor Server a Blazor WebAssembly, nainstalujte nejnovější náhled [Visual Studia 2019](https://visualstudio.microsoft.com/vs/preview/) s **ASP.NET a zatížením vývoje webu.**</span><span class="sxs-lookup"><span data-stu-id="348be-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="348be-109">Informace o dvou modelech Blazor hosting, *Blazor WebAssembly* <xref:blazor/hosting-models>a *Blazor Server*, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="348be-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="348be-110">Nainstalujte šablonu náhledu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="348be-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

1. <span data-ttu-id="348be-111">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="348be-111">Create a new project.</span></span>

1. <span data-ttu-id="348be-112">Vyberte **Aplikaci Blazor**.</span><span class="sxs-lookup"><span data-stu-id="348be-112">Select **Blazor App**.</span></span> <span data-ttu-id="348be-113">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="348be-113">Select **Next**.</span></span>

1. <span data-ttu-id="348be-114">Zadejte název projektu do pole **Název projektu** nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="348be-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="348be-115">Potvrďte, že položka **Umístění** je správná, nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="348be-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="348be-116">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="348be-116">Select **Create**.</span></span>

1. <span data-ttu-id="348be-117">Pro prostředí Blazor WebAssembly (Visual Studio 16.6 Preview 2 nebo novější) zvolte šablonu **aplikace Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="348be-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="348be-118">Pro prostředí Blazor Server (Visual Studio 16.4 nebo novější) zvolte šablonu **aplikace Blazor Server.**</span><span class="sxs-lookup"><span data-stu-id="348be-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="348be-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="348be-119">Select **Create**.</span></span>

1. <span data-ttu-id="348be-120">Stisknutím <kbd>klávesy Ctrl</kbd>+<kbd>F5</kbd> aplikaci spusťte.</span><span class="sxs-lookup"><span data-stu-id="348be-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="348be-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="348be-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="348be-122">Nainstalujte sadu [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="348be-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="348be-123">Volitelně nainstalujte šablonu náhledu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="348be-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > <span data-ttu-id="348be-124">K použití šablony 3.2 Preview 4 Blazor WebAssembly je **vyžadována** sada [.NET Core SDK verze 3.1.201 nebo novější.](https://dotnet.microsoft.com/download/dotnet-core/3.1)</span><span class="sxs-lookup"><span data-stu-id="348be-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="348be-125">Potvrďte nainstalovanou verzi sady .NET Core SDK spuštěním `dotnet --version` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="348be-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="348be-126">Nainstalujte [kód sady Visual Studio](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="348be-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="348be-127">Nainstalujte nejnovější [c# pro rozšíření kódu Sady Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a rozšíření [debugger u v jazyce JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="348be-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="348be-128">Pro prostředí Serveru Blazor proveďte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="348be-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="348be-129">Pro prostředí Blazor WebAssembly proveďte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="348be-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="348be-130">Informace o dvou modelech Blazor hosting, *Blazor Server* a <xref:blazor/hosting-models> *Blazor WebAssembly*, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="348be-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="348be-131">Otevřete složku *WebApplication1* v kódu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="348be-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="348be-132">IDE požadavky, které přidáte prostředky k sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="348be-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="348be-133">Vyberte **ano**.</span><span class="sxs-lookup"><span data-stu-id="348be-133">Select **Yes**.</span></span>

1. <span data-ttu-id="348be-134">S Blazor Server, spusťte aplikaci pomocí ladicího programu Visual Studio Kód.</span><span class="sxs-lookup"><span data-stu-id="348be-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="348be-135">S Blazor WebAssembly spusťte aplikaci pomocí konfigurace spuštění **.NET Core Launch (Blazor Standalone)** a poté spusťte prohlížeč pomocí **webové sestavy .NET Core Debug Blazor v** konfiguraci spuštění Chromu (vyžaduje Chrome).</span><span class="sxs-lookup"><span data-stu-id="348be-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="348be-136">Další informace naleznete v tématu <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="348be-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="348be-137">V prohlížeči přejděte na . `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="348be-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="348be-138">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="348be-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="348be-139">Blazor Server je podporován v Sadě Visual Studio pro Mac.</span><span class="sxs-lookup"><span data-stu-id="348be-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="348be-140">Blazor WebAssembly není v tuto chvíli podporována.</span><span class="sxs-lookup"><span data-stu-id="348be-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="348be-141">Chcete-li vytvořit aplikace Blazor WebAssembly v systému macOS, postupujte podle pokynů na kartě **.NET Core CLI.**</span><span class="sxs-lookup"><span data-stu-id="348be-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="348be-142">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="348be-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="348be-143">Vyberte **Soubor** > **nové řešení** nebo vytvořit nový **projekt**.</span><span class="sxs-lookup"><span data-stu-id="348be-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="348be-144">Na postranním panelu vyberte **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="348be-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="348be-145">Vyberte šablonu **aplikace Blazor Server.**</span><span class="sxs-lookup"><span data-stu-id="348be-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="348be-146">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="348be-146">Select **Create**.</span></span>

   <span data-ttu-id="348be-147">Informace o hostitelském modelu serveru Blazor naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="348be-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="348be-148">Nastavte **cílovou architekturu** na **.NET Core 3.1** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="348be-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="348be-149">V poli **Název projektu** pojmenujte aplikaci `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="348be-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="348be-150">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="348be-150">Select **Create**.</span></span>

1. <span data-ttu-id="348be-151">Chcete-li spustit aplikaci *bez ladicího programu ,* vyberte **spustit** > **spustit bez ladění** .</span><span class="sxs-lookup"><span data-stu-id="348be-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="348be-152">Spusťte aplikaci s **startem ladění** spustit aplikaci *s ladicím programem*.</span><span class="sxs-lookup"><span data-stu-id="348be-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="348be-153">Pokud se zobrazí výzva, která důvěryhodně důvěřuje certifikátu vývoje, důvěřujte certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="348be-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="348be-154">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="348be-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="348be-155">Nainstalujte sadu [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="348be-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="348be-156">Volitelně nainstalujte šablonu náhledu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="348be-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > <span data-ttu-id="348be-157">K použití šablony 3.2 Preview 4 Blazor WebAssembly je **vyžadována** sada [.NET Core SDK verze 3.1.201 nebo novější.](https://dotnet.microsoft.com/download/dotnet-core/3.1)</span><span class="sxs-lookup"><span data-stu-id="348be-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="348be-158">Potvrďte nainstalovanou verzi sady .NET Core SDK spuštěním `dotnet --version` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="348be-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="348be-159">Pro prostředí Serveru Blazor proveďte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="348be-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="348be-160">Pro prostředí Blazor WebAssembly proveďte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="348be-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="348be-161">Informace o dvou modelech Blazor hosting, *Blazor Server* a <xref:blazor/hosting-models> *Blazor WebAssembly*, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="348be-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="348be-162">V prohlížeči přejděte na . `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="348be-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="348be-163">Více stránek je k dispozici na kartách v postranním panelu:</span><span class="sxs-lookup"><span data-stu-id="348be-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="348be-164">Domů</span><span class="sxs-lookup"><span data-stu-id="348be-164">Home</span></span>
* <span data-ttu-id="348be-165">Čítač</span><span class="sxs-lookup"><span data-stu-id="348be-165">Counter</span></span>
* <span data-ttu-id="348be-166">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="348be-166">Fetch data</span></span>

<span data-ttu-id="348be-167">Na stránce Čítač vyberte tlačítko Klikněte na **mě,** chcete-li čítač narůst bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="348be-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="348be-168">Zvýšení čítače na webové stránce obvykle vyžaduje psaní Blazor JavaScriptu, ale s můžete použít C#.</span><span class="sxs-lookup"><span data-stu-id="348be-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="348be-169">*Stránky/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="348be-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="348be-170">Požadavek `/counter` v prohlížeči, jak `@page` je určeno směrnice v `Counter` horní části, způsobí, že komponenta vykreslit jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="348be-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="348be-171">Součásti vykreslování do reprezentace v paměti stromu vykreslení, které pak lze použít k aktualizaci ui flexibilní majestátu a efektivním způsobem.</span><span class="sxs-lookup"><span data-stu-id="348be-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="348be-172">Pokaždé, když je vybráno tlačítko **Klikni na mě:**</span><span class="sxs-lookup"><span data-stu-id="348be-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="348be-173">Událost `onclick` je aktivována.</span><span class="sxs-lookup"><span data-stu-id="348be-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="348be-174">Metoda `IncrementCount` je volána.</span><span class="sxs-lookup"><span data-stu-id="348be-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="348be-175">Je `currentCount` se zpřísněn.</span><span class="sxs-lookup"><span data-stu-id="348be-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="348be-176">Součást je vykreslenznovu.</span><span class="sxs-lookup"><span data-stu-id="348be-176">The component is rendered again.</span></span>

<span data-ttu-id="348be-177">Runtime porovná nový obsah s předchozím obsahem a použije pouze změněný obsah na objektový model dokumentu (DOM).</span><span class="sxs-lookup"><span data-stu-id="348be-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="348be-178">Přidejte komponentu do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="348be-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="348be-179">Můžete například `Counter` přidat komponentu na domovskou stránku aplikace přidáním `<Counter />` prvku do komponenty. `Index`</span><span class="sxs-lookup"><span data-stu-id="348be-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="348be-180">*Stránky/Index.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="348be-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="348be-181">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="348be-181">Run the app.</span></span> <span data-ttu-id="348be-182">Domovská stránka má svůj vlastní `Counter` čítač poskytovaný komponentou.</span><span class="sxs-lookup"><span data-stu-id="348be-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="348be-183">Parametry komponenty jsou určeny pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), které umožňují nastavit vlastnosti podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="348be-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="348be-184">Chcete-li do `Counter` komponenty přidat parametr, `@code` aktualizujte blok komponenty:</span><span class="sxs-lookup"><span data-stu-id="348be-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="348be-185">Přidejte veřejnou `IncrementAmount` vlastnost `[Parameter]` s atributem.</span><span class="sxs-lookup"><span data-stu-id="348be-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="348be-186">Změňte `IncrementCount` metodu, `IncrementAmount` která se `currentCount`má použít při zvyšování hodnoty .</span><span class="sxs-lookup"><span data-stu-id="348be-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="348be-187">*Stránky/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="348be-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="348be-188">Zadejte `IncrementAmount` prvek `Index` v `<Counter>` elementu komponenty pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="348be-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="348be-189">*Stránky/Index.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="348be-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="348be-190">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="348be-190">Run the app.</span></span> <span data-ttu-id="348be-191">Komponenta `Index` má svůj vlastní čítač, který se pohybuje o deset pokaždé, když je vybráno tlačítko **Klikněte na mě.**</span><span class="sxs-lookup"><span data-stu-id="348be-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="348be-192">Komponenta `Counter` (*Counter.razor*) pokračuje `/counter` v přírůstcích o jednu.</span><span class="sxs-lookup"><span data-stu-id="348be-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="348be-193">Další kroky</span><span class="sxs-lookup"><span data-stu-id="348be-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="348be-194">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="348be-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
