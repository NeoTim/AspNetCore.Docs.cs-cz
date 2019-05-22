---
title: Začínáme s Blazor
author: guardrex
description: Začínáme s Blazor vytvořením Blazor aplikace pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2019
uid: blazor/get-started
ms.openlocfilehash: 09613f5d8a4d130f7dca53f31bdd33de527fc776
ms.sourcegitcommit: b4ef2b00f3e1eb287138f8b43c811cb35a100d3e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969863"
---
# <a name="get-started-with-blazor"></a><span data-ttu-id="fc912-103">Začínáme s Blazor</span><span class="sxs-lookup"><span data-stu-id="fc912-103">Get started with Blazor</span></span>

<span data-ttu-id="fc912-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fc912-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fc912-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="fc912-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="fc912-106">Nainstalujte nejnovější [.NET Core 3.0 ve verzi Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span><span class="sxs-lookup"><span data-stu-id="fc912-106">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="fc912-107">Instalace šablony Blazor spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="fc912-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview5-19227-01
   ```

1. <span data-ttu-id="fc912-108">Postupujte podle pokynů podle vaší volby nástrojů:</span><span class="sxs-lookup"><span data-stu-id="fc912-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fc912-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc912-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="fc912-110">1.&nbsp;nainstalujte nejnovější [ve verzi preview sady Visual Studio](https://visualstudio.com/preview) s **vývoj pro ASP.NET a web** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="fc912-110">1.&nbsp;Install the latest [Visual Studio preview](https://visualstudio.com/preview) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="fc912-111">2.&nbsp;nainstalujte nejnovější [Blazor rozšíření](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="fc912-111">2.&nbsp;Install the latest [Blazor extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="fc912-112">Tento krok zpřístupní Blazor šablony sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fc912-112">This step makes Blazor templates available to Visual Studio.</span></span>

   <span data-ttu-id="fc912-113">3.&nbsp;Pokud používáte nejnovější stabilní verze sady Visual Studio (nikoli verze preview), povolte sadě Visual Studio pomocí sady SDK ve verzi preview: Otevřít **nástroje** > **možnosti** v panelu nabídek.</span><span class="sxs-lookup"><span data-stu-id="fc912-113">3.&nbsp;If using the latest stable release of Visual Studio (not a preview release), enable Visual Studio to use preview SDKs: Open **Tools** > **Options** in the menu bar.</span></span> <span data-ttu-id="fc912-114">Otevřít **projekty a řešení** uzlu.</span><span class="sxs-lookup"><span data-stu-id="fc912-114">Open the **Projects and Solutions** node.</span></span> <span data-ttu-id="fc912-115">Otevřít **.NET Core** kartu. Zaškrtněte políčko u **pomocí verze Preview sady .NET Core SDK**.</span><span class="sxs-lookup"><span data-stu-id="fc912-115">Open the **.NET Core** tab. Check the box for **Use previews of the .NET Core SDK**.</span></span> <span data-ttu-id="fc912-116">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="fc912-116">Select **OK**.</span></span>

   <span data-ttu-id="fc912-117">4.&nbsp;vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="fc912-117">4.&nbsp;Create a new project.</span></span>

   <span data-ttu-id="fc912-118">5.&nbsp;vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="fc912-118">5.&nbsp;Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="fc912-119">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="fc912-119">Select **Next**.</span></span>

   <span data-ttu-id="fc912-120">6.&nbsp;zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="fc912-120">6.&nbsp;Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="fc912-121">Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt.</span><span class="sxs-lookup"><span data-stu-id="fc912-121">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="fc912-122">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fc912-122">Select **Create**.</span></span>

   <span data-ttu-id="fc912-123">7.&nbsp;v **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.</span><span class="sxs-lookup"><span data-stu-id="fc912-123">7.&nbsp;In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>

   <span data-ttu-id="fc912-124">8.&nbsp;Blazor prostředí na straně klienta, vyberte **Blazor (na straně klienta)** šablony.</span><span class="sxs-lookup"><span data-stu-id="fc912-124">8.&nbsp;For a Blazor client-side experience, choose the **Blazor (client-side)** template.</span></span> <span data-ttu-id="fc912-125">Blazor prostředí na straně serveru, zvolte **Blazor (serverové)** šablony.</span><span class="sxs-lookup"><span data-stu-id="fc912-125">For a Blazor server-side experience, choose the **Blazor (server-side)** template.</span></span> <span data-ttu-id="fc912-126">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fc912-126">Select **Create**.</span></span> <span data-ttu-id="fc912-127">Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="fc912-127">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="fc912-128">9.&nbsp;stiskněte **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="fc912-128">9.&nbsp;Press **F5** to run the app.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="fc912-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fc912-129">Visual Studio Code</span></span>](#tab/visual-studio-code)
   
   <span data-ttu-id="fc912-130">1.&nbsp;nainstalovat [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="fc912-130">1.&nbsp;Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="fc912-131">2.&nbsp;nainstalujte nejnovější [ C# pro Visual Studio Code příponou](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="fc912-131">2.&nbsp;Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="fc912-132">3.&nbsp;Blazor prostředí na straně klienta, spusťte následující příkaz z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="fc912-132">3.&nbsp;For a Blazor client-side experience, execute the following command from a command shell:</span></span>

      ```console
      dotnet new blazor -o WebApplication1
      ```

      <span data-ttu-id="fc912-133">Blazor prostředí na straně serveru spusťte následující příkaz z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="fc912-133">For a Blazor server-side experience, execute the following command from a command shell:</span></span>

      ```console
      dotnet new blazorserverside -o WebApplication1
      ```

      <span data-ttu-id="fc912-134">Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="fc912-134">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="fc912-135">4.&nbsp;otevřít *WebApplication1* složky ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fc912-135">4.&nbsp;Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="fc912-136">5.&nbsp;projektu pro Blazor na straně serveru, integrovaného vývojového prostředí požádá, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="fc912-136">5.&nbsp;For a Blazor server-side project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="fc912-137">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="fc912-137">Select **Yes**.</span></span>

   <span data-ttu-id="fc912-138">6.&nbsp;při použití aplikace na straně serveru Blazor aplikaci spustíme s použitím ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fc912-138">6.&nbsp;If using a Blazor server-side app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="fc912-139">Pokud používáte aplikaci na straně klienta Blazor, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="fc912-139">If using a Blazor client-side app, execute `dotnet run` from the app's project folder.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1.&nbsp;Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2.&nbsp;Select **File** > **New Solution** or **New Project**.

   3.&nbsp;In the sidebar, select **.NET Core** > **App**.

   4.&nbsp;For a Blazor server-side experience, select the **ASP.NET Core Blazor (server-side)** template. For a Blazor client-side experience, select the **ASP.NET Core Blazor (client-side)** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5.&nbsp;The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6.&nbsp;In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7.&nbsp;Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="fc912-140">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="fc912-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="fc912-141">Blazor prostředí na straně klienta spusťte následující příkazy z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="fc912-141">For a Blazor client-side experience, execute the following commands from a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="fc912-142">Blazor prostředí na straně serveru spusťte následující příkazy z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="fc912-142">For a Blazor server-side experience, execute the following commands from a command shell:</span></span>

   ```console
   dotnet new blazorserverside -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="fc912-143">Informace o dvou modelech hostování Blazor a na straně serveru a na straně klienta najdete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="fc912-143">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   ---

<span data-ttu-id="fc912-144">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fc912-144">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="fc912-145">Více stránek jsou k dispozici z karty na bočním panelu:</span><span class="sxs-lookup"><span data-stu-id="fc912-145">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="fc912-146">Domů</span><span class="sxs-lookup"><span data-stu-id="fc912-146">Home</span></span>
* <span data-ttu-id="fc912-147">Čítač</span><span class="sxs-lookup"><span data-stu-id="fc912-147">Counter</span></span>
* <span data-ttu-id="fc912-148">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="fc912-148">Fetch data</span></span>

<span data-ttu-id="fc912-149">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="fc912-149">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="fc912-150">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti syntaxe Razor poskytují lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="fc912-150">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="fc912-151">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="fc912-151">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="fc912-152">Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="fc912-152">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="fc912-153">Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="fc912-153">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="fc912-154">Pokaždé, když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="fc912-154">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="fc912-155">`onclick` Událost se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="fc912-155">The `onclick` event is fired.</span></span>
* <span data-ttu-id="fc912-156">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="fc912-156">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="fc912-157">`currentCount` Se zvýší.</span><span class="sxs-lookup"><span data-stu-id="fc912-157">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="fc912-158">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="fc912-158">The component is rendered again.</span></span>

<span data-ttu-id="fc912-159">Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="fc912-159">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="fc912-160">Přidáte součást do jiné součásti pomocí syntaxe pro HTML.</span><span class="sxs-lookup"><span data-stu-id="fc912-160">Add a component to another component using an HTML syntax.</span></span> <span data-ttu-id="fc912-161">Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="fc912-161">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="fc912-162">Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="fc912-162">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="fc912-163">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="fc912-163">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="fc912-164">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fc912-164">Run the app.</span></span> <span data-ttu-id="fc912-165">Na domovskou stránku má svůj vlastní čítače poskytované součást čítače.</span><span class="sxs-lookup"><span data-stu-id="fc912-165">The homepage has its own counter provided by the Counter component.</span></span>

<span data-ttu-id="fc912-166">Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:</span><span class="sxs-lookup"><span data-stu-id="fc912-166">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="fc912-167">Přidání vlastnosti pro `IncrementAmount` s `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="fc912-167">Add a property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="fc912-168">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="fc912-168">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="fc912-169">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="fc912-169">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="fc912-170">Zadejte `IncrementAmount` v komponentě Index `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="fc912-170">Specify the `IncrementAmount` in the Index component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="fc912-171">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="fc912-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="fc912-172">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fc912-172">Run the app.</span></span> <span data-ttu-id="fc912-173">Součást indexu má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="fc912-173">The Index component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="fc912-174">Součást čítače (*Counter.razor*) na `/counter` pořád postupně zvyšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="fc912-174">The Counter component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fc912-175">Další kroky</span><span class="sxs-lookup"><span data-stu-id="fc912-175">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="fc912-176">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fc912-176">Additional resources</span></span>

* <xref:signalr/introduction>
