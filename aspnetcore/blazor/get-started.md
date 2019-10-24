---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením aplikace Blazor pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/21/2019
uid: blazor/get-started
ms.openlocfilehash: 80ff7b42a44e722dd27bc4fde53a066863448e10
ms.sourcegitcommit: 810d5831169770ee240d03207d6671dabea2486e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72779127"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="fae46-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="fae46-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="fae46-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fae46-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="fae46-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="fae46-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="fae46-106">Nainstalujte [sadu .NET Core 3,1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="fae46-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="fae46-107">Nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) spuštěním následujícího příkazu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="fae46-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="fae46-108">Balíček [Microsoft. AspNetCore. Blazor. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) obsahuje verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="fae46-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview1.19508.20
   ```

1. <span data-ttu-id="fae46-109">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="fae46-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fae46-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fae46-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="fae46-111">1 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-111">1\.</span></span> <span data-ttu-id="fae46-112">Nainstalujte [Visual Studio 16,4 Preview 2 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="fae46-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="fae46-113">2 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-113">2\.</span></span> <span data-ttu-id="fae46-114">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="fae46-114">Create a new project.</span></span>

   <span data-ttu-id="fae46-115">3 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-115">3\.</span></span> <span data-ttu-id="fae46-116">Vyberte **aplikaci Blazor**.</span><span class="sxs-lookup"><span data-stu-id="fae46-116">Select **Blazor App**.</span></span> <span data-ttu-id="fae46-117">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="fae46-117">Select **Next**.</span></span>

   <span data-ttu-id="fae46-118">4 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-118">4\.</span></span> <span data-ttu-id="fae46-119">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="fae46-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="fae46-120">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="fae46-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="fae46-121">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fae46-121">Select **Create**.</span></span>

   <span data-ttu-id="fae46-122">5 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-122">5\.</span></span> <span data-ttu-id="fae46-123">Pro prostředí WebAssembly Blazor vyberte šablonu **aplikace Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="fae46-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="fae46-124">Pro prostředí Blazor serveru vyberte šablonu **aplikace serveru Blazor** .</span><span class="sxs-lookup"><span data-stu-id="fae46-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="fae46-125">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fae46-125">Select **Create**.</span></span> <span data-ttu-id="fae46-126">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="fae46-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="fae46-127">6 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-127">6\.</span></span> <span data-ttu-id="fae46-128">Spusťte aplikaci stisknutím klávesy **Ctrl** +**F5** .</span><span class="sxs-lookup"><span data-stu-id="fae46-128">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="fae46-129">Pokud jste nainstalovali rozšíření sady Visual Studio Blazor pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="fae46-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="fae46-130">Instalace šablon Blazor do příkazového prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fae46-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="fae46-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fae46-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="fae46-132">1 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-132">1\.</span></span> <span data-ttu-id="fae46-133">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="fae46-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="fae46-134">2 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-134">2\.</span></span> <span data-ttu-id="fae46-135">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="fae46-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="fae46-136">3 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-136">3\.</span></span> <span data-ttu-id="fae46-137">Pro prostředí WebAssembly Blazor spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="fae46-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="fae46-138">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fae46-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="fae46-139">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="fae46-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="fae46-140">4 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-140">4\.</span></span> <span data-ttu-id="fae46-141">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fae46-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="fae46-142">5 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-142">5\.</span></span> <span data-ttu-id="fae46-143">Pro projekt Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="fae46-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="fae46-144">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="fae46-144">Select **Yes**.</span></span>

   <span data-ttu-id="fae46-145">6 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-145">6\.</span></span> <span data-ttu-id="fae46-146">Pokud používáte aplikaci serveru Blazor, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fae46-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="fae46-147">Pokud používáte aplikaci WebAssembly Blazor, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="fae46-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="fae46-148">7 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-148">7\.</span></span> <span data-ttu-id="fae46-149">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fae46-149">In a browser, navigate to `https://localhost:5001`.</span></span>

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

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="fae46-150">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="fae46-150">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="fae46-151">Pro prostředí WebAssembly Blazor spusťte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="fae46-151">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="fae46-152">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="fae46-152">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="fae46-153">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="fae46-153">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="fae46-154">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fae46-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="fae46-155">Nainstalujte nejnovější verzi [sady .NET Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="fae46-155">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="fae46-156">Volitelně nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) tak, že nainstalujete [sadu .NET Core 3,1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) a potom v příkazovém prostředí spustíte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fae46-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview1.19508.20
   ```

1. <span data-ttu-id="fae46-157">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="fae46-157">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fae46-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fae46-158">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="fae46-159">1 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-159">1\.</span></span> <span data-ttu-id="fae46-160">Nainstalujte si nejnovější verzi sady [Visual Studio](https://visualstudio.com/vs/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="fae46-160">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="fae46-161">2 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-161">2\.</span></span> <span data-ttu-id="fae46-162">Volitelně můžete nainstalovat [Visual Studio 16,4 Preview 2 nebo novější](https://visualstudio.microsoft.com/vs/preview/) s úlohou **vývoje ASP.NET a webu** pro vývoj aplikací pro Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="fae46-162">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="fae46-163">3 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-163">3\.</span></span> <span data-ttu-id="fae46-164">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="fae46-164">Create a new project.</span></span>

   <span data-ttu-id="fae46-165">4 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-165">4\.</span></span> <span data-ttu-id="fae46-166">Vyberte **aplikaci Blazor**.</span><span class="sxs-lookup"><span data-stu-id="fae46-166">Select **Blazor App**.</span></span> <span data-ttu-id="fae46-167">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="fae46-167">Select **Next**.</span></span>

   <span data-ttu-id="fae46-168">5 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-168">5\.</span></span> <span data-ttu-id="fae46-169">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="fae46-169">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="fae46-170">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="fae46-170">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="fae46-171">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fae46-171">Select **Create**.</span></span>

   <span data-ttu-id="fae46-172">6 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-172">6\.</span></span> <span data-ttu-id="fae46-173">Pro prostředí WebAssembly Blazor vyberte šablonu **aplikace Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="fae46-173">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="fae46-174">Pro prostředí Blazor serveru vyberte šablonu **aplikace serveru Blazor** .</span><span class="sxs-lookup"><span data-stu-id="fae46-174">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="fae46-175">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fae46-175">Select **Create**.</span></span> <span data-ttu-id="fae46-176">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="fae46-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="fae46-177">7 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-177">7\.</span></span> <span data-ttu-id="fae46-178">Stisknutím klávesy **F5** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fae46-178">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="fae46-179">Pokud jste nainstalovali rozšíření sady Visual Studio Blazor pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="fae46-179">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="fae46-180">Instalace šablon Blazor do příkazového prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fae46-180">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="fae46-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fae46-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="fae46-182">1 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-182">1\.</span></span> <span data-ttu-id="fae46-183">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="fae46-183">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="fae46-184">2 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-184">2\.</span></span> <span data-ttu-id="fae46-185">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="fae46-185">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="fae46-186">3 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-186">3\.</span></span> <span data-ttu-id="fae46-187">Pro prostředí WebAssembly Blazor spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="fae46-187">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="fae46-188">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fae46-188">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="fae46-189">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="fae46-189">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="fae46-190">4 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-190">4\.</span></span> <span data-ttu-id="fae46-191">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fae46-191">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="fae46-192">5 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-192">5\.</span></span> <span data-ttu-id="fae46-193">Pro projekt Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="fae46-193">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="fae46-194">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="fae46-194">Select **Yes**.</span></span>

   <span data-ttu-id="fae46-195">6 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-195">6\.</span></span> <span data-ttu-id="fae46-196">Pokud používáte aplikaci serveru Blazor, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fae46-196">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="fae46-197">Pokud používáte aplikaci WebAssembly Blazor, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="fae46-197">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="fae46-198">7 \.</span><span class="sxs-lookup"><span data-stu-id="fae46-198">7\.</span></span> <span data-ttu-id="fae46-199">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fae46-199">In a browser, navigate to `https://localhost:5001`.</span></span>

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

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="fae46-200">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="fae46-200">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="fae46-201">Pro prostředí WebAssembly Blazor spusťte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="fae46-201">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="fae46-202">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="fae46-202">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="fae46-203">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="fae46-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="fae46-204">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fae46-204">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="fae46-205">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="fae46-205">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="fae46-206">Domů</span><span class="sxs-lookup"><span data-stu-id="fae46-206">Home</span></span>
* <span data-ttu-id="fae46-207">Čítač</span><span class="sxs-lookup"><span data-stu-id="fae46-207">Counter</span></span>
* <span data-ttu-id="fae46-208">Načíst data</span><span class="sxs-lookup"><span data-stu-id="fae46-208">Fetch data</span></span>

<span data-ttu-id="fae46-209">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="fae46-209">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="fae46-210">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít C#.</span><span class="sxs-lookup"><span data-stu-id="fae46-210">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="fae46-211">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="fae46-211">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="fae46-212">Požadavek na `/counter` v prohlížeči, jak je uvedeno v direktivě `@page` v horní části, způsobí, že komponenta `Counter` vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="fae46-212">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="fae46-213">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fae46-213">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="fae46-214">Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :</span><span class="sxs-lookup"><span data-stu-id="fae46-214">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="fae46-215">Událost `onclick` je aktivována.</span><span class="sxs-lookup"><span data-stu-id="fae46-215">The `onclick` event is fired.</span></span>
* <span data-ttu-id="fae46-216">Je volána metoda `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="fae46-216">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="fae46-217">Hodnota `currentCount` se zvýší.</span><span class="sxs-lookup"><span data-stu-id="fae46-217">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="fae46-218">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="fae46-218">The component is rendered again.</span></span>

<span data-ttu-id="fae46-219">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="fae46-219">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="fae46-220">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="fae46-220">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="fae46-221">Můžete například přidat komponentu `Counter` do domovské stránky aplikace přidáním prvku `<Counter />` do komponenty `Index`.</span><span class="sxs-lookup"><span data-stu-id="fae46-221">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="fae46-222">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="fae46-222">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="fae46-223">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fae46-223">Run the app.</span></span> <span data-ttu-id="fae46-224">Domovská stránka má vlastní čítač poskytovaný komponentou `Counter`.</span><span class="sxs-lookup"><span data-stu-id="fae46-224">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="fae46-225">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="fae46-225">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="fae46-226">Chcete-li přidat parametr do součásti `Counter`, aktualizujte blok `@code` komponenty:</span><span class="sxs-lookup"><span data-stu-id="fae46-226">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="fae46-227">Přidejte veřejnou vlastnost pro `IncrementAmount` s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="fae46-227">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="fae46-228">Změňte metodu `IncrementCount` tak, aby při zvyšování hodnoty `currentCount` používala `IncrementAmount`.</span><span class="sxs-lookup"><span data-stu-id="fae46-228">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="fae46-229">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="fae46-229">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="fae46-230">Zadejte `IncrementAmount` v prvku `<Counter>` komponenty `Index` pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="fae46-230">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="fae46-231">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="fae46-231">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="fae46-232">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fae46-232">Run the app.</span></span> <span data-ttu-id="fae46-233">Komponenta `Index` má vlastní čítač, který se při každém výběru tlačítka pro **kliknutí na tlačítko Další** zvyšuje o 10.</span><span class="sxs-lookup"><span data-stu-id="fae46-233">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="fae46-234">Součást `Counter` (*čítač. Razor*) v `/counter` bude nadále zvyšovat o jednu.</span><span class="sxs-lookup"><span data-stu-id="fae46-234">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fae46-235">Další kroky</span><span class="sxs-lookup"><span data-stu-id="fae46-235">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="fae46-236">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fae46-236">Additional resources</span></span>

* <xref:signalr/introduction>
