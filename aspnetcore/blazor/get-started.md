---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením aplikace Blazor pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
uid: blazor/get-started
ms.openlocfilehash: 9ebe1e2d36f96bfa214c2389535c28db5db5020a
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412410"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="8ac4f-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="8ac4f-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="8ac4f-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8ac4f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8ac4f-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="8ac4f-106">Nainstalujte nejnovější verzi [sady .NET Core 3,0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="8ac4f-106">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="8ac4f-107">Nainstalujte šablony Blazor spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview7.19365.7
   ```

1. <span data-ttu-id="8ac4f-108">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8ac4f-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ac4f-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="8ac4f-110">1\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-110">1\.</span></span> <span data-ttu-id="8ac4f-111">Nainstalujte si nejnovější verzi sady [Visual Studio Preview](https://visualstudio.com/vs/preview) s využitím úlohy **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="8ac4f-111">Install the latest [Visual Studio preview](https://visualstudio.com/vs/preview) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="8ac4f-112">2\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-112">2\.</span></span> <span data-ttu-id="8ac4f-113">Nainstalujte nejnovější [rozšíření Blazor](https://go.microsoft.com/fwlink/?linkid=870389) z Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-113">Install the latest [Blazor extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="8ac4f-114">Tento krok zpřístupňuje šablony Blazor k dispozici pro Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-114">This step makes Blazor templates available to Visual Studio.</span></span>

   <span data-ttu-id="8ac4f-115">3\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-115">3\.</span></span> <span data-ttu-id="8ac4f-116">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-116">Create a new project.</span></span>

   <span data-ttu-id="8ac4f-117">4\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-117">4\.</span></span> <span data-ttu-id="8ac4f-118">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="8ac4f-119">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-119">Select **Next**.</span></span>

   <span data-ttu-id="8ac4f-120">5\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-120">5\.</span></span> <span data-ttu-id="8ac4f-121">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="8ac4f-122">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="8ac4f-123">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-123">Select **Create**.</span></span>

   <span data-ttu-id="8ac4f-124">6\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-124">6\.</span></span> <span data-ttu-id="8ac4f-125">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="8ac4f-125">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>

   <span data-ttu-id="8ac4f-126">7\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-126">7\.</span></span> <span data-ttu-id="8ac4f-127">Pro Blazor prostředí na straně klienta vyberte šablonu **aplikace Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="8ac4f-127">For a Blazor client-side experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="8ac4f-128">Pro Blazor prostředí na straně serveru vyberte šablonu **aplikace serveru Blazor** .</span><span class="sxs-lookup"><span data-stu-id="8ac4f-128">For a Blazor server-side experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="8ac4f-129">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-129">Select **Create**.</span></span> <span data-ttu-id="8ac4f-130">Informace o dvou modelech hostování Blazor, na straně serveru a na straně klienta, najdete v <xref:blazor/hosting-models>tématu.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-130">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="8ac4f-131">8\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-131">8\.</span></span> <span data-ttu-id="8ac4f-132">Stisknutím klávesy **F5** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-132">Press **F5** to run the app.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8ac4f-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ac4f-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="8ac4f-134">1\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-134">1\.</span></span> <span data-ttu-id="8ac4f-135">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="8ac4f-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="8ac4f-136">2\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-136">2\.</span></span> <span data-ttu-id="8ac4f-137">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="8ac4f-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="8ac4f-138">3\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-138">3\.</span></span> <span data-ttu-id="8ac4f-139">Pro Blazor prostředí na straně klienta spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-139">For a Blazor client-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazor -o WebApplication1
      ```

      <span data-ttu-id="8ac4f-140">Pro Blazor prostředí na straně serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-140">For a Blazor server-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorserverside -o WebApplication1
      ```

      <span data-ttu-id="8ac4f-141">Informace o dvou modelech hostování Blazor, na straně serveru a na straně klienta, najdete v <xref:blazor/hosting-models>tématu.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-141">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="8ac4f-142">4\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-142">4\.</span></span> <span data-ttu-id="8ac4f-143">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="8ac4f-144">5\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-144">5\.</span></span> <span data-ttu-id="8ac4f-145">Pro Blazor projekt na straně serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-145">For a Blazor server-side project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="8ac4f-146">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-146">Select **Yes**.</span></span>

   <span data-ttu-id="8ac4f-147">6\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-147">6\.</span></span> <span data-ttu-id="8ac4f-148">Pokud používáte aplikaci Blazor na straně serveru, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-148">If using a Blazor server-side app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="8ac4f-149">Pokud používáte aplikaci Blazor na straně klienta, spusťte `dotnet run` ji ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-149">If using a Blazor client-side app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="8ac4f-150">7\.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-150">7\.</span></span> <span data-ttu-id="8ac4f-151">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor server-side experience, select the **ASP.NET Core Blazor Server App** template. For a Blazor client-side experience, select the **ASP.NET Core Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8ac4f-152">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="8ac4f-152">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="8ac4f-153">Pro Blazor prostředí na straně klienta spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-153">For a Blazor client-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="8ac4f-154">Pro Blazor prostředí na straně serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-154">For a Blazor server-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorserverside -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="8ac4f-155">Informace o dvou modelech hostování Blazor, na straně serveru a na straně klienta, najdete v <xref:blazor/hosting-models>tématu.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-155">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="8ac4f-156">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-156">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="8ac4f-157">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-157">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="8ac4f-158">Domů</span><span class="sxs-lookup"><span data-stu-id="8ac4f-158">Home</span></span>
* <span data-ttu-id="8ac4f-159">Čítač</span><span class="sxs-lookup"><span data-stu-id="8ac4f-159">Counter</span></span>
* <span data-ttu-id="8ac4f-160">Načíst data</span><span class="sxs-lookup"><span data-stu-id="8ac4f-160">Fetch data</span></span>

<span data-ttu-id="8ac4f-161">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-161">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="8ac4f-162">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale komponenty Razor poskytují lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-162">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="8ac4f-163">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-163">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="8ac4f-164">Požadavek `/counter` na v prohlížeči, jak je uveden `@page` v direktivě nahoře, způsobí, že `Counter` komponenta vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-164">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="8ac4f-165">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-165">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="8ac4f-166">Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :</span><span class="sxs-lookup"><span data-stu-id="8ac4f-166">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="8ac4f-167">`onclick` Událost je aktivována.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-167">The `onclick` event is fired.</span></span>
* <span data-ttu-id="8ac4f-168">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-168">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="8ac4f-169">Dojde `currentCount` k zvýšení.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-169">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="8ac4f-170">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-170">The component is rendered again.</span></span>

<span data-ttu-id="8ac4f-171">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-171">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="8ac4f-172">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-172">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="8ac4f-173">Například přidejte `Counter` komponentu do domovské stránky aplikace `<Counter />` přidáním elementu do `Index` komponenty.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-173">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="8ac4f-174">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-174">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="8ac4f-175">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-175">Run the app.</span></span> <span data-ttu-id="8ac4f-176">Domovská stránka má vlastní počítadlo poskytované `Counter` komponentou.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-176">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="8ac4f-177">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-177">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="8ac4f-178">Chcete-li přidat parametr do `Counter` komponenty, aktualizujte `@code` blok komponenty:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-178">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="8ac4f-179">Přidejte vlastnost pro `IncrementAmount` `[Parameter]` s atributem.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-179">Add a property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="8ac4f-180">Změňte metodu na `IncrementAmount` použití`currentCount`při zvyšování hodnoty. `IncrementCount`</span><span class="sxs-lookup"><span data-stu-id="8ac4f-180">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="8ac4f-181">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-181">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="8ac4f-182">`IncrementAmount` Zadejte velementu`<Counter>`komponentypomocíatributu. `Index`</span><span class="sxs-lookup"><span data-stu-id="8ac4f-182">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="8ac4f-183">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="8ac4f-183">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="8ac4f-184">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-184">Run the app.</span></span> <span data-ttu-id="8ac4f-185">Komponenta má vlastní čítač, který se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další.** `Index`</span><span class="sxs-lookup"><span data-stu-id="8ac4f-185">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="8ac4f-186">Komponenta `Counter` ( `/counter` *Counter. Razor*) se stále zvyšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="8ac4f-186">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8ac4f-187">Další postup</span><span class="sxs-lookup"><span data-stu-id="8ac4f-187">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="8ac4f-188">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8ac4f-188">Additional resources</span></span>

* <xref:signalr/introduction>
