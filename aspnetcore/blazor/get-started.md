---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením Blazor aplikace pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: 9b4aee0be30568f098c756e9ab4cb5298e9a049b
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963000"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="b646c-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b646c-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="b646c-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b646c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b646c-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="b646c-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="b646c-106">Nainstalujte [sadu .NET Core 3,1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="b646c-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="b646c-107">Nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) spuštěním následujícího příkazu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b646c-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="b646c-108">[Microsoft. AspNetCore.Blazor. Balíček šablon](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) má verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="b646c-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview2.19528.8
   ```

1. <span data-ttu-id="b646c-109">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="b646c-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b646c-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b646c-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="b646c-111">1 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-111">1\.</span></span> <span data-ttu-id="b646c-112">Nainstalujte [Visual Studio 16,4 Preview 2 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="b646c-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="b646c-113">2 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-113">2\.</span></span> <span data-ttu-id="b646c-114">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="b646c-114">Create a new project.</span></span>

   <span data-ttu-id="b646c-115">3 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-115">3\.</span></span> <span data-ttu-id="b646c-116">Vyberte **Blazor aplikace**.</span><span class="sxs-lookup"><span data-stu-id="b646c-116">Select **Blazor App**.</span></span> <span data-ttu-id="b646c-117">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="b646c-117">Select **Next**.</span></span>

   <span data-ttu-id="b646c-118">4 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-118">4\.</span></span> <span data-ttu-id="b646c-119">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="b646c-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b646c-120">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="b646c-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="b646c-121">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="b646c-121">Select **Create**.</span></span>

   <span data-ttu-id="b646c-122">5 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-122">5\.</span></span> <span data-ttu-id="b646c-123">Pro Blazor prostředí WebAssembly vyberte šablonu **aplikaceBlazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="b646c-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b646c-124">Pro prostředí Blazor serveru vyberte šablonu **aplikaceBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="b646c-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="b646c-125">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="b646c-125">Select **Create**.</span></span> <span data-ttu-id="b646c-126">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b646c-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="b646c-127">6 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-127">6\.</span></span> <span data-ttu-id="b646c-128">Spusťte aplikaci stisknutím klávesy **Ctrl** +**F5** .</span><span class="sxs-lookup"><span data-stu-id="b646c-128">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b646c-129">Pokud jste nainstalovali rozšíření Blazor sady Visual Studio pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="b646c-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="b646c-130">Instalace šablon Blazor v příkazovém prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b646c-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b646c-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b646c-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="b646c-132">1 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-132">1\.</span></span> <span data-ttu-id="b646c-133">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="b646c-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="b646c-134">2 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-134">2\.</span></span> <span data-ttu-id="b646c-135">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="b646c-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="b646c-136">3 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-136">3\.</span></span> <span data-ttu-id="b646c-137">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b646c-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="b646c-138">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b646c-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="b646c-139">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b646c-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="b646c-140">4 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-140">4\.</span></span> <span data-ttu-id="b646c-141">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b646c-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="b646c-142">5 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-142">5\.</span></span> <span data-ttu-id="b646c-143">V případě projektu Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="b646c-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="b646c-144">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="b646c-144">Select **Yes**.</span></span>

   <span data-ttu-id="b646c-145">6 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-145">6\.</span></span> <span data-ttu-id="b646c-146">Pokud používáte aplikaci Blazor serveru, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b646c-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="b646c-147">Pokud používáte aplikaci Blazor WebAssembly, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b646c-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="b646c-148">7 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-148">7\.</span></span> <span data-ttu-id="b646c-149">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b646c-149">In a browser, navigate to `https://localhost:5001`.</span></span>

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

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b646c-150">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="b646c-150">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="b646c-151">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="b646c-151">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b646c-152">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="b646c-152">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b646c-153">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b646c-153">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="b646c-154">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b646c-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="b646c-155">Nainstalujte nejnovější verzi [sady .NET Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="b646c-155">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="b646c-156">Volitelně nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) , a to tak, že nainstalujete [sadu .NET Core 3,1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) a potom v příkazovém prostředí spustíte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b646c-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview2.19528.8
   ```

1. <span data-ttu-id="b646c-157">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="b646c-157">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b646c-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b646c-158">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="b646c-159">1 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-159">1\.</span></span> <span data-ttu-id="b646c-160">Nainstalujte si nejnovější verzi sady [Visual Studio](https://visualstudio.com/vs/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="b646c-160">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="b646c-161">2 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-161">2\.</span></span> <span data-ttu-id="b646c-162">Volitelně můžete nainstalovat [Visual Studio 16,4 Preview 2 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** pro Blazor vývoj aplikací pro WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b646c-162">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="b646c-163">3 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-163">3\.</span></span> <span data-ttu-id="b646c-164">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="b646c-164">Create a new project.</span></span>

   <span data-ttu-id="b646c-165">4 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-165">4\.</span></span> <span data-ttu-id="b646c-166">Vyberte **Blazor aplikace**.</span><span class="sxs-lookup"><span data-stu-id="b646c-166">Select **Blazor App**.</span></span> <span data-ttu-id="b646c-167">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="b646c-167">Select **Next**.</span></span>

   <span data-ttu-id="b646c-168">5 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-168">5\.</span></span> <span data-ttu-id="b646c-169">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="b646c-169">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b646c-170">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="b646c-170">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="b646c-171">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="b646c-171">Select **Create**.</span></span>

   <span data-ttu-id="b646c-172">6 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-172">6\.</span></span> <span data-ttu-id="b646c-173">Pro Blazor prostředí WebAssembly vyberte šablonu **aplikaceBlazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="b646c-173">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b646c-174">Pro prostředí Blazor serveru vyberte šablonu **aplikaceBlazor Server** .</span><span class="sxs-lookup"><span data-stu-id="b646c-174">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="b646c-175">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="b646c-175">Select **Create**.</span></span> <span data-ttu-id="b646c-176">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b646c-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="b646c-177">7 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-177">7\.</span></span> <span data-ttu-id="b646c-178">Stisknutím klávesy **F5** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b646c-178">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b646c-179">Pokud jste nainstalovali rozšíření Blazor sady Visual Studio pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="b646c-179">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="b646c-180">Instalace šablon Blazor v příkazovém prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b646c-180">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b646c-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b646c-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="b646c-182">1 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-182">1\.</span></span> <span data-ttu-id="b646c-183">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="b646c-183">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="b646c-184">2 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-184">2\.</span></span> <span data-ttu-id="b646c-185">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="b646c-185">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="b646c-186">3 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-186">3\.</span></span> <span data-ttu-id="b646c-187">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b646c-187">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="b646c-188">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b646c-188">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="b646c-189">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b646c-189">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="b646c-190">4 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-190">4\.</span></span> <span data-ttu-id="b646c-191">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b646c-191">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="b646c-192">5 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-192">5\.</span></span> <span data-ttu-id="b646c-193">V případě projektu Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="b646c-193">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="b646c-194">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="b646c-194">Select **Yes**.</span></span>

   <span data-ttu-id="b646c-195">6 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-195">6\.</span></span> <span data-ttu-id="b646c-196">Pokud používáte aplikaci Blazor serveru, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b646c-196">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="b646c-197">Pokud používáte aplikaci Blazor WebAssembly, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b646c-197">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="b646c-198">7 \.</span><span class="sxs-lookup"><span data-stu-id="b646c-198">7\.</span></span> <span data-ttu-id="b646c-199">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b646c-199">In a browser, navigate to `https://localhost:5001`.</span></span>

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

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b646c-200">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="b646c-200">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="b646c-201">Pro Blazor prostředí WebAssembly spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="b646c-201">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b646c-202">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="b646c-202">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b646c-203">Informace o těchto dvou Blazor modelech hostování, *Blazor serveru* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b646c-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="b646c-204">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b646c-204">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="b646c-205">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="b646c-205">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="b646c-206">Domů</span><span class="sxs-lookup"><span data-stu-id="b646c-206">Home</span></span>
* <span data-ttu-id="b646c-207">Čítač</span><span class="sxs-lookup"><span data-stu-id="b646c-207">Counter</span></span>
* <span data-ttu-id="b646c-208">Načíst data</span><span class="sxs-lookup"><span data-stu-id="b646c-208">Fetch data</span></span>

<span data-ttu-id="b646c-209">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="b646c-209">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="b646c-210">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít C#.</span><span class="sxs-lookup"><span data-stu-id="b646c-210">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="b646c-211">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b646c-211">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="b646c-212">Žádost o `/counter` v prohlížeči, jak je uvedeno v direktivě `@page` v horní části, způsobí, že komponenta `Counter` vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="b646c-212">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="b646c-213">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b646c-213">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="b646c-214">Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :</span><span class="sxs-lookup"><span data-stu-id="b646c-214">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="b646c-215">Událost `onclick` je aktivována.</span><span class="sxs-lookup"><span data-stu-id="b646c-215">The `onclick` event is fired.</span></span>
* <span data-ttu-id="b646c-216">Je volána metoda `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="b646c-216">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="b646c-217">`currentCount` se zvyšuje.</span><span class="sxs-lookup"><span data-stu-id="b646c-217">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="b646c-218">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="b646c-218">The component is rendered again.</span></span>

<span data-ttu-id="b646c-219">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="b646c-219">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="b646c-220">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="b646c-220">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="b646c-221">Přidejte například komponentu `Counter` do domovské stránky aplikace přidáním prvku `<Counter />` do komponenty `Index`.</span><span class="sxs-lookup"><span data-stu-id="b646c-221">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="b646c-222">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b646c-222">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="b646c-223">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b646c-223">Run the app.</span></span> <span data-ttu-id="b646c-224">Domovská stránka má svůj vlastní čítač poskytovaný komponentou `Counter`.</span><span class="sxs-lookup"><span data-stu-id="b646c-224">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="b646c-225">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="b646c-225">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="b646c-226">Chcete-li přidat parametr do součásti `Counter`, aktualizujte blok `@code` součásti:</span><span class="sxs-lookup"><span data-stu-id="b646c-226">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="b646c-227">Přidejte veřejnou vlastnost pro `IncrementAmount` s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="b646c-227">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="b646c-228">Změňte metodu `IncrementCount` na použití `IncrementAmount` při zvyšování hodnoty `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="b646c-228">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="b646c-229">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b646c-229">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="b646c-230">Určete `IncrementAmount` v prvku `<Counter>` komponenty `Index` pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="b646c-230">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="b646c-231">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b646c-231">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="b646c-232">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b646c-232">Run the app.</span></span> <span data-ttu-id="b646c-233">Komponenta `Index` má vlastní čítač, který při každém výběru tlačítka pro **kliknutí na tlačítko Další** zvyšuje o 10.</span><span class="sxs-lookup"><span data-stu-id="b646c-233">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="b646c-234">Komponenta `Counter` (*Counter. Razor*) se v `/counter` nadále zvětšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="b646c-234">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b646c-235">Další kroky</span><span class="sxs-lookup"><span data-stu-id="b646c-235">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="b646c-236">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b646c-236">Additional resources</span></span>

* <xref:signalr/introduction>
