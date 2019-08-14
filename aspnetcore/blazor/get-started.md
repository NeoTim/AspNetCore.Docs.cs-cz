---
title: Začínáme s ASP.NET Core Blazor
author: guardrex
description: Začněte s Blazor vytvořením aplikace Blazor pomocí nástrojů podle vašeho výběru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/get-started
ms.openlocfilehash: 1358a2e92af9d9104e565718692b1ca1940b9d9e
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993404"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="5385a-103">Začínáme s ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="5385a-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="5385a-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5385a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5385a-105">Začínáme s Blazor:</span><span class="sxs-lookup"><span data-stu-id="5385a-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="5385a-106">Nainstalujte nejnovější verzi [sady .NET Core 3,0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="5385a-106">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="5385a-107">Nainstalujte šablony Blazor spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="5385a-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview8.19405.7
   ```

1. <span data-ttu-id="5385a-108">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="5385a-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5385a-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5385a-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="5385a-110">1\.</span><span class="sxs-lookup"><span data-stu-id="5385a-110">1\.</span></span> <span data-ttu-id="5385a-111">Nainstalujte si nejnovější verzi sady [Visual Studio Preview](https://visualstudio.com/vs/preview) s využitím úlohy **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="5385a-111">Install the latest [Visual Studio preview](https://visualstudio.com/vs/preview) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="5385a-112">2\.</span><span class="sxs-lookup"><span data-stu-id="5385a-112">2\.</span></span> <span data-ttu-id="5385a-113">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="5385a-113">Create a new project.</span></span>

   <span data-ttu-id="5385a-114">3\.</span><span class="sxs-lookup"><span data-stu-id="5385a-114">3\.</span></span> <span data-ttu-id="5385a-115">Vyberte **aplikaci Blazor**.</span><span class="sxs-lookup"><span data-stu-id="5385a-115">Select **Blazor App**.</span></span> <span data-ttu-id="5385a-116">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5385a-116">Select **Next**.</span></span>

   <span data-ttu-id="5385a-117">4\.</span><span class="sxs-lookup"><span data-stu-id="5385a-117">4\.</span></span> <span data-ttu-id="5385a-118">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="5385a-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="5385a-119">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="5385a-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="5385a-120">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5385a-120">Select **Create**.</span></span>

   <span data-ttu-id="5385a-121">5\.</span><span class="sxs-lookup"><span data-stu-id="5385a-121">5\.</span></span> <span data-ttu-id="5385a-122">Pro Blazor prostředí na straně klienta vyberte šablonu **aplikace Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="5385a-122">For a Blazor client-side experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="5385a-123">Pro Blazor prostředí na straně serveru vyberte šablonu **aplikace serveru Blazor** .</span><span class="sxs-lookup"><span data-stu-id="5385a-123">For a Blazor server-side experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="5385a-124">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5385a-124">Select **Create**.</span></span> <span data-ttu-id="5385a-125">Informace o dvou modelech hostování Blazor, na straně serveru a na straně klienta, najdete v <xref:blazor/hosting-models>tématu.</span><span class="sxs-lookup"><span data-stu-id="5385a-125">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="5385a-126">6\.</span><span class="sxs-lookup"><span data-stu-id="5385a-126">6\.</span></span> <span data-ttu-id="5385a-127">Stisknutím klávesy **F5** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5385a-127">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="5385a-128">Pokud jste nainstalovali rozšíření sady Visual Studio Blazor pro předchozí verzi Preview služby ASP.NET Core Blazor (Preview 6 nebo starší), můžete rozšíření odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="5385a-128">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="5385a-129">Instalace šablon Blazor do příkazového prostředí je teď dostačující pro povrchování šablon v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5385a-129">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5385a-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5385a-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="5385a-131">1\.</span><span class="sxs-lookup"><span data-stu-id="5385a-131">1\.</span></span> <span data-ttu-id="5385a-132">Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="5385a-132">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="5385a-133">2\.</span><span class="sxs-lookup"><span data-stu-id="5385a-133">2\.</span></span> <span data-ttu-id="5385a-134">Nainstalujte nejnovější verzi [ C# pro Visual Studio Code rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="5385a-134">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="5385a-135">3\.</span><span class="sxs-lookup"><span data-stu-id="5385a-135">3\.</span></span> <span data-ttu-id="5385a-136">Pro Blazor prostředí na straně klienta spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5385a-136">For a Blazor client-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="5385a-137">Pro Blazor prostředí na straně serveru spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5385a-137">For a Blazor server-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="5385a-138">Informace o dvou modelech hostování Blazor, na straně serveru a na straně klienta, najdete v <xref:blazor/hosting-models>tématu.</span><span class="sxs-lookup"><span data-stu-id="5385a-138">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="5385a-139">4\.</span><span class="sxs-lookup"><span data-stu-id="5385a-139">4\.</span></span> <span data-ttu-id="5385a-140">Otevřete složku *WebApplication1* v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5385a-140">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="5385a-141">5\.</span><span class="sxs-lookup"><span data-stu-id="5385a-141">5\.</span></span> <span data-ttu-id="5385a-142">Pro Blazor projekt na straně serveru rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="5385a-142">For a Blazor server-side project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="5385a-143">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="5385a-143">Select **Yes**.</span></span>

   <span data-ttu-id="5385a-144">6\.</span><span class="sxs-lookup"><span data-stu-id="5385a-144">6\.</span></span> <span data-ttu-id="5385a-145">Pokud používáte aplikaci Blazor na straně serveru, spusťte aplikaci pomocí ladicího programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5385a-145">If using a Blazor server-side app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="5385a-146">Pokud používáte aplikaci Blazor na straně klienta, spusťte `dotnet run` ji ze složky projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="5385a-146">If using a Blazor client-side app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="5385a-147">7\.</span><span class="sxs-lookup"><span data-stu-id="5385a-147">7\.</span></span> <span data-ttu-id="5385a-148">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5385a-148">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor server-side experience, select the **Blazor Server App** template. For a Blazor client-side experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5385a-149">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="5385a-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="5385a-150">Pro Blazor prostředí na straně klienta spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5385a-150">For a Blazor client-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="5385a-151">Pro Blazor prostředí na straně serveru spusťte v příkazovém prostředí následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5385a-151">For a Blazor server-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="5385a-152">Informace o dvou modelech hostování Blazor, na straně serveru a na straně klienta, najdete v <xref:blazor/hosting-models>tématu.</span><span class="sxs-lookup"><span data-stu-id="5385a-152">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="5385a-153">V prohlížeči přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5385a-153">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="5385a-154">Na kartách na bočním panelu je k dispozici více stránek:</span><span class="sxs-lookup"><span data-stu-id="5385a-154">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="5385a-155">Domů</span><span class="sxs-lookup"><span data-stu-id="5385a-155">Home</span></span>
* <span data-ttu-id="5385a-156">Čítač</span><span class="sxs-lookup"><span data-stu-id="5385a-156">Counter</span></span>
* <span data-ttu-id="5385a-157">Načíst data</span><span class="sxs-lookup"><span data-stu-id="5385a-157">Fetch data</span></span>

<span data-ttu-id="5385a-158">Na stránce čítač můžete **kliknutím** na tlačítko pro zvýšit hodnotu čítače bez aktualizace stránky.</span><span class="sxs-lookup"><span data-stu-id="5385a-158">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="5385a-159">Zvýšení čítače na webové stránce obvykle vyžaduje psaní JavaScriptu, ale komponenty Razor poskytují lepší přístup pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="5385a-159">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="5385a-160">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="5385a-160">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="5385a-161">Požadavek `/counter` na v prohlížeči, jak je uveden `@page` v direktivě nahoře, způsobí, že `Counter` komponenta vykreslí svůj obsah.</span><span class="sxs-lookup"><span data-stu-id="5385a-161">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="5385a-162">Komponenty vykreslí do reprezentace v paměti stromu vykreslování, který lze poté použít k flexibilnímu a efektivnímu způsobu aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5385a-162">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="5385a-163">Pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další** :</span><span class="sxs-lookup"><span data-stu-id="5385a-163">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="5385a-164">`onclick` Událost je aktivována.</span><span class="sxs-lookup"><span data-stu-id="5385a-164">The `onclick` event is fired.</span></span>
* <span data-ttu-id="5385a-165">`IncrementCount` Metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="5385a-165">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="5385a-166">Dojde `currentCount` k zvýšení.</span><span class="sxs-lookup"><span data-stu-id="5385a-166">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="5385a-167">Komponenta se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="5385a-167">The component is rendered again.</span></span>

<span data-ttu-id="5385a-168">Modul runtime porovná nový obsah s předchozím obsahem a na model DOM (Document Object Model) (DOM) použije pouze změněný obsah.</span><span class="sxs-lookup"><span data-stu-id="5385a-168">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="5385a-169">Přidejte komponentu do jiné komponenty pomocí syntaxe jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="5385a-169">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="5385a-170">Například přidejte `Counter` komponentu do domovské stránky aplikace `<Counter />` přidáním elementu do `Index` komponenty.</span><span class="sxs-lookup"><span data-stu-id="5385a-170">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="5385a-171">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="5385a-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="5385a-172">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5385a-172">Run the app.</span></span> <span data-ttu-id="5385a-173">Domovská stránka má vlastní počítadlo poskytované `Counter` komponentou.</span><span class="sxs-lookup"><span data-stu-id="5385a-173">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="5385a-174">Parametry komponenty jsou zadány pomocí atributů nebo [podřízeného obsahu](xref:blazor/components#child-content), který umožňuje nastavit vlastnosti pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="5385a-174">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="5385a-175">Chcete-li přidat parametr do `Counter` komponenty, aktualizujte `@code` blok komponenty:</span><span class="sxs-lookup"><span data-stu-id="5385a-175">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="5385a-176">Přidejte veřejnou vlastnost pro `IncrementAmount` `[Parameter]` s atributem.</span><span class="sxs-lookup"><span data-stu-id="5385a-176">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="5385a-177">Změňte metodu na `IncrementAmount` použití`currentCount`při zvyšování hodnoty. `IncrementCount`</span><span class="sxs-lookup"><span data-stu-id="5385a-177">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="5385a-178">*Stránky/čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="5385a-178">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="5385a-179">`IncrementAmount` Zadejte velementu`<Counter>`komponentypomocíatributu. `Index`</span><span class="sxs-lookup"><span data-stu-id="5385a-179">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="5385a-180">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="5385a-180">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="5385a-181">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5385a-181">Run the app.</span></span> <span data-ttu-id="5385a-182">Komponenta má vlastní čítač, který se zvýší o deset pokaždé, když je vybráno tlačítko pro **kliknutí na tlačítko Další.** `Index`</span><span class="sxs-lookup"><span data-stu-id="5385a-182">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="5385a-183">Komponenta `Counter` ( `/counter` *Counter. Razor*) se stále zvyšuje o jednu.</span><span class="sxs-lookup"><span data-stu-id="5385a-183">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5385a-184">Další postup</span><span class="sxs-lookup"><span data-stu-id="5385a-184">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="5385a-185">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5385a-185">Additional resources</span></span>

* <xref:signalr/introduction>
