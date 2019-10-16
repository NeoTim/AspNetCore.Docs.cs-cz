---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením aplikace Blazor pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/get-started
ms.openlocfilehash: fc368be5eb2e5d8f7c80071dc86a02ae986a685f
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391057"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="81435-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="81435-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="81435-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="81435-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="81435-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="81435-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="81435-106">Nainstalujte [sadu .NET Core 3,1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="81435-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="81435-107">Nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) spuštěním následujícího příkazu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="81435-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="81435-108">Balíček [Microsoft. AspNetCore. Blazor. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) obsahuje verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="81435-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview1.19508.20
   ```

1. <span data-ttu-id="81435-109">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="81435-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="81435-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81435-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="81435-111">1 \.</span><span class="sxs-lookup"><span data-stu-id="81435-111">1\.</span></span> <span data-ttu-id="81435-112">Nainstalujte [Visual Studio 16,4 Preview 2 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="81435-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="81435-113">2 \.</span><span class="sxs-lookup"><span data-stu-id="81435-113">2\.</span></span> <span data-ttu-id="81435-114">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="81435-114">Create a new project.</span></span>

   <span data-ttu-id="81435-115">3 \.</span><span class="sxs-lookup"><span data-stu-id="81435-115">3\.</span></span> <span data-ttu-id="81435-116">Vyberte **aplikaci Blazor**.</span><span class="sxs-lookup"><span data-stu-id="81435-116">Select **Blazor App**.</span></span> <span data-ttu-id="81435-117">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="81435-117">Select **Next**.</span></span>

   <span data-ttu-id="81435-118">4 \.</span><span class="sxs-lookup"><span data-stu-id="81435-118">4\.</span></span> <span data-ttu-id="81435-119">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="81435-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="81435-120">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="81435-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="81435-121">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="81435-121">Select **Create**.</span></span>

   <span data-ttu-id="81435-122">5 \.</span><span class="sxs-lookup"><span data-stu-id="81435-122">5\.</span></span> <span data-ttu-id="81435-123">Pro prostředí WebAssembly Blazor vyberte šablonu **aplikace Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="81435-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="81435-124">Pro prostředí Blazor serveru vyberte šablonu **aplikace serveru Blazor** .</span><span class="sxs-lookup"><span data-stu-id="81435-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="81435-125">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="81435-125">Select **Create**.</span></span> <span data-ttu-id="81435-126">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="81435-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="81435-127">6 \.</span><span class="sxs-lookup"><span data-stu-id="81435-127">6\.</span></span> <span data-ttu-id="81435-128">Stisknutím klávesy **F5** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="81435-128">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="81435-129">Pokud jste nainstalovali rozšíření sady Visual Studio Blazor pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="81435-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="81435-130">Instalace šablon Blazor do příkazového prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="81435-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="81435-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81435-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="81435-132">1 \.</span><span class="sxs-lookup"><span data-stu-id="81435-132">1\.</span></span> <span data-ttu-id="81435-133">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="81435-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="81435-134">2 \.</span><span class="sxs-lookup"><span data-stu-id="81435-134">2\.</span></span> <span data-ttu-id="81435-135">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="81435-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="81435-136">3 \.</span><span class="sxs-lookup"><span data-stu-id="81435-136">3\.</span></span> <span data-ttu-id="81435-137">Pro prostředí WebAssembly Blazor spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="81435-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="81435-138">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="81435-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="81435-139">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="81435-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="81435-140">4 \.</span><span class="sxs-lookup"><span data-stu-id="81435-140">4\.</span></span> <span data-ttu-id="81435-141">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="81435-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="81435-142">5 \.</span><span class="sxs-lookup"><span data-stu-id="81435-142">5\.</span></span> <span data-ttu-id="81435-143">Pro projekt Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="81435-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="81435-144">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="81435-144">Select **Yes**.</span></span>

   <span data-ttu-id="81435-145">6 \.</span><span class="sxs-lookup"><span data-stu-id="81435-145">6\.</span></span> <span data-ttu-id="81435-146">Pokud používáte aplikaci serveru Blazor, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="81435-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="81435-147">Pokud používáte aplikaci WebAssembly Blazor, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="81435-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="81435-148">7 \.</span><span class="sxs-lookup"><span data-stu-id="81435-148">7\.</span></span> <span data-ttu-id="81435-149">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="81435-149">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="81435-150">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="81435-150">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="81435-151">Pro prostředí WebAssembly Blazor spusťte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="81435-151">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="81435-152">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="81435-152">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="81435-153">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="81435-153">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="81435-154">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="81435-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="81435-155">Nainstalujte nejnovější verzi [sady .NET Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="81435-155">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="81435-156">Volitelně nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) tak, že nainstalujete [sadu .NET Core 3,1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) a potom v příkazovém prostředí spustíte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="81435-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview1.19508.20
   ```

1. <span data-ttu-id="81435-157">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="81435-157">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="81435-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81435-158">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="81435-159">1 \.</span><span class="sxs-lookup"><span data-stu-id="81435-159">1\.</span></span> <span data-ttu-id="81435-160">Nainstalujte si nejnovější verzi sady [Visual Studio](https://visualstudio.com/vs/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="81435-160">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="81435-161">2 \.</span><span class="sxs-lookup"><span data-stu-id="81435-161">2\.</span></span> <span data-ttu-id="81435-162">Volitelně můžete nainstalovat [Visual Studio 16,4 Preview 2 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** pro vývoj aplikací pro Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="81435-162">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="81435-163">3 \.</span><span class="sxs-lookup"><span data-stu-id="81435-163">3\.</span></span> <span data-ttu-id="81435-164">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="81435-164">Create a new project.</span></span>

   <span data-ttu-id="81435-165">4 \.</span><span class="sxs-lookup"><span data-stu-id="81435-165">4\.</span></span> <span data-ttu-id="81435-166">Vyberte **aplikaci Blazor**.</span><span class="sxs-lookup"><span data-stu-id="81435-166">Select **Blazor App**.</span></span> <span data-ttu-id="81435-167">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="81435-167">Select **Next**.</span></span>

   <span data-ttu-id="81435-168">5 \.</span><span class="sxs-lookup"><span data-stu-id="81435-168">5\.</span></span> <span data-ttu-id="81435-169">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="81435-169">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="81435-170">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="81435-170">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="81435-171">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="81435-171">Select **Create**.</span></span>

   <span data-ttu-id="81435-172">6 \.</span><span class="sxs-lookup"><span data-stu-id="81435-172">6\.</span></span> <span data-ttu-id="81435-173">Pro prostředí WebAssembly Blazor vyberte šablonu **aplikace Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="81435-173">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="81435-174">Pro prostředí Blazor serveru vyberte šablonu **aplikace serveru Blazor** .</span><span class="sxs-lookup"><span data-stu-id="81435-174">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="81435-175">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="81435-175">Select **Create**.</span></span> <span data-ttu-id="81435-176">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="81435-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="81435-177">7 \.</span><span class="sxs-lookup"><span data-stu-id="81435-177">7\.</span></span> <span data-ttu-id="81435-178">Stisknutím klávesy **F5** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="81435-178">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="81435-179">Pokud jste nainstalovali rozšíření sady Visual Studio Blazor pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="81435-179">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="81435-180">Instalace šablon Blazor do příkazového prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="81435-180">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="81435-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81435-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="81435-182">1 \.</span><span class="sxs-lookup"><span data-stu-id="81435-182">1\.</span></span> <span data-ttu-id="81435-183">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="81435-183">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="81435-184">2 \.</span><span class="sxs-lookup"><span data-stu-id="81435-184">2\.</span></span> <span data-ttu-id="81435-185">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="81435-185">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="81435-186">3 \.</span><span class="sxs-lookup"><span data-stu-id="81435-186">3\.</span></span> <span data-ttu-id="81435-187">Pro prostředí WebAssembly Blazor spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="81435-187">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="81435-188">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="81435-188">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="81435-189">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="81435-189">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="81435-190">4 \.</span><span class="sxs-lookup"><span data-stu-id="81435-190">4\.</span></span> <span data-ttu-id="81435-191">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="81435-191">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="81435-192">5 \.</span><span class="sxs-lookup"><span data-stu-id="81435-192">5\.</span></span> <span data-ttu-id="81435-193">Pro projekt Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="81435-193">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="81435-194">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="81435-194">Select **Yes**.</span></span>

   <span data-ttu-id="81435-195">6 \.</span><span class="sxs-lookup"><span data-stu-id="81435-195">6\.</span></span> <span data-ttu-id="81435-196">Pokud používáte aplikaci serveru Blazor, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="81435-196">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="81435-197">Pokud používáte aplikaci WebAssembly Blazor, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="81435-197">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="81435-198">7 \.</span><span class="sxs-lookup"><span data-stu-id="81435-198">7\.</span></span> <span data-ttu-id="81435-199">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="81435-199">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="81435-200">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="81435-200">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="81435-201">Pro prostředí WebAssembly Blazor spusťte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="81435-201">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="81435-202">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="81435-202">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="81435-203">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="81435-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="81435-204">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="81435-204">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="81435-205">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="81435-205">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="81435-206">Domů</span><span class="sxs-lookup"><span data-stu-id="81435-206">Home</span></span>
* <span data-ttu-id="81435-207">Čítač</span><span class="sxs-lookup"><span data-stu-id="81435-207">Counter</span></span>
* <span data-ttu-id="81435-208">Načíst data</span><span class="sxs-lookup"><span data-stu-id="81435-208">Fetch data</span></span>

<span data-ttu-id="81435-209">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="81435-209">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="81435-210">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít C#.</span><span class="sxs-lookup"><span data-stu-id="81435-210">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="81435-211">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="81435-211">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="81435-212">Požadavek na `/counter` v prohlížeči, jak je uvedeno v direktivě `@page` v horní části, způsobí, že komponenta `Counter` vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="81435-212">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="81435-213">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="81435-213">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="81435-214">Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :</span><span class="sxs-lookup"><span data-stu-id="81435-214">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="81435-215">Událost `onclick` je aktivována.</span><span class="sxs-lookup"><span data-stu-id="81435-215">The `onclick` event is fired.</span></span>
* <span data-ttu-id="81435-216">Je volána metoda `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="81435-216">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="81435-217">Hodnota `currentCount` se zvýší.</span><span class="sxs-lookup"><span data-stu-id="81435-217">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="81435-218">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="81435-218">The component is rendered again.</span></span>

<span data-ttu-id="81435-219">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="81435-219">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="81435-220">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="81435-220">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="81435-221">Můžete například přidat komponentu `Counter` do domovské stránky aplikace přidáním prvku `<Counter />` do komponenty `Index`.</span><span class="sxs-lookup"><span data-stu-id="81435-221">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="81435-222">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="81435-222">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="81435-223">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="81435-223">Run the app.</span></span> <span data-ttu-id="81435-224">Domovská stránka má vlastní čítač poskytovaný komponentou `Counter`.</span><span class="sxs-lookup"><span data-stu-id="81435-224">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="81435-225">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="81435-225">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="81435-226">Chcete-li přidat parametr do součásti `Counter`, aktualizujte blok `@code` komponenty:</span><span class="sxs-lookup"><span data-stu-id="81435-226">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="81435-227">Přidejte veřejnou vlastnost pro `IncrementAmount` s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="81435-227">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="81435-228">Změňte metodu `IncrementCount` tak, aby při zvyšování hodnoty `currentCount` používala `IncrementAmount`.</span><span class="sxs-lookup"><span data-stu-id="81435-228">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="81435-229">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="81435-229">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="81435-230">Zadejte `IncrementAmount` v prvku `<Counter>` komponenty `Index` pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="81435-230">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="81435-231">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="81435-231">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="81435-232">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="81435-232">Run the app.</span></span> <span data-ttu-id="81435-233">Komponenta `Index` má vlastní čítač, který se při každém výběru tlačítka pro **kliknutí na tlačítko Další** zvyšuje o 10.</span><span class="sxs-lookup"><span data-stu-id="81435-233">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="81435-234">Součást `Counter` (*čítač. Razor*) v `/counter` bude nadále zvyšovat o jednu.</span><span class="sxs-lookup"><span data-stu-id="81435-234">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="81435-235">Další kroky</span><span class="sxs-lookup"><span data-stu-id="81435-235">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="81435-236">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="81435-236">Additional resources</span></span>

* <xref:signalr/introduction>
