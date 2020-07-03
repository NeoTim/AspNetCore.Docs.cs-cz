---
title: Nástroje pro ASP.NET CoreBlazor
author: guardrex
description: Přečtěte si informace o nástrojích dostupných pro sestavování Blazor aplikací.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
ms.openlocfilehash: 3ff610225c798624b7ead7d365924ae3d193829d
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944950"
---
<!-- zone_pivot_groups: operating-systems -->

# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="429ed-103">Nástroje pro ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="429ed-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="429ed-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="429ed-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="429ed-105">Vyberte nástroje pro vaši platformu:</span><span class="sxs-lookup"><span data-stu-id="429ed-105">Select the tooling for your platform:</span></span>

* <span data-ttu-id="429ed-106">Windows: vyberte kartu sady **Visual Studio** .</span><span class="sxs-lookup"><span data-stu-id="429ed-106">Windows: Select the **Visual Studio** tab.</span></span>
* <span data-ttu-id="429ed-107">Linux: vyberte kartu **Visual Studio Code** .</span><span class="sxs-lookup"><span data-stu-id="429ed-107">Linux: Select the **Visual Studio Code** tab.</span></span>
* <span data-ttu-id="429ed-108">macOS: vyberte kartu **Visual Studio pro Mac** .</span><span class="sxs-lookup"><span data-stu-id="429ed-108">macOS: Select the **Visual Studio for Mac** tab.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="429ed-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="429ed-109">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="429ed-110">Nainstalujte nejnovější verzi sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="429ed-110">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="429ed-111">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="429ed-111">Create a new project.</span></span>

1. <span data-ttu-id="429ed-112">Vyberte možnost \*\* Blazor aplikace\*\*.</span><span class="sxs-lookup"><span data-stu-id="429ed-112">Select **Blazor App**.</span></span> <span data-ttu-id="429ed-113">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="429ed-113">Select **Next**.</span></span>

1. <span data-ttu-id="429ed-114">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="429ed-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="429ed-115">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="429ed-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="429ed-116">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="429ed-116">Select **Create**.</span></span>

1. <span data-ttu-id="429ed-117">Pro Blazor WebAssembly prostředí vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="429ed-117">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="429ed-118">Pro Blazor Server prostředí vyberte šablonu \*\* Blazor Server aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="429ed-118">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="429ed-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="429ed-119">Select **Create**.</span></span>

   <span data-ttu-id="429ed-120">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="429ed-120">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="429ed-121">Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="429ed-121">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="429ed-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="429ed-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="429ed-123">Nainstalujte nejnovější verzi sady [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="429ed-123">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="429ed-124">Pokud jste dříve nainstalovali sadu SDK, můžete zjistit nainstalovanou verzi spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="429ed-124">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="429ed-125">Nainstalujte nejnovější verzi [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="429ed-125">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="429ed-126">Nainstalujte nejnovější [rozšíření C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a [ladicí program JavaScript (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavením na `true` .</span><span class="sxs-lookup"><span data-stu-id="429ed-126">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   <span data-ttu-id="429ed-127">Pokud chcete `debug.javascript.usePreview` nastavit `true` použití uživatelského rozhraní vs Code, otevřete **File**  >  **Preferences**  >  **Nastavení** předvoleb souborů a vyhledejte `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="429ed-127">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="429ed-128">Zaškrtněte políčko pro **použití novinového ladicího programu JavaScriptu v Preview pro Node.js a Chrome**.</span><span class="sxs-lookup"><span data-stu-id="429ed-128">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="429ed-129">Pro Blazor WebAssembly prostředí spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="429ed-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="429ed-130">Pro Blazor Server prostředí spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="429ed-130">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="429ed-131">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="429ed-131">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="429ed-132">Otevřete `WebApplication1` složku v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="429ed-132">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="429ed-133">Rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="429ed-133">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="429ed-134">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="429ed-134">Select **Yes**.</span></span>

1. <span data-ttu-id="429ed-135">Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="429ed-135">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="429ed-136">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="429ed-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="429ed-137">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="429ed-137">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="429ed-138">Vyberte **soubor**  >  **nové řešení** nebo vytvořte **Nový** projekt z **okna Start**.</span><span class="sxs-lookup"><span data-stu-id="429ed-138">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="429ed-139">Na bočním panelu vyberte **Webová a konzolová**  >  **aplikace**.</span><span class="sxs-lookup"><span data-stu-id="429ed-139">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="429ed-140">Pro Blazor WebAssembly prostředí vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="429ed-140">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="429ed-141">Pro Blazor Server prostředí vyberte šablonu \*\* Blazor Server aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="429ed-141">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="429ed-142">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="429ed-142">Select **Next**.</span></span>

   <span data-ttu-id="429ed-143">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="429ed-143">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="429ed-144">Potvrďte následující konfigurace:</span><span class="sxs-lookup"><span data-stu-id="429ed-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="429ed-145">**Cílová architektura** je nastavená na **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="429ed-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="429ed-146">**Ověřování** nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="429ed-146">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="429ed-147">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="429ed-147">Select **Next**.</span></span>

1. <span data-ttu-id="429ed-148">Do pole **název projektu** název aplikace `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="429ed-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="429ed-149">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="429ed-149">Select **Create**.</span></span>

1. <span data-ttu-id="429ed-150">**Run**  >  Chcete-li spustit aplikaci *bez ladicího programu*, vyberte možnost spustit**bez ladění** .</span><span class="sxs-lookup"><span data-stu-id="429ed-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="429ed-151">Spusťte aplikaci pomocí **příkazového**  >  **počátečního ladění** nebo tlačítka Spustit (&#9654;), aby se aplikace spustila *s ladicím programem*.</span><span class="sxs-lookup"><span data-stu-id="429ed-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="429ed-152">Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="429ed-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="429ed-153">Certifikát uživatele a řetězce klíčů je nutný k důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="429ed-153">The user and keychain passwords are required to trust the certificate.</span></span>

---

<!--

::: zone pivot="os-windows"

1. Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.

1. Create a new project.

1. Select **Blazor App**. Select **Next**.

1. Provide a project name in the **Project name** field or accept the default project name. Confirm the **Location** entry is correct or provide a location for the project. Select **Create**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Create**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-linux"

1. Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:

   ```dotnetcli
   dotnet --version
   ```

1. Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).

1. Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.

   To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`. Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.

1. For a Blazor WebAssembly experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   For a Blazor Server experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Open the `WebApplication1` folder in Visual Studio Code.

1. The IDE requests that you add assets to build and debug the project. Select **Yes**.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-macos"

1. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

   For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

If a prompt appears to trust the development certificate, trust the certificate and continue. The user and keychain passwords are required to trust the certificate.

::: zone-end

-->
