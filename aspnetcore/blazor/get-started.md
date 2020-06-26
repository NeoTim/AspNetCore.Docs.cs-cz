---
title: Začínáme s ASP.NET CoreBlazor
author: guardrex
description: Začněte Blazor tím, že sestavíte Blazor aplikaci s vámi zvoleným nástrojem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 1eabc35175d1b696de99488981b1382d231f5544
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402777"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="37ee6-103">Začínáme s ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="37ee6-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="37ee6-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="37ee6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="37ee6-105">Pokud chcete začít používat Blazor , postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="37ee6-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37ee6-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37ee6-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="37ee6-107">Nainstalujte nejnovější verzi sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="37ee6-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="37ee6-108">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="37ee6-108">Create a new project.</span></span>

1. <span data-ttu-id="37ee6-109">Vyberte možnost \*\* Blazor aplikace\*\*.</span><span class="sxs-lookup"><span data-stu-id="37ee6-109">Select **Blazor App**.</span></span> <span data-ttu-id="37ee6-110">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-110">Select **Next**.</span></span>

1. <span data-ttu-id="37ee6-111">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="37ee6-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="37ee6-112">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="37ee6-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="37ee6-113">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-113">Select **Create**.</span></span>

1. <span data-ttu-id="37ee6-114">Pro Blazor WebAssembly prostředí vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="37ee6-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="37ee6-115">Pro Blazor Server prostředí vyberte šablonu \*\* Blazor Server aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="37ee6-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="37ee6-116">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-116">Select **Create**.</span></span>

   <span data-ttu-id="37ee6-117">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="37ee6-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="37ee6-118">Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="37ee6-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="37ee6-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37ee6-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="37ee6-120">Nainstalujte nejnovější verzi sady [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="37ee6-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="37ee6-121">Pokud jste dříve nainstalovali sadu SDK, můžete zjistit nainstalovanou verzi spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="37ee6-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="37ee6-122">Nainstalujte nejnovější verzi [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="37ee6-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="37ee6-123">Nainstalujte nejnovější [rozšíření C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a [ladicí program JavaScript (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavením na `true` .</span><span class="sxs-lookup"><span data-stu-id="37ee6-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="37ee6-124">Pokud chcete `debug.javascript.usePreview` nastavit `true` použití uživatelského rozhraní vs Code, otevřete **File**  >  **Preferences**  >  **Nastavení** předvoleb souborů a vyhledejte `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="37ee6-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="37ee6-125">Zaškrtněte políčko pro **použití novinového ladicího programu JavaScriptu v Preview pro Node.js a Chrome**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="37ee6-126">Pro Blazor WebAssembly prostředí spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="37ee6-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="37ee6-127">Pro Blazor Server prostředí spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="37ee6-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="37ee6-128">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="37ee6-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="37ee6-129">Otevřete `WebApplication1` složku v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="37ee6-129">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="37ee6-130">Rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="37ee6-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="37ee6-131">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-131">Select **Yes**.</span></span>

1. <span data-ttu-id="37ee6-132">Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="37ee6-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="37ee6-133">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="37ee6-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="37ee6-134">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="37ee6-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="37ee6-135">Vyberte **soubor**  >  **nové řešení** nebo vytvořte **Nový** projekt z **okna Start**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="37ee6-136">Na bočním panelu vyberte **Webová a konzolová**  >  **aplikace**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="37ee6-137">Pro Blazor WebAssembly prostředí vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="37ee6-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="37ee6-138">Pro Blazor Server prostředí vyberte šablonu \*\* Blazor Server aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="37ee6-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="37ee6-139">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-139">Select **Next**.</span></span>

   <span data-ttu-id="37ee6-140">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="37ee6-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="37ee6-141">Potvrďte následující konfigurace:</span><span class="sxs-lookup"><span data-stu-id="37ee6-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="37ee6-142">**Cílová architektura** je nastavená na **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="37ee6-143">**Ověřování** nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="37ee6-144">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-144">Select **Next**.</span></span>

1. <span data-ttu-id="37ee6-145">Do pole **název projektu** název aplikace `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="37ee6-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="37ee6-146">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="37ee6-146">Select **Create**.</span></span>

1. <span data-ttu-id="37ee6-147">**Run**  >  Chcete-li spustit aplikaci *bez ladicího programu*, vyberte možnost spustit**bez ladění** .</span><span class="sxs-lookup"><span data-stu-id="37ee6-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="37ee6-148">Spusťte aplikaci pomocí **příkazového**  >  **počátečního ladění** nebo tlačítka Spustit (&#9654;), aby se aplikace spustila *s ladicím programem*.</span><span class="sxs-lookup"><span data-stu-id="37ee6-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="37ee6-149">Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="37ee6-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="37ee6-150">Certifikát uživatele a řetězce klíčů je nutný k důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="37ee6-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="37ee6-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="37ee6-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="37ee6-152">Nainstalujte nejnovější verzi sady [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="37ee6-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="37ee6-153">Pokud jste dříve nainstalovali sadu SDK, můžete zjistit nainstalovanou verzi spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="37ee6-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="37ee6-154">Pro Blazor WebAssembly prostředí spusťte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="37ee6-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="37ee6-155">Pro Blazor Server prostředí spusťte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="37ee6-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="37ee6-156">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="37ee6-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="37ee6-157">V prohlížeči přejděte na `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="37ee6-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="37ee6-158">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="37ee6-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="37ee6-159">Domů</span><span class="sxs-lookup"><span data-stu-id="37ee6-159">Home</span></span>
* <span data-ttu-id="37ee6-160">Čítač</span><span class="sxs-lookup"><span data-stu-id="37ee6-160">Counter</span></span>
* <span data-ttu-id="37ee6-161">Načíst data</span><span class="sxs-lookup"><span data-stu-id="37ee6-161">Fetch data</span></span>

<span data-ttu-id="37ee6-162">Na stránce čítač vyberte tlačítko pro zvýšení čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="37ee6-162">On the Counter page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="37ee6-163">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít jazyk C#.</span><span class="sxs-lookup"><span data-stu-id="37ee6-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="37ee6-164">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="37ee6-164">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="37ee6-165">Požadavek na `/counter` v prohlížeči, jak je uveden v `@page` direktivě nahoře, způsobí, že `Counter` Komponenta vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="37ee6-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="37ee6-166">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="37ee6-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="37ee6-167">Pokaždé, když je vybráno tlačítko:</span><span class="sxs-lookup"><span data-stu-id="37ee6-167">Each time the button is selected:</span></span>

* <span data-ttu-id="37ee6-168">`onclick`Událost je aktivována.</span><span class="sxs-lookup"><span data-stu-id="37ee6-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="37ee6-169">`IncrementCount`Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="37ee6-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="37ee6-170">`currentCount`Dojde k zvýšení.</span><span class="sxs-lookup"><span data-stu-id="37ee6-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="37ee6-171">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="37ee6-171">The component is rendered again.</span></span>

<span data-ttu-id="37ee6-172">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="37ee6-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="37ee6-173">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="37ee6-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="37ee6-174">Například přidejte `Counter` komponentu do domovské stránky aplikace přidáním `<Counter />` elementu do `Index` komponenty.</span><span class="sxs-lookup"><span data-stu-id="37ee6-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="37ee6-175">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="37ee6-175">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="37ee6-176">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="37ee6-176">Run the app.</span></span> <span data-ttu-id="37ee6-177">Domovská stránka má vlastní počítadlo poskytované `Counter` komponentou.</span><span class="sxs-lookup"><span data-stu-id="37ee6-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="37ee6-178">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components/index#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="37ee6-178">Component parameters are specified using attributes or [child content](xref:blazor/components/index#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="37ee6-179">Chcete-li přidat parametr do `Counter` komponenty, aktualizujte blok komponenty `@code` :</span><span class="sxs-lookup"><span data-stu-id="37ee6-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="37ee6-180">Přidejte veřejnou vlastnost pro `IncrementAmount` s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="37ee6-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="37ee6-181">Změňte `IncrementCount` metodu na použití `IncrementAmount` při zvyšování hodnoty `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="37ee6-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="37ee6-182">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="37ee6-182">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="37ee6-183">Zadejte `IncrementAmount` v `Index` `<Counter>` elementu komponenty pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="37ee6-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="37ee6-184">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="37ee6-184">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="37ee6-185">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="37ee6-185">Run the app.</span></span> <span data-ttu-id="37ee6-186">`Index`Komponenta má vlastní čítač, který se zvýší o deset pokaždé, když je vybráno tlačítko.</span><span class="sxs-lookup"><span data-stu-id="37ee6-186">The `Index` component has its own counter that increments by ten each time the button is selected.</span></span> <span data-ttu-id="37ee6-187">`Counter`Komponenta () se v této části `Pages/Counter.razor` `/counter` stále zvyšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="37ee6-187">The `Counter` component (`Pages/Counter.razor`) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="37ee6-188">Další kroky</span><span class="sxs-lookup"><span data-stu-id="37ee6-188">Next steps</span></span>

<span data-ttu-id="37ee6-189">Vytvoření Blazor aplikace krok za krokem:</span><span class="sxs-lookup"><span data-stu-id="37ee6-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="37ee6-190">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="37ee6-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
