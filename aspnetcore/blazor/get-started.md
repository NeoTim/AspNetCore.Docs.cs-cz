---
title: Začínáme s Blazor
author: guardrex
description: Začínáme s Blazor vytvořením Blazor aplikace pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/05/2019
uid: blazor/get-started
ms.openlocfilehash: 417f2ad79c14351487763888a9a3b022232b3766
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034437"
---
# <a name="get-started-with-blazor"></a><span data-ttu-id="8235e-103">Začínáme s Blazor</span><span class="sxs-lookup"><span data-stu-id="8235e-103">Get started with Blazor</span></span>

<span data-ttu-id="8235e-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8235e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8235e-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="8235e-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="8235e-106">Nainstalujte nejnovější [.NET Core 3.0 ve verzi Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span><span class="sxs-lookup"><span data-stu-id="8235e-106">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="8235e-107">Instalace šablony Blazor spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="8235e-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview6.19307.2
   ```

1. <span data-ttu-id="8235e-108">Postupujte podle pokynů podle vaší volby nástrojů:</span><span class="sxs-lookup"><span data-stu-id="8235e-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8235e-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8235e-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="8235e-110">1\.</span><span class="sxs-lookup"><span data-stu-id="8235e-110">1\.</span></span> <span data-ttu-id="8235e-111">Nainstalujte nejnovější [ve verzi preview sady Visual Studio](https://visualstudio.com/preview) s **vývoj pro ASP.NET a web** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="8235e-111">Install the latest [Visual Studio preview](https://visualstudio.com/preview) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="8235e-112">2\.</span><span class="sxs-lookup"><span data-stu-id="8235e-112">2\.</span></span> <span data-ttu-id="8235e-113">Nainstalujte nejnovější [Blazor rozšíření](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="8235e-113">Install the latest [Blazor extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="8235e-114">Tento krok zpřístupní Blazor šablony sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8235e-114">This step makes Blazor templates available to Visual Studio.</span></span>

   <span data-ttu-id="8235e-115">3\.</span><span class="sxs-lookup"><span data-stu-id="8235e-115">3\.</span></span> <span data-ttu-id="8235e-116">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="8235e-116">Create a new project.</span></span>

   <span data-ttu-id="8235e-117">4\.</span><span class="sxs-lookup"><span data-stu-id="8235e-117">4\.</span></span> <span data-ttu-id="8235e-118">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="8235e-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="8235e-119">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="8235e-119">Select **Next**.</span></span>

   <span data-ttu-id="8235e-120">5\.</span><span class="sxs-lookup"><span data-stu-id="8235e-120">5\.</span></span> <span data-ttu-id="8235e-121">Zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="8235e-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="8235e-122">Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt.</span><span class="sxs-lookup"><span data-stu-id="8235e-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="8235e-123">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8235e-123">Select **Create**.</span></span>

   <span data-ttu-id="8235e-124">6\.</span><span class="sxs-lookup"><span data-stu-id="8235e-124">6\.</span></span> <span data-ttu-id="8235e-125">V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.</span><span class="sxs-lookup"><span data-stu-id="8235e-125">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>

   <span data-ttu-id="8235e-126">7\.</span><span class="sxs-lookup"><span data-stu-id="8235e-126">7\.</span></span> <span data-ttu-id="8235e-127">Blazor prostředí na straně klienta, vyberte **Blazor (na straně klienta)** šablony.</span><span class="sxs-lookup"><span data-stu-id="8235e-127">For a Blazor client-side experience, choose the **Blazor (client-side)** template.</span></span> <span data-ttu-id="8235e-128">Blazor prostředí na straně serveru, zvolte **Blazor (serverové)** šablony.</span><span class="sxs-lookup"><span data-stu-id="8235e-128">For a Blazor server-side experience, choose the **Blazor (server-side)** template.</span></span> <span data-ttu-id="8235e-129">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8235e-129">Select **Create**.</span></span> <span data-ttu-id="8235e-130">Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="8235e-130">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="8235e-131">8\.</span><span class="sxs-lookup"><span data-stu-id="8235e-131">8\.</span></span> <span data-ttu-id="8235e-132">Stisknutím klávesy **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8235e-132">Press **F5** to run the app.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8235e-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8235e-133">Visual Studio Code</span></span>](#tab/visual-studio-code)
   
   <span data-ttu-id="8235e-134">1\.</span><span class="sxs-lookup"><span data-stu-id="8235e-134">1\.</span></span> <span data-ttu-id="8235e-135">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="8235e-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="8235e-136">2\.</span><span class="sxs-lookup"><span data-stu-id="8235e-136">2\.</span></span> <span data-ttu-id="8235e-137">Nainstalujte nejnovější [ C# pro Visual Studio Code příponou](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="8235e-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="8235e-138">3\.</span><span class="sxs-lookup"><span data-stu-id="8235e-138">3\.</span></span> <span data-ttu-id="8235e-139">Blazor prostředí na straně klienta spusťte následující příkaz z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="8235e-139">For a Blazor client-side experience, execute the following command from a command shell:</span></span>

      ```console
      dotnet new blazor -o WebApplication1
      ```

      <span data-ttu-id="8235e-140">Blazor prostředí na straně serveru spusťte následující příkaz z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="8235e-140">For a Blazor server-side experience, execute the following command from a command shell:</span></span>

      ```console
      dotnet new blazorserverside -o WebApplication1
      ```

      <span data-ttu-id="8235e-141">Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="8235e-141">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="8235e-142">4\.</span><span class="sxs-lookup"><span data-stu-id="8235e-142">4\.</span></span> <span data-ttu-id="8235e-143">Otevřít *WebApplication1* složky ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8235e-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="8235e-144">5\.</span><span class="sxs-lookup"><span data-stu-id="8235e-144">5\.</span></span> <span data-ttu-id="8235e-145">Projekt Blazor na straně serveru požádá o integrovaného vývojového prostředí, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="8235e-145">For a Blazor server-side project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="8235e-146">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="8235e-146">Select **Yes**.</span></span>

   <span data-ttu-id="8235e-147">6\.</span><span class="sxs-lookup"><span data-stu-id="8235e-147">6\.</span></span> <span data-ttu-id="8235e-148">Pokud používáte aplikaci na straně serveru Blazor aplikaci spustíme s použitím ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8235e-148">If using a Blazor server-side app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="8235e-149">Pokud používáte aplikaci na straně klienta Blazor, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="8235e-149">If using a Blazor client-side app, execute `dotnet run` from the app's project folder.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor server-side experience, select the **ASP.NET Core Blazor (server-side)** template. For a Blazor client-side experience, select the **ASP.NET Core Blazor (client-side)** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8235e-150">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="8235e-150">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="8235e-151">Blazor prostředí na straně klienta spusťte následující příkazy z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="8235e-151">For a Blazor client-side experience, execute the following commands from a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="8235e-152">Blazor prostředí na straně serveru spusťte následující příkazy z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="8235e-152">For a Blazor server-side experience, execute the following commands from a command shell:</span></span>

   ```console
   dotnet new blazorserverside -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="8235e-153">Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="8235e-153">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   ---

<span data-ttu-id="8235e-154">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8235e-154">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="8235e-155">Více stránek jsou k dispozici z karty na bočním panelu:</span><span class="sxs-lookup"><span data-stu-id="8235e-155">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="8235e-156">Domů</span><span class="sxs-lookup"><span data-stu-id="8235e-156">Home</span></span>
* <span data-ttu-id="8235e-157">Čítač</span><span class="sxs-lookup"><span data-stu-id="8235e-157">Counter</span></span>
* <span data-ttu-id="8235e-158">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="8235e-158">Fetch data</span></span>

<span data-ttu-id="8235e-159">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="8235e-159">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="8235e-160">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti syntaxe Razor poskytují lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="8235e-160">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="8235e-161">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="8235e-161">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="8235e-162">Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="8235e-162">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="8235e-163">Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="8235e-163">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="8235e-164">Pokaždé, když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="8235e-164">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="8235e-165">`onclick` Událost se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="8235e-165">The `onclick` event is fired.</span></span>
* <span data-ttu-id="8235e-166">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="8235e-166">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="8235e-167">`currentCount` Se zvýší.</span><span class="sxs-lookup"><span data-stu-id="8235e-167">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="8235e-168">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="8235e-168">The component is rendered again.</span></span>

<span data-ttu-id="8235e-169">Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="8235e-169">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="8235e-170">Přidáte součást do jiné součásti pomocí syntaxe kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="8235e-170">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="8235e-171">Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="8235e-171">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="8235e-172">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="8235e-172">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="8235e-173">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8235e-173">Run the app.</span></span> <span data-ttu-id="8235e-174">Na domovskou stránku má svůj vlastní čítače poskytované součást čítače.</span><span class="sxs-lookup"><span data-stu-id="8235e-174">The homepage has its own counter provided by the Counter component.</span></span>

<span data-ttu-id="8235e-175">Jsou zadány parametry komponenty pomocí atributů nebo [podřízený obsah](xref:blazor/components#child-content), které umožňují nastavit vlastnosti v podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="8235e-175">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="8235e-176">Přidání parametru do komponenty čítače, aktualizovat součásti `@code` blok:</span><span class="sxs-lookup"><span data-stu-id="8235e-176">To add a parameter to the Counter component, update the component's `@code` block:</span></span>

* <span data-ttu-id="8235e-177">Přidání vlastnosti pro `IncrementAmount` s `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="8235e-177">Add a property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="8235e-178">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="8235e-178">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="8235e-179">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="8235e-179">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="8235e-180">Zadejte `IncrementAmount` v komponentě Index `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="8235e-180">Specify the `IncrementAmount` in the Index component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="8235e-181">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="8235e-181">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="8235e-182">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8235e-182">Run the app.</span></span> <span data-ttu-id="8235e-183">Součást indexu má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="8235e-183">The Index component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="8235e-184">Součást čítače (*Counter.razor*) na `/counter` pořád postupně zvyšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="8235e-184">The Counter component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8235e-185">Další kroky</span><span class="sxs-lookup"><span data-stu-id="8235e-185">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="8235e-186">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8235e-186">Additional resources</span></span>

* <xref:signalr/introduction>
