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
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 538257597ec5c6c705d8280a817c409debe22224
ms.sourcegitcommit: c6467f86c09b1f608b09d37d33c8c43de7ae8bc7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2020
ms.locfileid: "85946815"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="3a6e1-103">Nástroje pro ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="3a6e1-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="3a6e1-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3a6e1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="os-windows"

1. <span data-ttu-id="3a6e1-105">Nainstalujte nejnovější verzi sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="3a6e1-106">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="3a6e1-106">Create a new project.</span></span>

1. <span data-ttu-id="3a6e1-107">Vyberte možnost \*\* Blazor aplikace\*\*.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-107">Select **Blazor App**.</span></span> <span data-ttu-id="3a6e1-108">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-108">Select **Next**.</span></span>

1. <span data-ttu-id="3a6e1-109">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3a6e1-110">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3a6e1-111">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-111">Select **Create**.</span></span>

1. <span data-ttu-id="3a6e1-112">Pro Blazor WebAssembly prostředí vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3a6e1-113">Pro Blazor Server prostředí vyberte šablonu \*\* Blazor Server aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3a6e1-114">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-114">Select **Create**.</span></span>

   <span data-ttu-id="3a6e1-115">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3a6e1-116">Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-linux"

1. <span data-ttu-id="3a6e1-117">Nainstalujte nejnovější verzi sady [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="3a6e1-117">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="3a6e1-118">Pokud jste dříve nainstalovali sadu SDK, můžete zjistit nainstalovanou verzi spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="3a6e1-118">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="3a6e1-119">Nainstalujte nejnovější verzi [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="3a6e1-119">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="3a6e1-120">Nainstalujte nejnovější [rozšíření C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a [ladicí program JavaScript (v noci)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) s `debug.javascript.usePreview` nastavením na `true` .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-120">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   <span data-ttu-id="3a6e1-121">Pokud chcete `debug.javascript.usePreview` nastavit `true` použití uživatelského rozhraní vs Code, otevřete **File**  >  **Preferences**  >  **Nastavení** předvoleb souborů a vyhledejte `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-121">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="3a6e1-122">Zaškrtněte políčko pro **použití novinového ladicího programu JavaScriptu v Preview pro Node.js a Chrome**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-122">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="3a6e1-123">Pro Blazor WebAssembly prostředí spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="3a6e1-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="3a6e1-124">Pro Blazor Server prostředí spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="3a6e1-124">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="3a6e1-125">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-125">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3a6e1-126">Otevřete `WebApplication1` složku v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-126">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="3a6e1-127">Rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-127">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="3a6e1-128">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-128">Select **Yes**.</span></span>

1. <span data-ttu-id="3a6e1-129">Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-129">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-macos"

1. <span data-ttu-id="3a6e1-130">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="3a6e1-130">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="3a6e1-131">Vyberte **soubor**  >  **nové řešení** nebo vytvořte **Nový** projekt z **okna Start**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-131">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="3a6e1-132">Na bočním panelu vyberte **Webová a konzolová**  >  **aplikace**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-132">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="3a6e1-133">Pro Blazor WebAssembly prostředí vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-133">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3a6e1-134">Pro Blazor Server prostředí vyberte šablonu \*\* Blazor Server aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-134">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3a6e1-135">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-135">Select **Next**.</span></span>

   <span data-ttu-id="3a6e1-136">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-136">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3a6e1-137">Potvrďte následující konfigurace:</span><span class="sxs-lookup"><span data-stu-id="3a6e1-137">Confirm the following configurations:</span></span>

   * <span data-ttu-id="3a6e1-138">**Cílová architektura** je nastavená na **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-138">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="3a6e1-139">**Ověřování** nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-139">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="3a6e1-140">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-140">Select **Next**.</span></span>

1. <span data-ttu-id="3a6e1-141">Do pole **název projektu** název aplikace `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-141">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="3a6e1-142">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-142">Select **Create**.</span></span>

1. <span data-ttu-id="3a6e1-143">**Run**  >  Chcete-li spustit aplikaci *bez ladicího programu*, vyberte možnost spustit**bez ladění** .</span><span class="sxs-lookup"><span data-stu-id="3a6e1-143">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="3a6e1-144">Spusťte aplikaci pomocí **příkazového**  >  **počátečního ladění** nebo tlačítka Spustit (&#9654;), aby se aplikace spustila *s ladicím programem*.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-144">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="3a6e1-145">Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-145">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="3a6e1-146">Certifikát uživatele a řetězce klíčů je nutný k důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="3a6e1-146">The user and keychain passwords are required to trust the certificate.</span></span>

::: zone-end
