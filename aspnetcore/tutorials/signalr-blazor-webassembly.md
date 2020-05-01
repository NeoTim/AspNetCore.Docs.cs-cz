---
title: Použití ASP.NET Core SignalR s Blazor WebAssembly
author: guardrex
description: Vytvořte aplikaci Chat, která používá ASP.NET Core SignalR s Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: e86047c9a830cd3ea906d0798f61d90eaee5a18c
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604828"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="5d279-103">Použití ASP.NET Coreového signálu s Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5d279-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="5d279-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5d279-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="5d279-105">V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje Signaler s Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="5d279-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="5d279-106">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="5d279-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5d279-107">Vytvoření projektu hostované aplikace Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5d279-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="5d279-108">Přidat klientskou knihovnu signálů</span><span class="sxs-lookup"><span data-stu-id="5d279-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="5d279-109">Přidat centrum signálů</span><span class="sxs-lookup"><span data-stu-id="5d279-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="5d279-110">Přidat služby signalizace a koncový bod pro centrum signalizace</span><span class="sxs-lookup"><span data-stu-id="5d279-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="5d279-111">Přidat kód komponenty Razor pro chat</span><span class="sxs-lookup"><span data-stu-id="5d279-111">Add Razor component code for chat</span></span>

<span data-ttu-id="5d279-112">Na konci tohoto kurzu budete mít funkční chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5d279-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="5d279-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5d279-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5d279-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="5d279-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d279-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d279-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d279-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d279-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5d279-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5d279-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="5d279-118">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="5d279-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="5d279-119">Vytvoření hostovaného projektu aplikace Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5d279-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="5d279-120">Pokud nepoužíváte Visual Studio verze 16,6 Preview 2 nebo novější, nainstalujte šablonu [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) .</span><span class="sxs-lookup"><span data-stu-id="5d279-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="5d279-121">Balíček [Microsoft. AspNetCore. Components. WebAssembly. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) obsahuje verzi Preview, zatímco Blazor WebAssembly je ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="5d279-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="5d279-122">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5d279-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

<span data-ttu-id="5d279-123">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="5d279-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d279-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d279-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5d279-125">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="5d279-125">Create a new project.</span></span>

1. <span data-ttu-id="5d279-126">Vyberte **aplikace Blazor** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5d279-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="5d279-127">Do pole **název projektu** zadejte "BlazorSignalRApp".</span><span class="sxs-lookup"><span data-stu-id="5d279-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="5d279-128">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="5d279-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="5d279-129">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5d279-129">Select **Create**.</span></span>

1. <span data-ttu-id="5d279-130">Vyberte šablonu **aplikace WebAssembly pro Blazor** .</span><span class="sxs-lookup"><span data-stu-id="5d279-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="5d279-131">V části **Upřesnit**zaškrtněte políčko **ASP.NET Core hostované** .</span><span class="sxs-lookup"><span data-stu-id="5d279-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="5d279-132">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5d279-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="5d279-133">Pokud jste provedli upgrade nebo instalaci nové verze sady Visual Studio a šablona WebAssembly Blazor se nezobrazí v uživatelském rozhraní VS, přeinstalujte ji pomocí `dotnet new` příkazu uvedeného výše.</span><span class="sxs-lookup"><span data-stu-id="5d279-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d279-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d279-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5d279-135">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5d279-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="5d279-136">V Visual Studio Code otevřete složku projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="5d279-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="5d279-137">Když se zobrazí dialogové okno s přidáním assetů pro sestavení a ladění aplikace, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="5d279-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="5d279-138">Visual Studio Code automaticky přidá složku *. VSCode* pomocí vygenerovaných souborů *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5d279-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5d279-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5d279-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5d279-140">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5d279-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="5d279-141">V Visual Studio pro Mac otevřete projekt tak, že přejdete do složky projektu a otevřete soubor řešení projektu (*. sln*).</span><span class="sxs-lookup"><span data-stu-id="5d279-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5d279-142">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="5d279-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="5d279-143">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5d279-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="5d279-144">Přidat klientskou knihovnu signálů</span><span class="sxs-lookup"><span data-stu-id="5d279-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d279-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d279-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="5d279-146">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt **BlazorSignalRApp. Client** a vyberte možnost **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="5d279-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="5d279-147">V dialogovém okně **Spravovat balíčky NuGet** ověřte, že je **zdroj balíčku** nastavený na *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="5d279-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="5d279-148">Když je vybrána možnost **Procházet** , do vyhledávacího pole zadejte "Microsoft. AspNetCore. signaler. Client".</span><span class="sxs-lookup"><span data-stu-id="5d279-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="5d279-149">Ve výsledcích hledání vyberte `Microsoft.AspNetCore.SignalR.Client` balíček a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="5d279-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="5d279-150">Pokud se zobrazí dialogové okno **Náhled změn** , vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="5d279-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="5d279-151">Pokud se zobrazí dialogové okno pro **přijetí licence** , **Vyberte možnost Souhlasím** , pokud souhlasíte s licenčními podmínkami.</span><span class="sxs-lookup"><span data-stu-id="5d279-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d279-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d279-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="5d279-153">V **integrovaném terminálu** (**zobrazení** > **terminálu** z panelu nástrojů) spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5d279-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5d279-154">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5d279-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5d279-155">Na bočním panelu **řešení** klikněte pravým tlačítkem myši na projekt **BlazorSignalRApp. Client** a vyberte možnost **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="5d279-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="5d279-156">V dialogovém okně **Spravovat balíčky NuGet** potvrďte, že je rozevírací seznam zdroj nastavený na *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="5d279-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="5d279-157">Když je vybrána možnost **Procházet** , do vyhledávacího pole zadejte "Microsoft. AspNetCore. signaler. Client".</span><span class="sxs-lookup"><span data-stu-id="5d279-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="5d279-158">Ve výsledcích hledání zaškrtněte políčko vedle `Microsoft.AspNetCore.SignalR.Client` balíčku a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="5d279-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="5d279-159">Pokud se zobrazí dialogové okno pro **přijetí licence** , vyberte **přijmout** , pokud souhlasíte s licenčními podmínkami.</span><span class="sxs-lookup"><span data-stu-id="5d279-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5d279-160">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="5d279-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="5d279-161">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5d279-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="5d279-162">Přidat centrum signálů</span><span class="sxs-lookup"><span data-stu-id="5d279-162">Add a SignalR hub</span></span>

<span data-ttu-id="5d279-163">V projektu **BlazorSignalRApp. Server** vytvořte složku *Centers* (plural) a přidejte následující `ChatHub` třídu (*Centers/ChatHub. cs*):</span><span class="sxs-lookup"><span data-stu-id="5d279-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="5d279-164">Přidání služeb a koncového bodu pro centrum signalizace</span><span class="sxs-lookup"><span data-stu-id="5d279-164">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="5d279-165">V projektu **BlazorSignalRApp. Server** otevřete soubor *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="5d279-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="5d279-166">Přidejte do horní části souboru `ChatHub` obor názvů pro třídu:</span><span class="sxs-lookup"><span data-stu-id="5d279-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="5d279-167">Přidejte služby middlewaru a služby pro kompresi odpovědí `Startup.ConfigureServices`do:</span><span class="sxs-lookup"><span data-stu-id="5d279-167">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="5d279-168">Do `Startup.Configure` mezi koncovými body řadičů a záložním serverem na straně klienta přidejte koncový bod pro centrum:</span><span class="sxs-lookup"><span data-stu-id="5d279-168">In `Startup.Configure` between the endpoints for controllers and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="5d279-169">Přidat kód komponenty Razor pro chat</span><span class="sxs-lookup"><span data-stu-id="5d279-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="5d279-170">V projektu **BlazorSignalRApp. Client** otevřete soubor *pages/index. Razor* .</span><span class="sxs-lookup"><span data-stu-id="5d279-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="5d279-171">Nahraďte kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5d279-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="5d279-172">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="5d279-172">Run the app</span></span>

1. <span data-ttu-id="5d279-173">Postupujte podle pokynů pro vaše nástroje:</span><span class="sxs-lookup"><span data-stu-id="5d279-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d279-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d279-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5d279-175">V **Průzkumník řešení**vyberte projekt **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="5d279-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="5d279-176">Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd>+<kbd>F5</kbd> pro spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="5d279-176">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5d279-177">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="5d279-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5d279-178">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="5d279-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="5d279-179">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="5d279-179">The name and message are displayed on both pages instantly:</span></span>

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="5d279-181">Quotes: *Star Trek VI:* &copy;nezjištěná země 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country) – nezjištěná</span><span class="sxs-lookup"><span data-stu-id="5d279-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d279-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d279-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5d279-183">Když VS Code nabídky vytvoří profil spuštění pro serverovou aplikaci (*. VSCode/Launch. JSON*), zobrazí se `program` položka podobně jako v následujícím příkladu, aby odkazovala na sestavení aplikace (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="5d279-183">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="5d279-184">Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd>+<kbd>F5</kbd> pro spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="5d279-184">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5d279-185">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="5d279-185">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5d279-186">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="5d279-186">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="5d279-187">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="5d279-187">The name and message are displayed on both pages instantly:</span></span>

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="5d279-189">Quotes: *Star Trek VI:* &copy;nezjištěná země 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country) – nezjištěná</span><span class="sxs-lookup"><span data-stu-id="5d279-189">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5d279-190">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5d279-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5d279-191">Na bočním panelu **řešení** vyberte projekt **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="5d279-191">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="5d279-192">Stiskněte <kbd>⌘</kbd>+<kbd>↩</kbd>\* \* ke spuštění aplikace s laděním nebo <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> ke spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="5d279-192">Press <kbd>⌘</kbd>+<kbd>↩</kbd>\*\* to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5d279-193">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="5d279-193">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5d279-194">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="5d279-194">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="5d279-195">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="5d279-195">The name and message are displayed on both pages instantly:</span></span>

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="5d279-197">Quotes: *Star Trek VI:* &copy;nezjištěná země 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country) – nezjištěná</span><span class="sxs-lookup"><span data-stu-id="5d279-197">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5d279-198">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="5d279-198">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="5d279-199">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5d279-199">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="5d279-200">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="5d279-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5d279-201">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="5d279-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="5d279-202">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="5d279-202">The name and message are displayed on both pages instantly:</span></span>

   ![Ukázková aplikace pro WebAssembly Blazor, která je otevřená ve dvou oknech prohlížeče, zobrazuje vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="5d279-204">Quotes: *Star Trek VI:* &copy;nezjištěná země 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country) – nezjištěná</span><span class="sxs-lookup"><span data-stu-id="5d279-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="5d279-205">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5d279-205">Next steps</span></span>

<span data-ttu-id="5d279-206">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="5d279-206">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5d279-207">Blazor Vytvoření projektu hostované aplikace WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5d279-207">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="5d279-208">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="5d279-208">Add the SignalR client library</span></span>
> * <span data-ttu-id="5d279-209">Přidání SignalR centra</span><span class="sxs-lookup"><span data-stu-id="5d279-209">Add a SignalR hub</span></span>
> * <span data-ttu-id="5d279-210">Přidání SignalR služeb a koncového bodu pro SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="5d279-210">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="5d279-211">Přidat kód komponenty Razor pro chat</span><span class="sxs-lookup"><span data-stu-id="5d279-211">Add Razor component code for chat</span></span>

<span data-ttu-id="5d279-212">Další informace o vytváření Blazor aplikací najdete v Blazor dokumentaci:</span><span class="sxs-lookup"><span data-stu-id="5d279-212">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="5d279-213">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="5d279-213">Additional resources</span></span>

* <xref:signalr/introduction>
