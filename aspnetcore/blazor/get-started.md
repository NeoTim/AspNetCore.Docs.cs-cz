---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením aplikace Blazor pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/25/2019
uid: blazor/get-started
ms.openlocfilehash: ef9113dbfdbbd5920c4358cdac0c77c60f40b7c8
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288794"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="21f9d-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="21f9d-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="21f9d-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="21f9d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="21f9d-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="21f9d-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="21f9d-106">Nainstalujte nejnovější verzi [sady .NET Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="21f9d-106">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="21f9d-107">Nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="21f9d-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview9.19465.2
   ```

1. <span data-ttu-id="21f9d-108">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="21f9d-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="21f9d-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="21f9d-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="21f9d-110">1 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-110">1\.</span></span> <span data-ttu-id="21f9d-111">Nainstalujte si nejnovější verzi sady [Visual Studio](https://visualstudio.com/vs/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="21f9d-111">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="21f9d-112">2 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-112">2\.</span></span> <span data-ttu-id="21f9d-113">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="21f9d-113">Create a new project.</span></span>

   <span data-ttu-id="21f9d-114">3 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-114">3\.</span></span> <span data-ttu-id="21f9d-115">Vyberte **aplikaci Blazor**.</span><span class="sxs-lookup"><span data-stu-id="21f9d-115">Select **Blazor App**.</span></span> <span data-ttu-id="21f9d-116">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="21f9d-116">Select **Next**.</span></span>

   <span data-ttu-id="21f9d-117">4 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-117">4\.</span></span> <span data-ttu-id="21f9d-118">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="21f9d-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="21f9d-119">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="21f9d-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="21f9d-120">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="21f9d-120">Select **Create**.</span></span>

   <span data-ttu-id="21f9d-121">5 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-121">5\.</span></span> <span data-ttu-id="21f9d-122">Pro prostředí WebAssembly Blazor vyberte šablonu **aplikace Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="21f9d-122">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="21f9d-123">Pro prostředí Blazor serveru vyberte šablonu **aplikace serveru Blazor** .</span><span class="sxs-lookup"><span data-stu-id="21f9d-123">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="21f9d-124">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="21f9d-124">Select **Create**.</span></span> <span data-ttu-id="21f9d-125">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="21f9d-125">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="21f9d-126">6 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-126">6\.</span></span> <span data-ttu-id="21f9d-127">Stisknutím klávesy **F5** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="21f9d-127">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="21f9d-128">Pokud jste nainstalovali rozšíření sady Visual Studio Blazor pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="21f9d-128">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="21f9d-129">Instalace šablon Blazor do příkazového prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="21f9d-129">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="21f9d-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="21f9d-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="21f9d-131">1 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-131">1\.</span></span> <span data-ttu-id="21f9d-132">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="21f9d-132">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="21f9d-133">2 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-133">2\.</span></span> <span data-ttu-id="21f9d-134">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="21f9d-134">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="21f9d-135">3 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-135">3\.</span></span> <span data-ttu-id="21f9d-136">Pro prostředí WebAssembly Blazor spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="21f9d-136">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="21f9d-137">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="21f9d-137">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="21f9d-138">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="21f9d-138">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="21f9d-139">4 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-139">4\.</span></span> <span data-ttu-id="21f9d-140">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="21f9d-140">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="21f9d-141">5 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-141">5\.</span></span> <span data-ttu-id="21f9d-142">Pro projekt Blazor serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="21f9d-142">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="21f9d-143">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="21f9d-143">Select **Yes**.</span></span>

   <span data-ttu-id="21f9d-144">6 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-144">6\.</span></span> <span data-ttu-id="21f9d-145">Pokud používáte aplikaci serveru Blazor, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="21f9d-145">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="21f9d-146">Pokud používáte aplikaci WebAssembly Blazor, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="21f9d-146">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="21f9d-147">7 \.</span><span class="sxs-lookup"><span data-stu-id="21f9d-147">7\.</span></span> <span data-ttu-id="21f9d-148">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="21f9d-148">In a browser, navigate to `https://localhost:5001`.</span></span>

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

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="21f9d-149">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="21f9d-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="21f9d-150">Pro prostředí WebAssembly Blazor spusťte následující příkazy v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="21f9d-150">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="21f9d-151">Pro prostředí Blazor serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="21f9d-151">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="21f9d-152">Informace o dvou modelech hostování Blazor, *serverech Blazor* a *Blazor WebAssembly*naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="21f9d-152">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="21f9d-153">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="21f9d-153">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="21f9d-154">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="21f9d-154">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="21f9d-155">Domů</span><span class="sxs-lookup"><span data-stu-id="21f9d-155">Home</span></span>
* <span data-ttu-id="21f9d-156">Čítač</span><span class="sxs-lookup"><span data-stu-id="21f9d-156">Counter</span></span>
* <span data-ttu-id="21f9d-157">Načíst data</span><span class="sxs-lookup"><span data-stu-id="21f9d-157">Fetch data</span></span>

<span data-ttu-id="21f9d-158">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="21f9d-158">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="21f9d-159">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale s Blazor můžete použít C#.</span><span class="sxs-lookup"><span data-stu-id="21f9d-159">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="21f9d-160">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="21f9d-160">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="21f9d-161">Požadavek na `/counter` v prohlížeči, jak je uvedeno v direktivě `@page` v horní části, způsobí, že komponenta `Counter` vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="21f9d-161">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="21f9d-162">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="21f9d-162">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="21f9d-163">Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :</span><span class="sxs-lookup"><span data-stu-id="21f9d-163">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="21f9d-164">Událost `onclick` je aktivována.</span><span class="sxs-lookup"><span data-stu-id="21f9d-164">The `onclick` event is fired.</span></span>
* <span data-ttu-id="21f9d-165">Je volána metoda `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="21f9d-165">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="21f9d-166">Hodnota `currentCount` se zvýší.</span><span class="sxs-lookup"><span data-stu-id="21f9d-166">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="21f9d-167">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="21f9d-167">The component is rendered again.</span></span>

<span data-ttu-id="21f9d-168">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="21f9d-168">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="21f9d-169">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="21f9d-169">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="21f9d-170">Můžete například přidat komponentu `Counter` do domovské stránky aplikace přidáním prvku `<Counter />` do komponenty `Index`.</span><span class="sxs-lookup"><span data-stu-id="21f9d-170">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="21f9d-171">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="21f9d-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="21f9d-172">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="21f9d-172">Run the app.</span></span> <span data-ttu-id="21f9d-173">Domovská stránka má vlastní čítač poskytovaný komponentou `Counter`.</span><span class="sxs-lookup"><span data-stu-id="21f9d-173">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="21f9d-174">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="21f9d-174">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="21f9d-175">Chcete-li přidat parametr do součásti `Counter`, aktualizujte blok `@code` komponenty:</span><span class="sxs-lookup"><span data-stu-id="21f9d-175">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="21f9d-176">Přidejte veřejnou vlastnost pro `IncrementAmount` s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="21f9d-176">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="21f9d-177">Změňte metodu `IncrementCount` tak, aby při zvyšování hodnoty `currentCount` používala `IncrementAmount`.</span><span class="sxs-lookup"><span data-stu-id="21f9d-177">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="21f9d-178">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="21f9d-178">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="21f9d-179">Zadejte `IncrementAmount` v prvku `<Counter>` komponenty `Index` pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="21f9d-179">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="21f9d-180">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="21f9d-180">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="21f9d-181">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="21f9d-181">Run the app.</span></span> <span data-ttu-id="21f9d-182">Komponenta `Index` má vlastní čítač, který se při každém výběru tlačítka pro **kliknutí na tlačítko Další** zvyšuje o 10.</span><span class="sxs-lookup"><span data-stu-id="21f9d-182">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="21f9d-183">Součást `Counter` (*čítač. Razor*) v `/counter` bude nadále zvyšovat o jednu.</span><span class="sxs-lookup"><span data-stu-id="21f9d-183">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="21f9d-184">Další kroky</span><span class="sxs-lookup"><span data-stu-id="21f9d-184">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="21f9d-185">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="21f9d-185">Additional resources</span></span>

* <xref:signalr/introduction>
