---
title: Začínáme s Blazor
author: guardrex
description: Zjistěte, jak začít pracovat s Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2019
uid: blazor/get-started
ms.openlocfilehash: 45ae0acc6aaee433cce4eddb2fe9c59c306581d7
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982669"
---
# <a name="get-started-with-blazor"></a><span data-ttu-id="2de6f-103">Začínáme s Blazor</span><span class="sxs-lookup"><span data-stu-id="2de6f-103">Get started with Blazor</span></span>

<span data-ttu-id="2de6f-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2de6f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2de6f-105">V několika krocích Blazor začněte:</span><span class="sxs-lookup"><span data-stu-id="2de6f-105">In a few steps, get started with Blazor:</span></span>

1. <span data-ttu-id="2de6f-106">Nainstalujte nejnovější [.NET Core 3.0 ve verzi Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span><span class="sxs-lookup"><span data-stu-id="2de6f-106">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="2de6f-107">Instalace šablony Blazor spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="2de6f-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview4-19216-03
   ```

1. <span data-ttu-id="2de6f-108">Postupujte podle pokynů podle vaší volby nástrojů:</span><span class="sxs-lookup"><span data-stu-id="2de6f-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2de6f-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2de6f-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="2de6f-110">1.&nbsp;nainstalovat verzi preview [Visual Studio 2019](https://visualstudio.com/preview) s **vývoj pro ASP.NET a web** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="2de6f-110">1.&nbsp;Install the latest preview of [Visual Studio 2019](https://visualstudio.com/preview) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="2de6f-111">2.&nbsp;nainstalujte nejnovější [Blazor rozšíření](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="2de6f-111">2.&nbsp;Install the latest [Blazor extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="2de6f-112">Tento krok zpřístupní Blazor šablony sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2de6f-112">This step makes Blazor templates available to Visual Studio.</span></span>

   <span data-ttu-id="2de6f-113">3.&nbsp;povolit sady Visual Studio pro použití ve verzi preview sady SDK: Otevřít **nástroje** > **možnosti** v panelu nabídek.</span><span class="sxs-lookup"><span data-stu-id="2de6f-113">3.&nbsp;Enable Visual Studio to use preview SDKs: Open **Tools** > **Options** in the menu bar.</span></span> <span data-ttu-id="2de6f-114">Otevřít **projekty a řešení** uzlu.</span><span class="sxs-lookup"><span data-stu-id="2de6f-114">Open the **Projects and Solutions** node.</span></span> <span data-ttu-id="2de6f-115">Otevřít **.NET Core** kartu. Zaškrtněte políčko u **pomocí verze Preview sady .NET Core SDK**.</span><span class="sxs-lookup"><span data-stu-id="2de6f-115">Open the **.NET Core** tab. Check the box for **Use previews of the .NET Core SDK**.</span></span> <span data-ttu-id="2de6f-116">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="2de6f-116">Select **OK**.</span></span>

   <span data-ttu-id="2de6f-117">4.&nbsp;vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="2de6f-117">4.&nbsp;Create a new project.</span></span>

   <span data-ttu-id="2de6f-118">5.&nbsp;vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="2de6f-118">5.&nbsp;Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="2de6f-119">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="2de6f-119">Select **Next**.</span></span>

   <span data-ttu-id="2de6f-120">6.&nbsp;zadejte název **název projektu** pole.</span><span class="sxs-lookup"><span data-stu-id="2de6f-120">6.&nbsp;Provide a name in the **Project name** field.</span></span> <span data-ttu-id="2de6f-121">Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt.</span><span class="sxs-lookup"><span data-stu-id="2de6f-121">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2de6f-122">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2de6f-122">Select **Create**.</span></span>

   <span data-ttu-id="2de6f-123">7.&nbsp;Ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány v horní části.</span><span class="sxs-lookup"><span data-stu-id="2de6f-123">7.&nbsp;Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>

   <span data-ttu-id="2de6f-124">8.&nbsp;prostředí Blazor na straně klienta, vyberte **Blazor (na straně klienta)** šablony.</span><span class="sxs-lookup"><span data-stu-id="2de6f-124">8.&nbsp;For an experience with Blazor client-side, choose the **Blazor (client-side)** template.</span></span> <span data-ttu-id="2de6f-125">Pro prostředí Blazor na straně serveru, zvolte **Blazor (serverové)** šablony.</span><span class="sxs-lookup"><span data-stu-id="2de6f-125">For an experience with Blazor server-side, choose the **Blazor (server-side)** template.</span></span> <span data-ttu-id="2de6f-126">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2de6f-126">Select **Create**.</span></span>

   <span data-ttu-id="2de6f-127">9.&nbsp;stiskněte **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2de6f-127">9.&nbsp;Press **F5** to run the app.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2de6f-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2de6f-128">Visual Studio Code</span></span>](#tab/visual-studio-code)
   
   <span data-ttu-id="2de6f-129">1.&nbsp;nainstalovat [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="2de6f-129">1.&nbsp;Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="2de6f-130">2.&nbsp;nainstalujte nejnovější [ C# pro Visual Studio Code příponou](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="2de6f-130">2.&nbsp;Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="2de6f-131">3.&nbsp;prostředí Blazor na straně klienta, spusťte následující příkaz z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="2de6f-131">3.&nbsp;For an experience with Blazor client-side, execute the following command from a command shell:</span></span>

      ```console
      dotnet new blazor -o WebApplication1
      ```

      <span data-ttu-id="2de6f-132">Pro prostředí Blazor na straně serveru spusťte následující příkaz z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="2de6f-132">For an experience with Blazor server-side, execute the following command from a command shell:</span></span>

      ```console
      dotnet new blazorserverside -o WebApplication1
      ```

      > [!NOTE]
      > <span data-ttu-id="2de6f-133">V systému macOS v technologii ASP.NET Core 3.0 ve verzi Preview 4 se podporuje jenom Blazor straně klienta.</span><span class="sxs-lookup"><span data-stu-id="2de6f-133">Only Blazor client-side is supported on macOS in ASP.NET Core 3.0 Preview 4.</span></span> <span data-ttu-id="2de6f-134">Další informace najdete v tématu [Blazor na straně serveru: dotnet spustit selže s InvalidOperationException v systému MacOS](https://github.com/aspnet/AspNetCore/issues/9402).</span><span class="sxs-lookup"><span data-stu-id="2de6f-134">For more information, see [Blazor server side: dotnet run fails with InvalidOperationException on MacOS](https://github.com/aspnet/AspNetCore/issues/9402).</span></span>

   <span data-ttu-id="2de6f-135">4.&nbsp;otevřít *WebApplication1* složky ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2de6f-135">4.&nbsp;Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="2de6f-136">5.&nbsp;po zobrazení výzvy Visual Studio Code pro projekt Blazor na straně serveru, chcete-li přidat požadované prostředky pro sestavení a ladění projektu, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="2de6f-136">5.&nbsp;When prompted by Visual Studio Code for a Blazor server-side project to add required assets to build and debug the project, select **Yes**.</span></span>

   <span data-ttu-id="2de6f-137">6.&nbsp;při použití aplikace na straně serveru Blazor aplikaci spustíme s použitím ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2de6f-137">6.&nbsp;If using a Blazor server-side app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="2de6f-138">Pokud používáte aplikaci na straně klienta Blazor, spusťte `dotnet run` ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2de6f-138">If using a Blazor client-side app, execute `dotnet run` from the app's project folder.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1.&nbsp;Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2.&nbsp;Select **File** > **New Solution** or **New Project**.

   3.&nbsp;In the sidebar, select **.NET Core** > **App**.

   4.&nbsp;For an experience with Blazor server-side, select the **ASP.NET Core Blazor (server-side)** template. For an experience with Blazor server-side, select the **ASP.NET Core Blazor (client-side)** template. Select **Next**.

   5.&nbsp;The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6.&nbsp;In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7.&nbsp;Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2de6f-139">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="2de6f-139">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="2de6f-140">Pro prostředí Blazor na straně klienta spusťte následující příkazy z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="2de6f-140">For an experience with Blazor client-side, execute the following commands from a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="2de6f-141">Pro prostředí Blazor na straně serveru spusťte následující příkazy z příkazového prostředí:</span><span class="sxs-lookup"><span data-stu-id="2de6f-141">For an experience with Blazor server-side, execute the following commands from a command shell:</span></span>

   ```console
   dotnet new blazorserverside -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   > [!NOTE]
   > <span data-ttu-id="2de6f-142">V systému macOS použití Blazor aplikace na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="2de6f-142">On macOS, use a Blazor client-side app.</span></span> <span data-ttu-id="2de6f-143">Blazor na straně serveru se nepodporuje pro macOS na technologii ASP.NET Core 3.0 ve verzi Preview 4.</span><span class="sxs-lookup"><span data-stu-id="2de6f-143">Blazor server-side isn't supported for macOS on ASP.NET Core 3.0 Preview 4.</span></span> <span data-ttu-id="2de6f-144">Další informace najdete v tématu [Blazor na straně serveru: dotnet spustit selže s InvalidOperationException v systému MacOS](https://github.com/aspnet/AspNetCore/issues/9402).</span><span class="sxs-lookup"><span data-stu-id="2de6f-144">For more information, see [Blazor server side: dotnet run fails with InvalidOperationException on MacOS](https://github.com/aspnet/AspNetCore/issues/9402).</span></span>

   ---

<span data-ttu-id="2de6f-145">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2de6f-145">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="2de6f-146">Více stránek jsou k dispozici z karty na bočním panelu:</span><span class="sxs-lookup"><span data-stu-id="2de6f-146">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="2de6f-147">Domů</span><span class="sxs-lookup"><span data-stu-id="2de6f-147">Home</span></span>
* <span data-ttu-id="2de6f-148">Čítač</span><span class="sxs-lookup"><span data-stu-id="2de6f-148">Counter</span></span>
* <span data-ttu-id="2de6f-149">Načtení dat</span><span class="sxs-lookup"><span data-stu-id="2de6f-149">Fetch data</span></span>

<span data-ttu-id="2de6f-150">Na stránce čítače, vyberte **klikněte na mě** tlačítka se zvýší čítač bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="2de6f-150">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="2de6f-151">Zvyšování hodnoty čítače na webové stránce obvykle vyžaduje zadání jazyka JavaScript, ale součásti syntaxe Razor poskytují lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="2de6f-151">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="2de6f-152">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="2de6f-152">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor)]

<span data-ttu-id="2de6f-153">Žádost o `/counter` v prohlížeči, jak jsou určené `@page` – direktiva v horní části stránky, způsobí, že součást čítače pro vykreslení jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="2de6f-153">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="2de6f-154">Komponenty vykreslování do reprezentaci v paměti, který lze použít k aktualizaci uživatelského rozhraní v flexibilní a efektivní způsob vykreslení stromu.</span><span class="sxs-lookup"><span data-stu-id="2de6f-154">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="2de6f-155">Pokaždé, když **klikněte na mě** výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="2de6f-155">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="2de6f-156">`onclick` Událost se aktivuje.</span><span class="sxs-lookup"><span data-stu-id="2de6f-156">The `onclick` event is fired.</span></span>
* <span data-ttu-id="2de6f-157">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="2de6f-157">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="2de6f-158">`currentCount` Se zvýší.</span><span class="sxs-lookup"><span data-stu-id="2de6f-158">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="2de6f-159">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="2de6f-159">The component is rendered again.</span></span>

<span data-ttu-id="2de6f-160">Modul runtime porovnává nový obsah na předchozí obsah a platí pouze změněný obsah do modelu Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="2de6f-160">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="2de6f-161">Přidáte součást do jiné součásti pomocí syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="2de6f-161">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="2de6f-162">Komponenta parametry jsou určeny pomocí atributů nebo podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="2de6f-162">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="2de6f-163">Například můžete přidat součást čítače na domovskou stránku aplikace tak, že přidáte `<Counter />` – element pro součást indexu.</span><span class="sxs-lookup"><span data-stu-id="2de6f-163">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="2de6f-164">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="2de6f-164">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="2de6f-165">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2de6f-165">Run the app.</span></span> <span data-ttu-id="2de6f-166">Na domovskou stránku má svůj vlastní čítače.</span><span class="sxs-lookup"><span data-stu-id="2de6f-166">The homepage has its own counter.</span></span>

<span data-ttu-id="2de6f-167">Přidání parametru do komponenty čítače, aktualizovat součásti `@functions` blok:</span><span class="sxs-lookup"><span data-stu-id="2de6f-167">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="2de6f-168">Přidání vlastnosti pro `IncrementAmount` dekorován `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="2de6f-168">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="2de6f-169">Změnit `IncrementCount` metoda se má použít `IncrementAmount` při zvýšit hodnotu `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="2de6f-169">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="2de6f-170">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="2de6f-170">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=4-5,9)]

<span data-ttu-id="2de6f-171">Zadejte `IncrementAmount` parametr v komponentě domovské `<Counter>` pomocí atributu element.</span><span class="sxs-lookup"><span data-stu-id="2de6f-171">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="2de6f-172">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="2de6f-172">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor)]

<span data-ttu-id="2de6f-173">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2de6f-173">Run the app.</span></span> <span data-ttu-id="2de6f-174">Na domovskou stránku má svůj vlastní čítač, který zvýší o 10 pokaždé, když **klikněte na mě** výběru tlačítka.</span><span class="sxs-lookup"><span data-stu-id="2de6f-174">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2de6f-175">Další kroky</span><span class="sxs-lookup"><span data-stu-id="2de6f-175">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="2de6f-176">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2de6f-176">Additional resources</span></span>

* <xref:signalr/introduction>
