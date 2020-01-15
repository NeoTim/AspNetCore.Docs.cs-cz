---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením Blazor aplikace pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: 2135c2a090d60ec7a46fa4f899f0f14987b6b4e0
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75951712"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="617b2-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="617b2-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="617b2-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="617b2-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="617b2-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="617b2-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="617b2-106">Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="617b2-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="617b2-107">Volitelně nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="617b2-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="617b2-108">Nainstalujte [sadu SDK .NET Core 3,1 nebo novější (Preview)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="617b2-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="617b2-109">Spusťte následující příkaz v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="617b2-109">Run the following command in a command shell.</span></span> <span data-ttu-id="617b2-110">[Microsoft.AspNetCore.Blazor.Balíček šablon](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) má verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="617b2-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="617b2-111">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="617b2-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="617b2-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="617b2-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="617b2-113">1\.</span><span class="sxs-lookup"><span data-stu-id="617b2-113">1\.</span></span> <span data-ttu-id="617b2-114">Nainstalujte [Visual Studio 16,4 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="617b2-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="617b2-115">2\.</span><span class="sxs-lookup"><span data-stu-id="617b2-115">2\.</span></span> <span data-ttu-id="617b2-116">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="617b2-116">Create a new project.</span></span>

   <span data-ttu-id="617b2-117">3\.</span><span class="sxs-lookup"><span data-stu-id="617b2-117">3\.</span></span> <span data-ttu-id="617b2-118">Vyberte **Blazor aplikace**.</span><span class="sxs-lookup"><span data-stu-id="617b2-118">Select **Blazor App**.</span></span> <span data-ttu-id="617b2-119">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="617b2-119">Select **Next**.</span></span>

   <span data-ttu-id="617b2-120">4\.</span><span class="sxs-lookup"><span data-stu-id="617b2-120">4\.</span></span> <span data-ttu-id="617b2-121">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="617b2-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="617b2-122">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="617b2-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="617b2-123">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="617b2-123">Select **Create**.</span></span>

   <span data-ttu-id="617b2-124">5\.</span><span class="sxs-lookup"><span data-stu-id="617b2-124">5\.</span></span> <span data-ttu-id="617b2-125">Pro Blazor prostředí WebAssembly vyberte šablonu **aplikaceBlazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="617b2-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="617b2-126">Pro prostředí Blazor serveru vyberte šablonu **aplikaceBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="617b2-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="617b2-127">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="617b2-127">Select **Create**.</span></span> <span data-ttu-id="617b2-128">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="617b2-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="617b2-129">6\.</span><span class="sxs-lookup"><span data-stu-id="617b2-129">6\.</span></span> <span data-ttu-id="617b2-130">Spusťte aplikaci stisknutím klávesy **Ctrl**+**F5** .</span><span class="sxs-lookup"><span data-stu-id="617b2-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="617b2-131">Pokud jste nainstalovali rozšíření Blazor sady Visual Studio pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="617b2-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="617b2-132">Instalace šablon Blazor v příkazovém prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="617b2-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="617b2-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="617b2-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="617b2-134">1\.</span><span class="sxs-lookup"><span data-stu-id="617b2-134">1\.</span></span> <span data-ttu-id="617b2-135">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="617b2-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="617b2-136">2\.</span><span class="sxs-lookup"><span data-stu-id="617b2-136">2\.</span></span> <span data-ttu-id="617b2-137">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="617b2-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="617b2-138">3\.</span><span class="sxs-lookup"><span data-stu-id="617b2-138">3\.</span></span> <span data-ttu-id="617b2-139">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="617b2-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="617b2-140">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="617b2-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="617b2-141">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="617b2-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="617b2-142">4\.</span><span class="sxs-lookup"><span data-stu-id="617b2-142">4\.</span></span> <span data-ttu-id="617b2-143">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="617b2-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="617b2-144">5\.</span><span class="sxs-lookup"><span data-stu-id="617b2-144">5\.</span></span> <span data-ttu-id="617b2-145">V případě projektu Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="617b2-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="617b2-146">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="617b2-146">Select **Yes**.</span></span>

   <span data-ttu-id="617b2-147">6\.</span><span class="sxs-lookup"><span data-stu-id="617b2-147">6\.</span></span> <span data-ttu-id="617b2-148">Pokud používáte aplikaci Blazor serveru, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="617b2-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="617b2-149">Pokud používáte aplikaci Blazor WebAssembly, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="617b2-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="617b2-150">7\.</span><span class="sxs-lookup"><span data-stu-id="617b2-150">7\.</span></span> <span data-ttu-id="617b2-151">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="617b2-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="617b2-152">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="617b2-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="617b2-153">1\.</span><span class="sxs-lookup"><span data-stu-id="617b2-153">1\.</span></span> <span data-ttu-id="617b2-154">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="617b2-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="617b2-155">2\.</span><span class="sxs-lookup"><span data-stu-id="617b2-155">2\.</span></span> <span data-ttu-id="617b2-156">Vyberte **soubor** > **nové řešení** nebo vytvořte **Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="617b2-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="617b2-157">3\.</span><span class="sxs-lookup"><span data-stu-id="617b2-157">3\.</span></span> <span data-ttu-id="617b2-158">Na bočním panelu vyberte **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="617b2-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="617b2-159">4\.</span><span class="sxs-lookup"><span data-stu-id="617b2-159">4\.</span></span> <span data-ttu-id="617b2-160">Vyberte šablonu **aplikaceBlazorového serveru** .</span><span class="sxs-lookup"><span data-stu-id="617b2-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="617b2-161">V tuto chvíli je v Visual Studio pro Mac dostupná jenom šablona serveru Blazor.</span><span class="sxs-lookup"><span data-stu-id="617b2-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="617b2-162">Pro Blazor prostředí WebAssembly postupujte podle pokynů na kartě **.NET Core CLI** . Po výběru šablony Blazor serveru vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="617b2-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="617b2-163">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="617b2-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="617b2-164">5\.</span><span class="sxs-lookup"><span data-stu-id="617b2-164">5\.</span></span> <span data-ttu-id="617b2-165">Nastavte **cílovou architekturu** na **.NET Core 3,1** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="617b2-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="617b2-166">6\.</span><span class="sxs-lookup"><span data-stu-id="617b2-166">6\.</span></span> <span data-ttu-id="617b2-167">Do pole **název projektu** název aplikace `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="617b2-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="617b2-168">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="617b2-168">Select **Create**.</span></span>

   <span data-ttu-id="617b2-169">7\.</span><span class="sxs-lookup"><span data-stu-id="617b2-169">7\.</span></span> <span data-ttu-id="617b2-170">Vyberte **spustit** > **Spustit bez ladění** pro spuštění aplikace *bez ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="617b2-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="617b2-171">Spusťte aplikaci s použitím **Spustit ladění** a spusťte aplikaci *pomocí ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="617b2-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="617b2-172">Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="617b2-172">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="617b2-173">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="617b2-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="617b2-174">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="617b2-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="617b2-175">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="617b2-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="617b2-176">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="617b2-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="617b2-177">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="617b2-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="617b2-178">Nainstalujte si nejnovější verzi [sady .NET Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span><span class="sxs-lookup"><span data-stu-id="617b2-178">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="617b2-179">Volitelně nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="617b2-179">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="617b2-180">Nainstalujte [sadu SDK .NET Core 3,1 nebo novější (Preview)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="617b2-180">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="617b2-181">Spusťte následující příkaz v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="617b2-181">Run the following command in a command shell.</span></span> <span data-ttu-id="617b2-182">[Microsoft.AspNetCore.Blazor.Balíček šablon](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) má verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="617b2-182">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="617b2-183">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="617b2-183">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="617b2-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="617b2-184">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="617b2-185">1\.</span><span class="sxs-lookup"><span data-stu-id="617b2-185">1\.</span></span> <span data-ttu-id="617b2-186">Nainstalujte si nejnovější verzi sady [Visual Studio](https://visualstudio.com/vs/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="617b2-186">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="617b2-187">2\.</span><span class="sxs-lookup"><span data-stu-id="617b2-187">2\.</span></span> <span data-ttu-id="617b2-188">Volitelně můžete nainstalovat [Visual Studio 16,4 Preview 2 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** pro Blazor vývoj aplikací pro WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="617b2-188">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="617b2-189">3\.</span><span class="sxs-lookup"><span data-stu-id="617b2-189">3\.</span></span> <span data-ttu-id="617b2-190">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="617b2-190">Create a new project.</span></span>

   <span data-ttu-id="617b2-191">4\.</span><span class="sxs-lookup"><span data-stu-id="617b2-191">4\.</span></span> <span data-ttu-id="617b2-192">Vyberte **Blazor aplikace**.</span><span class="sxs-lookup"><span data-stu-id="617b2-192">Select **Blazor App**.</span></span> <span data-ttu-id="617b2-193">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="617b2-193">Select **Next**.</span></span>

   <span data-ttu-id="617b2-194">5\.</span><span class="sxs-lookup"><span data-stu-id="617b2-194">5\.</span></span> <span data-ttu-id="617b2-195">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="617b2-195">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="617b2-196">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="617b2-196">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="617b2-197">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="617b2-197">Select **Create**.</span></span>

   <span data-ttu-id="617b2-198">6\.</span><span class="sxs-lookup"><span data-stu-id="617b2-198">6\.</span></span> <span data-ttu-id="617b2-199">Pro Blazor prostředí WebAssembly vyberte šablonu **aplikaceBlazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="617b2-199">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="617b2-200">Pro prostředí Blazor serveru vyberte šablonu **aplikaceBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="617b2-200">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="617b2-201">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="617b2-201">Select **Create**.</span></span> <span data-ttu-id="617b2-202">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="617b2-202">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="617b2-203">7\.</span><span class="sxs-lookup"><span data-stu-id="617b2-203">7\.</span></span> <span data-ttu-id="617b2-204">Stisknutím klávesy **F5** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="617b2-204">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="617b2-205">Pokud jste nainstalovali rozšíření Blazor sady Visual Studio pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="617b2-205">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="617b2-206">Instalace šablon Blazor v příkazovém prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="617b2-206">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="617b2-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="617b2-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="617b2-208">1\.</span><span class="sxs-lookup"><span data-stu-id="617b2-208">1\.</span></span> <span data-ttu-id="617b2-209">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="617b2-209">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="617b2-210">2\.</span><span class="sxs-lookup"><span data-stu-id="617b2-210">2\.</span></span> <span data-ttu-id="617b2-211">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="617b2-211">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="617b2-212">3\.</span><span class="sxs-lookup"><span data-stu-id="617b2-212">3\.</span></span> <span data-ttu-id="617b2-213">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="617b2-213">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="617b2-214">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="617b2-214">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="617b2-215">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="617b2-215">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="617b2-216">4\.</span><span class="sxs-lookup"><span data-stu-id="617b2-216">4\.</span></span> <span data-ttu-id="617b2-217">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="617b2-217">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="617b2-218">5\.</span><span class="sxs-lookup"><span data-stu-id="617b2-218">5\.</span></span> <span data-ttu-id="617b2-219">V případě projektu Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="617b2-219">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="617b2-220">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="617b2-220">Select **Yes**.</span></span>

   <span data-ttu-id="617b2-221">6\.</span><span class="sxs-lookup"><span data-stu-id="617b2-221">6\.</span></span> <span data-ttu-id="617b2-222">Pokud používáte aplikaci Blazor serveru, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="617b2-222">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="617b2-223">Pokud používáte aplikaci Blazor WebAssembly, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="617b2-223">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="617b2-224">7\.</span><span class="sxs-lookup"><span data-stu-id="617b2-224">7\.</span></span> <span data-ttu-id="617b2-225">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="617b2-225">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="617b2-226">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="617b2-226">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="617b2-227">1\.</span><span class="sxs-lookup"><span data-stu-id="617b2-227">1\.</span></span> <span data-ttu-id="617b2-228">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="617b2-228">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="617b2-229">Přepněte [kanál aktualizace na verzi Preview](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="617b2-229">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="617b2-230">2\.</span><span class="sxs-lookup"><span data-stu-id="617b2-230">2\.</span></span> <span data-ttu-id="617b2-231">Vyberte **soubor** > **nové řešení** nebo vytvořte **Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="617b2-231">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="617b2-232">3\.</span><span class="sxs-lookup"><span data-stu-id="617b2-232">3\.</span></span> <span data-ttu-id="617b2-233">Na bočním panelu vyberte **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="617b2-233">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="617b2-234">4\.</span><span class="sxs-lookup"><span data-stu-id="617b2-234">4\.</span></span> <span data-ttu-id="617b2-235">Vyberte šablonu **aplikaceBlazorového serveru** .</span><span class="sxs-lookup"><span data-stu-id="617b2-235">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="617b2-236">V tuto chvíli je v Visual Studio pro Mac dostupná jenom šablona serveru Blazor.</span><span class="sxs-lookup"><span data-stu-id="617b2-236">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="617b2-237">Pro Blazor prostředí WebAssembly postupujte podle pokynů na kartě **.NET Core CLI** . Po výběru šablony Blazor serveru vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="617b2-237">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="617b2-238">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="617b2-238">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="617b2-239">5\.</span><span class="sxs-lookup"><span data-stu-id="617b2-239">5\.</span></span> <span data-ttu-id="617b2-240">Nastavte **cílovou architekturu** na **.NET Core 3,0** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="617b2-240">Set the **Target Framework** to **.NET Core 3.0** and select **Next**.</span></span>

   <span data-ttu-id="617b2-241">6\.</span><span class="sxs-lookup"><span data-stu-id="617b2-241">6\.</span></span> <span data-ttu-id="617b2-242">Do pole **název projektu** název aplikace `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="617b2-242">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="617b2-243">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="617b2-243">Select **Create**.</span></span>

   <span data-ttu-id="617b2-244">7\.</span><span class="sxs-lookup"><span data-stu-id="617b2-244">7\.</span></span> <span data-ttu-id="617b2-245">Vyberte **spustit** > **Spustit bez ladění** pro spuštění aplikace *bez ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="617b2-245">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="617b2-246">Spusťte aplikaci s použitím **Spustit ladění** a spusťte aplikaci *pomocí ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="617b2-246">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="617b2-247">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="617b2-247">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="617b2-248">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="617b2-248">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="617b2-249">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="617b2-249">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="617b2-250">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="617b2-250">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="617b2-251">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="617b2-251">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="617b2-252">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="617b2-252">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="617b2-253">Domovská stránka produktu</span><span class="sxs-lookup"><span data-stu-id="617b2-253">Home</span></span>
* <span data-ttu-id="617b2-254">Counter</span><span class="sxs-lookup"><span data-stu-id="617b2-254">Counter</span></span>
* <span data-ttu-id="617b2-255">Načíst data</span><span class="sxs-lookup"><span data-stu-id="617b2-255">Fetch data</span></span>

<span data-ttu-id="617b2-256">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="617b2-256">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="617b2-257">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít C#.</span><span class="sxs-lookup"><span data-stu-id="617b2-257">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="617b2-258">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="617b2-258">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="617b2-259">Žádost o `/counter` v prohlížeči, jak je uvedeno v direktivě `@page` v horní části, způsobí, že komponenta `Counter` vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="617b2-259">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="617b2-260">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="617b2-260">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="617b2-261">Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :</span><span class="sxs-lookup"><span data-stu-id="617b2-261">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="617b2-262">Událost `onclick` je aktivována.</span><span class="sxs-lookup"><span data-stu-id="617b2-262">The `onclick` event is fired.</span></span>
* <span data-ttu-id="617b2-263">Je volána metoda `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="617b2-263">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="617b2-264">`currentCount` se zvyšuje.</span><span class="sxs-lookup"><span data-stu-id="617b2-264">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="617b2-265">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="617b2-265">The component is rendered again.</span></span>

<span data-ttu-id="617b2-266">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="617b2-266">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="617b2-267">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="617b2-267">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="617b2-268">Přidejte například komponentu `Counter` do domovské stránky aplikace přidáním prvku `<Counter />` do komponenty `Index`.</span><span class="sxs-lookup"><span data-stu-id="617b2-268">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="617b2-269">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="617b2-269">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="617b2-270">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="617b2-270">Run the app.</span></span> <span data-ttu-id="617b2-271">Domovská stránka má svůj vlastní čítač poskytovaný komponentou `Counter`.</span><span class="sxs-lookup"><span data-stu-id="617b2-271">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="617b2-272">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="617b2-272">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="617b2-273">Chcete-li přidat parametr do součásti `Counter`, aktualizujte blok `@code` součásti:</span><span class="sxs-lookup"><span data-stu-id="617b2-273">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="617b2-274">Přidejte veřejnou vlastnost pro `IncrementAmount` s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="617b2-274">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="617b2-275">Změňte metodu `IncrementCount` na použití `IncrementAmount` při zvyšování hodnoty `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="617b2-275">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="617b2-276">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="617b2-276">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="617b2-277">Určete `IncrementAmount` v prvku `<Counter>` komponenty `Index` pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="617b2-277">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="617b2-278">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="617b2-278">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="617b2-279">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="617b2-279">Run the app.</span></span> <span data-ttu-id="617b2-280">Komponenta `Index` má vlastní čítač, který při každém výběru tlačítka pro **kliknutí na tlačítko Další** zvyšuje o 10.</span><span class="sxs-lookup"><span data-stu-id="617b2-280">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="617b2-281">Komponenta `Counter` (*Counter. Razor*) se v `/counter` nadále zvětšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="617b2-281">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="617b2-282">Další kroky</span><span class="sxs-lookup"><span data-stu-id="617b2-282">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="617b2-283">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="617b2-283">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
