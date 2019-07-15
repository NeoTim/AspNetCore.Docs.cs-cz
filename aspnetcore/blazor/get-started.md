---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začínáme s Blazor vytvořením Blazor aplikace pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/get-started
ms.openlocfilehash: 147e3b98ba4a5c6edc4f4dede2773730ffbe0385
ms.sourcegitcommit: 040aedca220ed24ee1726e6886daf6906f95a028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67892240"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="68c55-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="68c55-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="68c55-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="68c55-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="68c55-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="68c55-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="68c55-106">Nainstalujte nejnovější [.NET Core 3.0 ve verzi Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span><span class="sxs-lookup"><span data-stu-id="68c55-106">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="68c55-107">Instalace šablony Blazor spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="68c55-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview6.19307.2
   ```

1. <span data-ttu-id="68c55-108">Postupujte podle pokynů podle vaší volby nástrojů:</span><span class="sxs-lookup"><span data-stu-id="68c55-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68c55-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68c55-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="68c55-110">1\.</span><span class="sxs-lookup"><span data-stu-id="68c55-110">1\.</span></span> <span data-ttu-id="68c55-111">Nainstalujte nejnovější [ve verzi preview sady Visual Studio](https://visualstudio.com/vs/preview) s **vývoj pro ASP.NET a web** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="68c55-111">Install the latest [Visual Studio preview](https://visualstudio.com/vs/preview) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="68c55-112">2\.</span><span class="sxs-lookup"><span data-stu-id="68c55-112">2\.</span></span> <span data-ttu-id="68c55-113">Nainstalujte nejnovější [Blazor rozšíření](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="68c55-113">Install the latest [Blazor extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="68c55-114">Tento krok zpřístupní Blazor šablony sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="68c55-114">This step makes Blazor templates available to Visual Studio.</span></span>

   <span data-ttu-id="68c55-115">3\.</span><span class="sxs-lookup"><span data-stu-id="68c55-115">3\.</span></span> <span data-ttu-id="68c55-116">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="68c55-116">Create a new project.</span></span>

   <span data-ttu-id="68c55-117">4\.</span><span class="sxs-lookup"><span data-stu-id="68c55-117">4\.</span></span> <span data-ttu-id="68c55-118">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="68c55-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="68c55-119">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="68c55-119">Select **Next**.</span></span>

   <span data-ttu-id="68c55-120">5\.</span><span class="sxs-lookup"><span data-stu-id="68c55-120">5\.</span></span> <span data-ttu-id="68c55-121">Zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="68c55-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="68c55-122">Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt.</span><span class="sxs-lookup"><span data-stu-id="68c55-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="68c55-123">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="68c55-123">Select **Create**.</span></span>

   <span data-ttu-id="68c55-124">6\.</span><span class="sxs-lookup"><span data-stu-id="68c55-124">6\.</span></span> <span data-ttu-id="68c55-125">V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.</span><span class="sxs-lookup"><span data-stu-id="68c55-125">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>

   <span data-ttu-id="68c55-126">7\.</span><span class="sxs-lookup"><span data-stu-id="68c55-126">7\.</span></span> <span data-ttu-id="68c55-127">Blazor prostředí na straně klienta, vyberte **Blazor (na straně klienta)** šablony.</span><span class="sxs-lookup"><span data-stu-id="68c55-127">For a Blazor client-side experience, choose the **Blazor (client-side)** template.</span></span> <span data-ttu-id="68c55-128">Blazor prostředí na straně serveru, zvolte **aplikace Server Blazor** šablony.</span><span class="sxs-lookup"><span data-stu-id="68c55-128">For a Blazor server-side experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="68c55-129">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="68c55-129">Select **Create**.</span></span> <span data-ttu-id="68c55-130">Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="68c55-130">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="68c55-131">8\.</span><span class="sxs-lookup"><span data-stu-id="68c55-131">8\.</span></span> <span data-ttu-id="68c55-132">Stisknutím klávesy **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="68c55-132">Press **F5** to run the app.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68c55-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68c55-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="68c55-134">1\.</span><span class="sxs-lookup"><span data-stu-id="68c55-134">1\.</span></span> <span data-ttu-id="68c55-135">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="68c55-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="68c55-136">2\.</span><span class="sxs-lookup"><span data-stu-id="68c55-136">2\.</span></span> <span data-ttu-id="68c55-137">Nainstalujte nejnovější [ C# pro Visual Studio Code příponou](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="68c55-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="68c55-138">3\.</span><span class="sxs-lookup"><span data-stu-id="68c55-138">3\.</span></span> <span data-ttu-id="68c55-139">Blazor prostředí na straně klienta spusťte následující příkaz v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="68c55-139">For a Blazor client-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazor -o WebApplication1
      ```

      <span data-ttu-id="68c55-140">Blazor prostředí na straně serveru spusťte následující příkaz v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="68c55-140">For a Blazor server-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorserverside -o WebApplication1
      ```

      <span data-ttu-id="68c55-141">Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="68c55-141">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="68c55-142">4\.</span><span class="sxs-lookup"><span data-stu-id="68c55-142">4\.</span></span> <span data-ttu-id="68c55-143">Otevřít *WebApplication1* složky ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="68c55-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="68c55-144">5\.</span><span class="sxs-lookup"><span data-stu-id="68c55-144">5\.</span></span> <span data-ttu-id="68c55-145">Projekt Blazor na straně serveru požádá o integrovaného vývojového prostředí, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="68c55-145">For a Blazor server-side project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="68c55-146">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="68c55-146">Select **Yes**.</span></span>

   <span data-ttu-id="68c55-147">6\.</span><span class="sxs-lookup"><span data-stu-id="68c55-147">6\.</span></span> <span data-ttu-id="68c55-148">Pokud používáte aplikaci na straně serveru Blazor aplikaci spustíme s použitím ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="68c55-148">If using a Blazor server-side app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="68c55-149">Pokud používáte aplikaci na straně klienta Blazor, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="68c55-149">If using a Blazor client-side app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="68c55-150">7\.</span><span class="sxs-lookup"><span data-stu-id="68c55-150">7\.</span></span> <span data-ttu-id="68c55-151">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="68c55-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor server-side experience, select the **ASP.NET Core Blazor Server App** template. For a Blazor client-side experience, select the **ASP.NET Core Blazor (client-side)** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="68c55-152">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="68c55-152">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="68c55-153">Blazor prostředí na straně klienta spusťte následující příkazy v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="68c55-153">For a Blazor client-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="68c55-154">Blazor prostředí na straně serveru spusťte následující příkazy v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="68c55-154">For a Blazor server-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorserverside -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="68c55-155">Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="68c55-155">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="68c55-156">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="68c55-156">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="68c55-157">Více stránek jsou k dispozici z karty na bočním panelu:</span><span class="sxs-lookup"><span data-stu-id="68c55-157">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="68c55-158">Domů</span><span class="sxs-lookup"><span data-stu-id="68c55-158">Home</span></span>
* <span data-ttu-id="68c55-159">Čítač</span><span class="sxs-lookup"><span data-stu-id="68c55-159">Counter</span></span>
* <span data-ttu-id="68c55-160">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="68c55-160">Fetch data</span></span>

<span data-ttu-id="68c55-161">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="68c55-161">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="68c55-162">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti syntaxe Razor poskytují lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="68c55-162">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="68c55-163">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="68c55-163">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="68c55-164">Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že `Counter` součásti pro vykreslení jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="68c55-164">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="68c55-165">Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="68c55-165">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="68c55-166">Pokaždé, když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="68c55-166">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="68c55-167">`onclick` Událost se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="68c55-167">The `onclick` event is fired.</span></span>
* <span data-ttu-id="68c55-168">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="68c55-168">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="68c55-169">`currentCount` Se zvýší.</span><span class="sxs-lookup"><span data-stu-id="68c55-169">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="68c55-170">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="68c55-170">The component is rendered again.</span></span>

<span data-ttu-id="68c55-171">Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="68c55-171">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="68c55-172">Přidáte součást do jiné součásti pomocí syntaxe kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="68c55-172">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="68c55-173">Například přidat `Counter` na domovskou stránku aplikace tak, že přidáte komponentu `<Counter />` elementu `Index` komponenty.</span><span class="sxs-lookup"><span data-stu-id="68c55-173">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="68c55-174">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="68c55-174">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="68c55-175">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="68c55-175">Run the app.</span></span> <span data-ttu-id="68c55-176">Na domovskou stránku má svůj vlastní čítače poskytované `Counter` komponenty.</span><span class="sxs-lookup"><span data-stu-id="68c55-176">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="68c55-177">Jsou zadány parametry komponenty pomocí atributů nebo [podřízený obsah](xref:blazor/components#child-content), které umožňují nastavit vlastnosti v podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="68c55-177">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="68c55-178">Chcete-li přidat parametr `Counter` komponenty, aktualizovat součásti `@code` bloku:</span><span class="sxs-lookup"><span data-stu-id="68c55-178">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="68c55-179">Přidání vlastnosti pro `IncrementAmount` s `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="68c55-179">Add a property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="68c55-180">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="68c55-180">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="68c55-181">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="68c55-181">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="68c55-182">Zadejte `IncrementAmount` v `Index` komponenty `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="68c55-182">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="68c55-183">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="68c55-183">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="68c55-184">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="68c55-184">Run the app.</span></span> <span data-ttu-id="68c55-185">`Index` Komponenta má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="68c55-185">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="68c55-186">`Counter` Součásti (*Counter.razor*) na `/counter` pořád postupně zvyšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="68c55-186">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="68c55-187">Další kroky</span><span class="sxs-lookup"><span data-stu-id="68c55-187">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="68c55-188">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="68c55-188">Additional resources</span></span>

* <xref:signalr/introduction>
