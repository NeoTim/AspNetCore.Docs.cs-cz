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
ms.openlocfilehash: 554f4daff92a0839ee7679287a4618e9b51e0fe5
ms.sourcegitcommit: 925cdbd94613243f33bc7613a62ea34006219931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921290"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="3de56-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="3de56-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="3de56-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3de56-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="3de56-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="3de56-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="3de56-106">Nainstalujte [sadu .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="3de56-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="3de56-107">Volitelně nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="3de56-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="3de56-108">Nainstalujte [sadu SDK .NET Core 3,1 nebo novější (Preview)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="3de56-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="3de56-109">Spusťte následující příkaz v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="3de56-109">Run the following command in a command shell.</span></span> <span data-ttu-id="3de56-110">[Microsoft.AspNetCore.Blazor.Balíček šablon](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) má verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="3de56-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="3de56-111">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="3de56-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3de56-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3de56-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="3de56-113">1\.</span><span class="sxs-lookup"><span data-stu-id="3de56-113">1\.</span></span> <span data-ttu-id="3de56-114">Nainstalujte [Visual Studio 16,4 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="3de56-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="3de56-115">2\.</span><span class="sxs-lookup"><span data-stu-id="3de56-115">2\.</span></span> <span data-ttu-id="3de56-116">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="3de56-116">Create a new project.</span></span>

   <span data-ttu-id="3de56-117">3\.</span><span class="sxs-lookup"><span data-stu-id="3de56-117">3\.</span></span> <span data-ttu-id="3de56-118">Vyberte **Blazor aplikace**.</span><span class="sxs-lookup"><span data-stu-id="3de56-118">Select **Blazor App**.</span></span> <span data-ttu-id="3de56-119">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3de56-119">Select **Next**.</span></span>

   <span data-ttu-id="3de56-120">4\.</span><span class="sxs-lookup"><span data-stu-id="3de56-120">4\.</span></span> <span data-ttu-id="3de56-121">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="3de56-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3de56-122">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="3de56-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3de56-123">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3de56-123">Select **Create**.</span></span>

   <span data-ttu-id="3de56-124">5\.</span><span class="sxs-lookup"><span data-stu-id="3de56-124">5\.</span></span> <span data-ttu-id="3de56-125">Pro Blazor prostředí WebAssembly vyberte šablonu **aplikaceBlazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="3de56-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3de56-126">Pro prostředí Blazor serveru vyberte šablonu **aplikaceBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="3de56-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3de56-127">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3de56-127">Select **Create**.</span></span> <span data-ttu-id="3de56-128">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3de56-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3de56-129">6\.</span><span class="sxs-lookup"><span data-stu-id="3de56-129">6\.</span></span> <span data-ttu-id="3de56-130">Spusťte aplikaci stisknutím klávesy **Ctrl**+**F5** .</span><span class="sxs-lookup"><span data-stu-id="3de56-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="3de56-131">Pokud jste nainstalovali rozšíření Blazor sady Visual Studio pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="3de56-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="3de56-132">Instalace šablon Blazor v příkazovém prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3de56-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3de56-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3de56-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="3de56-134">1\.</span><span class="sxs-lookup"><span data-stu-id="3de56-134">1\.</span></span> <span data-ttu-id="3de56-135">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="3de56-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="3de56-136">2\.</span><span class="sxs-lookup"><span data-stu-id="3de56-136">2\.</span></span> <span data-ttu-id="3de56-137">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="3de56-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="3de56-138">3\.</span><span class="sxs-lookup"><span data-stu-id="3de56-138">3\.</span></span> <span data-ttu-id="3de56-139">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3de56-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="3de56-140">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3de56-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="3de56-141">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3de56-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3de56-142">4\.</span><span class="sxs-lookup"><span data-stu-id="3de56-142">4\.</span></span> <span data-ttu-id="3de56-143">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3de56-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="3de56-144">5\.</span><span class="sxs-lookup"><span data-stu-id="3de56-144">5\.</span></span> <span data-ttu-id="3de56-145">V případě projektu Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="3de56-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="3de56-146">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="3de56-146">Select **Yes**.</span></span>

   <span data-ttu-id="3de56-147">6\.</span><span class="sxs-lookup"><span data-stu-id="3de56-147">6\.</span></span> <span data-ttu-id="3de56-148">Pokud používáte aplikaci Blazor serveru, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3de56-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="3de56-149">Pokud používáte aplikaci Blazor WebAssembly, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="3de56-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="3de56-150">7\.</span><span class="sxs-lookup"><span data-stu-id="3de56-150">7\.</span></span> <span data-ttu-id="3de56-151">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3de56-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3de56-152">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3de56-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="3de56-153">1\.</span><span class="sxs-lookup"><span data-stu-id="3de56-153">1\.</span></span> <span data-ttu-id="3de56-154">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="3de56-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="3de56-155">2\.</span><span class="sxs-lookup"><span data-stu-id="3de56-155">2\.</span></span> <span data-ttu-id="3de56-156">Vyberte **soubor** > **nové řešení** nebo vytvořte **Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="3de56-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="3de56-157">3\.</span><span class="sxs-lookup"><span data-stu-id="3de56-157">3\.</span></span> <span data-ttu-id="3de56-158">Na bočním panelu vyberte **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="3de56-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="3de56-159">4\.</span><span class="sxs-lookup"><span data-stu-id="3de56-159">4\.</span></span> <span data-ttu-id="3de56-160">Vyberte šablonu **aplikaceBlazorového serveru** .</span><span class="sxs-lookup"><span data-stu-id="3de56-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="3de56-161">V tuto chvíli je v Visual Studio pro Mac dostupná jenom šablona serveru Blazor.</span><span class="sxs-lookup"><span data-stu-id="3de56-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="3de56-162">Pro Blazor prostředí WebAssembly postupujte podle pokynů na kartě **.NET Core CLI** . Po výběru šablony Blazor serveru vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3de56-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="3de56-163">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3de56-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="3de56-164">5\.</span><span class="sxs-lookup"><span data-stu-id="3de56-164">5\.</span></span> <span data-ttu-id="3de56-165">Nastavte **cílovou architekturu** na **.NET Core 3,1** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3de56-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="3de56-166">6\.</span><span class="sxs-lookup"><span data-stu-id="3de56-166">6\.</span></span> <span data-ttu-id="3de56-167">Do pole **název projektu** název aplikace `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="3de56-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="3de56-168">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3de56-168">Select **Create**.</span></span>

   <span data-ttu-id="3de56-169">7\.</span><span class="sxs-lookup"><span data-stu-id="3de56-169">7\.</span></span> <span data-ttu-id="3de56-170">Vyberte **spustit** > **Spustit bez ladění** pro spuštění aplikace *bez ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="3de56-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="3de56-171">Spusťte aplikaci s použitím **Spustit ladění** a spusťte aplikaci *pomocí ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="3de56-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3de56-172">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3de56-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="3de56-173">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3de56-173">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="3de56-174">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3de56-174">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="3de56-175">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3de56-175">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3de56-176">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3de56-176">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="3de56-177">Nainstalujte si nejnovější verzi [sady .NET Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span><span class="sxs-lookup"><span data-stu-id="3de56-177">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="3de56-178">Volitelně nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="3de56-178">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="3de56-179">Nainstalujte [sadu SDK .NET Core 3,1 nebo novější (Preview)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="3de56-179">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="3de56-180">Spusťte následující příkaz v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="3de56-180">Run the following command in a command shell.</span></span> <span data-ttu-id="3de56-181">[Microsoft.AspNetCore.Blazor.Balíček šablon](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) má verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="3de56-181">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="3de56-182">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="3de56-182">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3de56-183">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3de56-183">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="3de56-184">1\.</span><span class="sxs-lookup"><span data-stu-id="3de56-184">1\.</span></span> <span data-ttu-id="3de56-185">Nainstalujte si nejnovější verzi sady [Visual Studio](https://visualstudio.com/vs/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="3de56-185">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="3de56-186">2\.</span><span class="sxs-lookup"><span data-stu-id="3de56-186">2\.</span></span> <span data-ttu-id="3de56-187">Volitelně můžete nainstalovat [Visual Studio 16,4 Preview 2 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** pro Blazor vývoj aplikací pro WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3de56-187">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="3de56-188">3\.</span><span class="sxs-lookup"><span data-stu-id="3de56-188">3\.</span></span> <span data-ttu-id="3de56-189">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="3de56-189">Create a new project.</span></span>

   <span data-ttu-id="3de56-190">4\.</span><span class="sxs-lookup"><span data-stu-id="3de56-190">4\.</span></span> <span data-ttu-id="3de56-191">Vyberte **Blazor aplikace**.</span><span class="sxs-lookup"><span data-stu-id="3de56-191">Select **Blazor App**.</span></span> <span data-ttu-id="3de56-192">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3de56-192">Select **Next**.</span></span>

   <span data-ttu-id="3de56-193">5\.</span><span class="sxs-lookup"><span data-stu-id="3de56-193">5\.</span></span> <span data-ttu-id="3de56-194">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="3de56-194">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3de56-195">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="3de56-195">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3de56-196">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3de56-196">Select **Create**.</span></span>

   <span data-ttu-id="3de56-197">6\.</span><span class="sxs-lookup"><span data-stu-id="3de56-197">6\.</span></span> <span data-ttu-id="3de56-198">Pro Blazor prostředí WebAssembly vyberte šablonu **aplikaceBlazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="3de56-198">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3de56-199">Pro prostředí Blazor serveru vyberte šablonu **aplikaceBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="3de56-199">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3de56-200">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3de56-200">Select **Create**.</span></span> <span data-ttu-id="3de56-201">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3de56-201">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3de56-202">7\.</span><span class="sxs-lookup"><span data-stu-id="3de56-202">7\.</span></span> <span data-ttu-id="3de56-203">Stisknutím klávesy **F5** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3de56-203">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="3de56-204">Pokud jste nainstalovali rozšíření Blazor sady Visual Studio pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="3de56-204">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="3de56-205">Instalace šablon Blazor v příkazovém prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3de56-205">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3de56-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3de56-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="3de56-207">1\.</span><span class="sxs-lookup"><span data-stu-id="3de56-207">1\.</span></span> <span data-ttu-id="3de56-208">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="3de56-208">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="3de56-209">2\.</span><span class="sxs-lookup"><span data-stu-id="3de56-209">2\.</span></span> <span data-ttu-id="3de56-210">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="3de56-210">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="3de56-211">3\.</span><span class="sxs-lookup"><span data-stu-id="3de56-211">3\.</span></span> <span data-ttu-id="3de56-212">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3de56-212">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="3de56-213">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3de56-213">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="3de56-214">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3de56-214">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3de56-215">4\.</span><span class="sxs-lookup"><span data-stu-id="3de56-215">4\.</span></span> <span data-ttu-id="3de56-216">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3de56-216">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="3de56-217">5\.</span><span class="sxs-lookup"><span data-stu-id="3de56-217">5\.</span></span> <span data-ttu-id="3de56-218">V případě projektu Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="3de56-218">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="3de56-219">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="3de56-219">Select **Yes**.</span></span>

   <span data-ttu-id="3de56-220">6\.</span><span class="sxs-lookup"><span data-stu-id="3de56-220">6\.</span></span> <span data-ttu-id="3de56-221">Pokud používáte aplikaci Blazor serveru, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3de56-221">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="3de56-222">Pokud používáte aplikaci Blazor WebAssembly, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="3de56-222">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="3de56-223">7\.</span><span class="sxs-lookup"><span data-stu-id="3de56-223">7\.</span></span> <span data-ttu-id="3de56-224">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3de56-224">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3de56-225">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3de56-225">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="3de56-226">1\.</span><span class="sxs-lookup"><span data-stu-id="3de56-226">1\.</span></span> <span data-ttu-id="3de56-227">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="3de56-227">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="3de56-228">Přepněte [kanál aktualizace na verzi Preview](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="3de56-228">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="3de56-229">2\.</span><span class="sxs-lookup"><span data-stu-id="3de56-229">2\.</span></span> <span data-ttu-id="3de56-230">Vyberte **soubor** > **nové řešení** nebo vytvořte **Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="3de56-230">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="3de56-231">3\.</span><span class="sxs-lookup"><span data-stu-id="3de56-231">3\.</span></span> <span data-ttu-id="3de56-232">Na bočním panelu vyberte **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="3de56-232">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="3de56-233">4\.</span><span class="sxs-lookup"><span data-stu-id="3de56-233">4\.</span></span> <span data-ttu-id="3de56-234">Vyberte šablonu **aplikaceBlazorového serveru** .</span><span class="sxs-lookup"><span data-stu-id="3de56-234">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="3de56-235">V tuto chvíli je v Visual Studio pro Mac dostupná jenom šablona serveru Blazor.</span><span class="sxs-lookup"><span data-stu-id="3de56-235">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="3de56-236">Pro Blazor prostředí WebAssembly postupujte podle pokynů na kartě **.NET Core CLI** . Po výběru šablony Blazor serveru vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3de56-236">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="3de56-237">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3de56-237">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="3de56-238">5\.</span><span class="sxs-lookup"><span data-stu-id="3de56-238">5\.</span></span> <span data-ttu-id="3de56-239">Nastavte **cílovou architekturu** na **.NET Core 3,0** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3de56-239">Set the **Target Framework** to **.NET Core 3.0** and select **Next**.</span></span>

   <span data-ttu-id="3de56-240">6\.</span><span class="sxs-lookup"><span data-stu-id="3de56-240">6\.</span></span> <span data-ttu-id="3de56-241">Do pole **název projektu** název aplikace `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="3de56-241">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="3de56-242">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3de56-242">Select **Create**.</span></span>

   <span data-ttu-id="3de56-243">7\.</span><span class="sxs-lookup"><span data-stu-id="3de56-243">7\.</span></span> <span data-ttu-id="3de56-244">Vyberte **spustit** > **Spustit bez ladění** pro spuštění aplikace *bez ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="3de56-244">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="3de56-245">Spusťte aplikaci s použitím **Spustit ladění** a spusťte aplikaci *pomocí ladicího programu*.</span><span class="sxs-lookup"><span data-stu-id="3de56-245">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3de56-246">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3de56-246">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="3de56-247">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3de56-247">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="3de56-248">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3de56-248">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="3de56-249">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="3de56-249">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="3de56-250">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3de56-250">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="3de56-251">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="3de56-251">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="3de56-252">Domovská stránka produktu</span><span class="sxs-lookup"><span data-stu-id="3de56-252">Home</span></span>
* <span data-ttu-id="3de56-253">Counter</span><span class="sxs-lookup"><span data-stu-id="3de56-253">Counter</span></span>
* <span data-ttu-id="3de56-254">Načíst data</span><span class="sxs-lookup"><span data-stu-id="3de56-254">Fetch data</span></span>

<span data-ttu-id="3de56-255">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="3de56-255">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="3de56-256">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít C#.</span><span class="sxs-lookup"><span data-stu-id="3de56-256">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="3de56-257">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="3de56-257">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="3de56-258">Žádost o `/counter` v prohlížeči, jak je uvedeno v direktivě `@page` v horní části, způsobí, že komponenta `Counter` vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="3de56-258">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="3de56-259">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3de56-259">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="3de56-260">Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :</span><span class="sxs-lookup"><span data-stu-id="3de56-260">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="3de56-261">Událost `onclick` je aktivována.</span><span class="sxs-lookup"><span data-stu-id="3de56-261">The `onclick` event is fired.</span></span>
* <span data-ttu-id="3de56-262">Je volána metoda `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="3de56-262">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="3de56-263">`currentCount` se zvyšuje.</span><span class="sxs-lookup"><span data-stu-id="3de56-263">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="3de56-264">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="3de56-264">The component is rendered again.</span></span>

<span data-ttu-id="3de56-265">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="3de56-265">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="3de56-266">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="3de56-266">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="3de56-267">Přidejte například komponentu `Counter` do domovské stránky aplikace přidáním prvku `<Counter />` do komponenty `Index`.</span><span class="sxs-lookup"><span data-stu-id="3de56-267">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="3de56-268">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="3de56-268">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="3de56-269">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3de56-269">Run the app.</span></span> <span data-ttu-id="3de56-270">Domovská stránka má svůj vlastní čítač poskytovaný komponentou `Counter`.</span><span class="sxs-lookup"><span data-stu-id="3de56-270">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="3de56-271">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="3de56-271">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="3de56-272">Chcete-li přidat parametr do součásti `Counter`, aktualizujte blok `@code` součásti:</span><span class="sxs-lookup"><span data-stu-id="3de56-272">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="3de56-273">Přidejte veřejnou vlastnost pro `IncrementAmount` s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="3de56-273">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="3de56-274">Změňte metodu `IncrementCount` na použití `IncrementAmount` při zvyšování hodnoty `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="3de56-274">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="3de56-275">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="3de56-275">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="3de56-276">Určete `IncrementAmount` v prvku `<Counter>` komponenty `Index` pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="3de56-276">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="3de56-277">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="3de56-277">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="3de56-278">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3de56-278">Run the app.</span></span> <span data-ttu-id="3de56-279">Komponenta `Index` má vlastní čítač, který při každém výběru tlačítka pro **kliknutí na tlačítko Další** zvyšuje o 10.</span><span class="sxs-lookup"><span data-stu-id="3de56-279">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="3de56-280">Komponenta `Counter` (*Counter. Razor*) se v `/counter` nadále zvětšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="3de56-280">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3de56-281">Další kroky</span><span class="sxs-lookup"><span data-stu-id="3de56-281">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="3de56-282">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="3de56-282">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
