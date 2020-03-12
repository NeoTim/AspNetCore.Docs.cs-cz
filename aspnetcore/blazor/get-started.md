---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením Blazor aplikace pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 89c7529d2b8ec97db731f7c7268e19937c398115
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083240"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="66cb5-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="66cb5-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="66cb5-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="66cb5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="66cb5-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="66cb5-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="66cb5-106">Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="66cb5-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="66cb5-107">Volitelně nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="66cb5-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="66cb5-108">Nainstalujte [sadu SDK .NET Core 3.1.102 nebo novější (Preview)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="66cb5-108">Install the [.NET Core 3.1.102 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="66cb5-109">Spusťte následující příkaz v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="66cb5-109">Run the following command in a command shell.</span></span> <span data-ttu-id="66cb5-110">Balíček [Microsoft. AspNetCore. Components. WebAssembly. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) obsahuje verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="66cb5-110">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview2.20160.5
   ```

   > [!NOTE]
   > <span data-ttu-id="66cb5-111">K použití šablony 3.1.102 verze 3,2 Preview 2 Blazor je **nutná** verze .NET Core SDK nebo novější.</span><span class="sxs-lookup"><span data-stu-id="66cb5-111">.NET Core SDK version 3.1.102 or later is **required** to use the 3.2 Preview 2 Blazor WebAssembly template.</span></span> <span data-ttu-id="66cb5-112">Ověřte nainstalovanou verzi .NET Core SDK spuštěním `dotnet --version` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="66cb5-112">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="66cb5-113">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="66cb5-113">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studio"></a>[<span data-ttu-id="66cb5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66cb5-114">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="66cb5-115">1\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-115">1\.</span></span> <span data-ttu-id="66cb5-116">Nainstalujte [Visual Studio 2019 verze 16,4 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="66cb5-116">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="66cb5-117">2\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-117">2\.</span></span> <span data-ttu-id="66cb5-118">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="66cb5-118">Create a new project.</span></span>

   <span data-ttu-id="66cb5-119">3\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-119">3\.</span></span> <span data-ttu-id="66cb5-120">Vyberte **aplikaci Blazor**.</span><span class="sxs-lookup"><span data-stu-id="66cb5-120">Select **Blazor App**.</span></span> <span data-ttu-id="66cb5-121">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="66cb5-121">Select **Next**.</span></span>

   <span data-ttu-id="66cb5-122">4\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-122">4\.</span></span> <span data-ttu-id="66cb5-123">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="66cb5-123">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="66cb5-124">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="66cb5-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="66cb5-125">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="66cb5-125">Select **Create**.</span></span>

   <span data-ttu-id="66cb5-126">5\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-126">5\.</span></span> <span data-ttu-id="66cb5-127">Pro prostředí WebAssembly Blazor vyberte šablonu **aplikace Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="66cb5-127">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="66cb5-128">Pro prostředí Blazor serveru vyberte šablonu **aplikace serveru Blazor** .</span><span class="sxs-lookup"><span data-stu-id="66cb5-128">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="66cb5-129">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="66cb5-129">Select **Create**.</span></span> <span data-ttu-id="66cb5-130">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="66cb5-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span> <span data-ttu-id="66cb5-131">Pokud šablona WebAssembly Blazor není přítomna, vraťte se k předchozímu kroku a znovu nainstalujte šablonu.</span><span class="sxs-lookup"><span data-stu-id="66cb5-131">If the Blazor WebAssembly template isn't present, return to the previous step and reinstall the template.</span></span>

   <span data-ttu-id="66cb5-132">6\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-132">6\.</span></span> <span data-ttu-id="66cb5-133">Spusťte aplikaci stisknutím klávesy **Ctrl**+**F5** .</span><span class="sxs-lookup"><span data-stu-id="66cb5-133">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="66cb5-134">Pokud jste nainstalovali rozšíření sady Visual Studio Blazor pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="66cb5-134">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="66cb5-135">Instalace šablon Blazor do příkazového prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="66cb5-135">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-code"></a>[<span data-ttu-id="66cb5-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66cb5-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="66cb5-137">1\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-137">1\.</span></span> <span data-ttu-id="66cb5-138">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="66cb5-138">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="66cb5-139">2\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-139">2\.</span></span> <span data-ttu-id="66cb5-140">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="66cb5-140">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

   <span data-ttu-id="66cb5-141">3\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-141">3\.</span></span> <span data-ttu-id="66cb5-142">Pro prostředí WebAssembly Blazor spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="66cb5-142">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="66cb5-143">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="66cb5-143">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="66cb5-144">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="66cb5-144">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="66cb5-145">4\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-145">4\.</span></span> <span data-ttu-id="66cb5-146">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="66cb5-146">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="66cb5-147">5\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-147">5\.</span></span> <span data-ttu-id="66cb5-148">Pro projekt Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="66cb5-148">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="66cb5-149">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="66cb5-149">Select **Yes**.</span></span>

   <span data-ttu-id="66cb5-150">6\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-150">6\.</span></span> <span data-ttu-id="66cb5-151">Pokud používáte aplikaci serveru Blazor, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="66cb5-151">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="66cb5-152">Pokud používáte aplikaci WebAssembly Blazor, proveďte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="66cb5-152">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="66cb5-153">7\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-153">7\.</span></span> <span data-ttu-id="66cb5-154">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="66cb5-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66cb5-155">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="66cb5-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="66cb5-156">1\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-156">1\.</span></span> <span data-ttu-id="66cb5-157">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="66cb5-157">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="66cb5-158">2\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-158">2\.</span></span> <span data-ttu-id="66cb5-159">Vyberte **soubor** > **nové řešení** nebo vytvořte **Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="66cb5-159">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="66cb5-160">3\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-160">3\.</span></span> <span data-ttu-id="66cb5-161">Na bočním panelu vyberte **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="66cb5-161">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="66cb5-162">4\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-162">4\.</span></span> <span data-ttu-id="66cb5-163">Vyberte šablonu **aplikace Blazor serveru** .</span><span class="sxs-lookup"><span data-stu-id="66cb5-163">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="66cb5-164">V Visual Studio pro Mac je v tuto chvíli dostupná jenom šablona serveru Blazor.</span><span class="sxs-lookup"><span data-stu-id="66cb5-164">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="66cb5-165">Pro prostředí WebAssembly Blazor postupujte podle pokynů na kartě **.NET Core CLI** . Po výběru šablony serveru Blazor vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="66cb5-165">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="66cb5-166">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="66cb5-166">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="66cb5-167">5\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-167">5\.</span></span> <span data-ttu-id="66cb5-168">Nastavte **cílovou architekturu** na **.NET Core 3,1** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="66cb5-168">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="66cb5-169">6\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-169">6\.</span></span> <span data-ttu-id="66cb5-170">Do pole **název projektu** název aplikace `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="66cb5-170">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="66cb5-171">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="66cb5-171">Select **Create**.</span></span>

   <span data-ttu-id="66cb5-172">7\.</span><span class="sxs-lookup"><span data-stu-id="66cb5-172">7\.</span></span> <span data-ttu-id="66cb5-173">Vyberte **spustit** > **Spustit bez ladění** pro spuštění aplikace *bez ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="66cb5-173">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="66cb5-174">Spusťte aplikaci s použitím **Spustit ladění** a spusťte aplikaci *pomocí ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="66cb5-174">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="66cb5-175">Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="66cb5-175">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-cli"></a>[<span data-ttu-id="66cb5-176">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="66cb5-176">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="66cb5-177">Pro prostředí WebAssembly Blazor spusťte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="66cb5-177">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="66cb5-178">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="66cb5-178">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="66cb5-179">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="66cb5-179">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="66cb5-180">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="66cb5-180">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="66cb5-181">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="66cb5-181">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="66cb5-182">Domů</span><span class="sxs-lookup"><span data-stu-id="66cb5-182">Home</span></span>
* <span data-ttu-id="66cb5-183">Čítač</span><span class="sxs-lookup"><span data-stu-id="66cb5-183">Counter</span></span>
* <span data-ttu-id="66cb5-184">Načíst data</span><span class="sxs-lookup"><span data-stu-id="66cb5-184">Fetch data</span></span>

<span data-ttu-id="66cb5-185">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="66cb5-185">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="66cb5-186">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít C#.</span><span class="sxs-lookup"><span data-stu-id="66cb5-186">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="66cb5-187">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="66cb5-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="66cb5-188">Žádost o `/counter` v prohlížeči, jak je uvedeno v direktivě `@page` v horní části, způsobí, že komponenta `Counter` vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="66cb5-188">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="66cb5-189">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="66cb5-189">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="66cb5-190">Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :</span><span class="sxs-lookup"><span data-stu-id="66cb5-190">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="66cb5-191">Událost `onclick` je aktivována.</span><span class="sxs-lookup"><span data-stu-id="66cb5-191">The `onclick` event is fired.</span></span>
* <span data-ttu-id="66cb5-192">Je volána metoda `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="66cb5-192">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="66cb5-193">`currentCount` se zvyšuje.</span><span class="sxs-lookup"><span data-stu-id="66cb5-193">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="66cb5-194">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="66cb5-194">The component is rendered again.</span></span>

<span data-ttu-id="66cb5-195">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="66cb5-195">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="66cb5-196">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="66cb5-196">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="66cb5-197">Přidejte například komponentu `Counter` do domovské stránky aplikace přidáním prvku `<Counter />` do komponenty `Index`.</span><span class="sxs-lookup"><span data-stu-id="66cb5-197">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="66cb5-198">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="66cb5-198">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="66cb5-199">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="66cb5-199">Run the app.</span></span> <span data-ttu-id="66cb5-200">Domovská stránka má svůj vlastní čítač poskytovaný komponentou `Counter`.</span><span class="sxs-lookup"><span data-stu-id="66cb5-200">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="66cb5-201">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="66cb5-201">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="66cb5-202">Chcete-li přidat parametr do součásti `Counter`, aktualizujte blok `@code` součásti:</span><span class="sxs-lookup"><span data-stu-id="66cb5-202">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="66cb5-203">Přidejte veřejnou vlastnost pro `IncrementAmount` s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="66cb5-203">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="66cb5-204">Změňte metodu `IncrementCount` na použití `IncrementAmount` při zvyšování hodnoty `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="66cb5-204">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="66cb5-205">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="66cb5-205">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="66cb5-206">Určete `IncrementAmount` v prvku `<Counter>` komponenty `Index` pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="66cb5-206">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="66cb5-207">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="66cb5-207">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="66cb5-208">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="66cb5-208">Run the app.</span></span> <span data-ttu-id="66cb5-209">Komponenta `Index` má vlastní čítač, který při každém výběru tlačítka pro **kliknutí na tlačítko Další** zvyšuje o 10.</span><span class="sxs-lookup"><span data-stu-id="66cb5-209">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="66cb5-210">Komponenta `Counter` (*Counter. Razor*) se v `/counter` nadále zvětšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="66cb5-210">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="66cb5-211">Další kroky</span><span class="sxs-lookup"><span data-stu-id="66cb5-211">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="66cb5-212">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="66cb5-212">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
