---
title: Použití ASP.NET SignalR Blazor jádra s webovou sestavou
author: guardrex
description: Vytvořte chatovací aplikaci, která používá ASP.NET Core SignalR s Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 798068c83e16070d3279c88c44af0cd96d182fe2
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488881"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="3e4b8-103">Použití ASP.NET core signalr s Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3e4b8-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="3e4b8-104">[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3e4b8-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="3e4b8-105">Tento kurz učí základy vytváření aplikace v reálném čase pomocí SignalR s Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="3e4b8-106">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3e4b8-107">Vytvoření projektu aplikace Hostované na webových shromažďovacích sestaveních Blazor</span><span class="sxs-lookup"><span data-stu-id="3e4b8-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="3e4b8-108">Přidání klientské knihovny SignalR</span><span class="sxs-lookup"><span data-stu-id="3e4b8-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="3e4b8-109">Přidání rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="3e4b8-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="3e4b8-110">Přidání služeb SignalR a koncového bodu pro rozbočovač SignalR</span><span class="sxs-lookup"><span data-stu-id="3e4b8-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="3e4b8-111">Přidat kód komponenty Razor pro chat</span><span class="sxs-lookup"><span data-stu-id="3e4b8-111">Add Razor component code for chat</span></span>

<span data-ttu-id="3e4b8-112">Na konci tohoto kurzu budete mít pracovní chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="3e4b8-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="3e4b8-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3e4b8-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3e4b8-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e4b8-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e4b8-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e4b8-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e4b8-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e4b8-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3e4b8-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="3e4b8-118">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3e4b8-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="3e4b8-119">Vytvoření hostovaného projektu aplikace Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3e4b8-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="3e4b8-120">Pokud nepoužíváte Visual Studio verze 16.6 Preview 2 nebo novější, nainstalujte šablonu [Blazor WebAssembly.](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="3e4b8-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="3e4b8-121">Balíček [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) má verzi preview, zatímco Blazor WebAssembly je ve verzi preview.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="3e4b8-122">V příkazovém prostředí proveďte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
```

<span data-ttu-id="3e4b8-123">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e4b8-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e4b8-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3e4b8-125">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="3e4b8-125">Create a new project.</span></span>

1. <span data-ttu-id="3e4b8-126">Vyberte **Blazor App** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="3e4b8-127">Do pole **Název projektu** zadejte "BlazorSignalRApp".</span><span class="sxs-lookup"><span data-stu-id="3e4b8-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="3e4b8-128">Potvrďte, že položka **Umístění** je správná, nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3e4b8-129">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-129">Select **Create**.</span></span>

1. <span data-ttu-id="3e4b8-130">Zvolte šablonu **aplikace Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="3e4b8-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="3e4b8-131">V části **Upřesnit**zaškrtněte **políčko ASP.NET Jádro hostované.**</span><span class="sxs-lookup"><span data-stu-id="3e4b8-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="3e4b8-132">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="3e4b8-133">Pokud jste upgradovali nebo nainstalovali novou verzi sady Visual Studio a šablona Blazor WebAssembly se v `dotnet new` uživatelském jazyce VS nezobrazí, přeinstalujte šablonu pomocí dříve zobrazeného příkazu.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e4b8-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e4b8-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3e4b8-135">V příkazovém prostředí proveďte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="3e4b8-136">V kódu Visual Studia otevřete složku projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="3e4b8-137">Když se zobrazí dialogové okno pro přidání datových zdrojů k sestavení a ladění aplikace, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="3e4b8-138">Visual Studio Code automaticky přidá složku *.vscode* s generovanými soubory *launch.json* a *tasks.json.*</span><span class="sxs-lookup"><span data-stu-id="3e4b8-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e4b8-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3e4b8-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3e4b8-140">V příkazovém prostředí proveďte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="3e4b8-141">V sadě Visual Studio for Mac otevřete projekt tak, že přejdete do složky projektu a otevřete soubor řešení projektu (*.sln*).</span><span class="sxs-lookup"><span data-stu-id="3e4b8-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3e4b8-142">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3e4b8-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3e4b8-143">V příkazovém prostředí proveďte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="3e4b8-144">Přidání klientské knihovny SignalR</span><span class="sxs-lookup"><span data-stu-id="3e4b8-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e4b8-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e4b8-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="3e4b8-146">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **BlazorSignalRApp.Client** a vyberte **příkaz Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3e4b8-147">V dialogovém okně **Spravovat balíčky NuGet** zkontrolujte, zda je **zdroj balíčku** nastaven na *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="3e4b8-148">S **vybranou možností Procházet** zadejte do vyhledávacího pole "Microsoft.AspNetCore.SignalR.Client".</span><span class="sxs-lookup"><span data-stu-id="3e4b8-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="3e4b8-149">Ve výsledcích hledání `Microsoft.AspNetCore.SignalR.Client` vyberte balíček a vyberte **Instalovat**.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="3e4b8-150">Pokud se zobrazí dialogové okno **Náhled změn,** vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="3e4b8-151">Pokud se zobrazí dialogové okno **Přijetí licence,** vyberte **Souhlasím,** pokud souhlasíte s licenčními podmínkami.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e4b8-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e4b8-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="3e4b8-153">V **integrovaném terminálu** (**Zobrazit** > **terminál** z panelu nástrojů) proveďte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e4b8-154">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3e4b8-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3e4b8-155">V postranním panelu **Řešení** klepněte pravým tlačítkem myši na projekt **BlazorSignalRApp.Client** a vyberte **spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3e4b8-156">V dialogovém okně **Spravovat balíčky NuGet** zkontrolujte, zda je zdrojový rozevírací seznam nastaven na *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="3e4b8-157">S **vybranou možností Procházet** zadejte do vyhledávacího pole "Microsoft.AspNetCore.SignalR.Client".</span><span class="sxs-lookup"><span data-stu-id="3e4b8-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="3e4b8-158">Ve výsledcích hledání zaškrtněte políčko `Microsoft.AspNetCore.SignalR.Client` vedle balíčku a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="3e4b8-159">Pokud se zobrazí dialogové okno **Přijetí licence,** vyberte **Přijmout,** pokud s licenčními podmínkami souhlasíte.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3e4b8-160">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3e4b8-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3e4b8-161">V příkazovém prostředí proveďte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="3e4b8-162">Přidání rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="3e4b8-162">Add a SignalR hub</span></span>

<span data-ttu-id="3e4b8-163">V projektu **BlazorSignalRApp.Server** vytvořte složku *Hubs* (plural) `ChatHub` a přidejte následující třídu (*Hubs/ChatHub.cs*):</span><span class="sxs-lookup"><span data-stu-id="3e4b8-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="3e4b8-164">Přidání služeb SignalR a koncového bodu pro rozbočovač SignalR</span><span class="sxs-lookup"><span data-stu-id="3e4b8-164">Add SignalR services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="3e4b8-165">V projektu **BlazorSignalRApp.Server** otevřete soubor *Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="3e4b8-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="3e4b8-166">Přidejte obor názvů `ChatHub` pro třídu na začátek souboru:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="3e4b8-167">Přidejte služby `Startup.ConfigureServices`SignalR do :</span><span class="sxs-lookup"><span data-stu-id="3e4b8-167">Add the SignalR services to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddSignalR();
   ```

1. <span data-ttu-id="3e4b8-168">Mezi `Startup.Configure` koncové body pro výchozí trasu řadiče a záložní straně klienta přidejte koncový bod pro rozbočovač:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-168">In `Startup.Configure` between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="3e4b8-169">Přidat kód komponenty Razor pro chat</span><span class="sxs-lookup"><span data-stu-id="3e4b8-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="3e4b8-170">V projektu **BlazorSignalRApp.Client** otevřete soubor *Pages/Index.razor.*</span><span class="sxs-lookup"><span data-stu-id="3e4b8-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="3e4b8-171">Nahraďte značku následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="3e4b8-172">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="3e4b8-172">Run the app</span></span>

1. <span data-ttu-id="3e4b8-173">Postupujte podle pokynů pro vaše nástroje:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e4b8-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e4b8-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3e4b8-175">V **Průzkumníku řešení**vyberte projekt **BlazorSignalRApp.Server.**</span><span class="sxs-lookup"><span data-stu-id="3e4b8-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="3e4b8-176">Stisknutím **kombinace kláves Ctrl+F5** spusťte aplikaci bez ladění.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-176">Press **Ctrl+F5** to run the app without debugging.</span></span>

1. <span data-ttu-id="3e4b8-177">Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3e4b8-178">Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat.**</span><span class="sxs-lookup"><span data-stu-id="3e4b8-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="3e4b8-179">Jméno a zpráva se zobrazí na obou stránkách okamžitě:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-179">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly ukázková aplikace otevřena ve dvou oknech prohlížeče zobrazujících vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="3e4b8-181">Citáty: *Star Trek VI: Neobjevená země* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3e4b8-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e4b8-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e4b8-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3e4b8-183">Na panelu nástrojů vyberte **Možnost Ladění** > **spustit bez ladění.**</span><span class="sxs-lookup"><span data-stu-id="3e4b8-183">Select **Debug** > **Run Without Debugging** from the toolbar.</span></span>

1. <span data-ttu-id="3e4b8-184">Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-184">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3e4b8-185">Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat.**</span><span class="sxs-lookup"><span data-stu-id="3e4b8-185">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="3e4b8-186">Jméno a zpráva se zobrazí na obou stránkách okamžitě:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-186">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly ukázková aplikace otevřena ve dvou oknech prohlížeče zobrazujících vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="3e4b8-188">Citáty: *Star Trek VI: Neobjevená země* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3e4b8-188">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e4b8-189">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3e4b8-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3e4b8-190">V postranním panelu **Řešení** vyberte projekt **BlazorSignalRApp.Server.**</span><span class="sxs-lookup"><span data-stu-id="3e4b8-190">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="3e4b8-191">V nabídce vyberte **Spustit** > **start bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-191">From the menu, select **Run** > **Start Without Debugging**.</span></span>

1. <span data-ttu-id="3e4b8-192">Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3e4b8-193">Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat.**</span><span class="sxs-lookup"><span data-stu-id="3e4b8-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="3e4b8-194">Jméno a zpráva se zobrazí na obou stránkách okamžitě:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-194">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly ukázková aplikace otevřena ve dvou oknech prohlížeče zobrazujících vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="3e4b8-196">Citáty: *Star Trek VI: Neobjevená země* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3e4b8-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3e4b8-197">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3e4b8-197">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="3e4b8-198">V příkazovém prostředí proveďte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-198">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="3e4b8-199">Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="3e4b8-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3e4b8-200">Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat.**</span><span class="sxs-lookup"><span data-stu-id="3e4b8-200">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="3e4b8-201">Jméno a zpráva se zobrazí na obou stránkách okamžitě:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-201">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly ukázková aplikace otevřena ve dvou oknech prohlížeče zobrazujících vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="3e4b8-203">Citáty: *Star Trek VI: Neobjevená země* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3e4b8-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="3e4b8-204">Další kroky</span><span class="sxs-lookup"><span data-stu-id="3e4b8-204">Next steps</span></span>

<span data-ttu-id="3e4b8-205">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-205">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3e4b8-206">Vytvoření Blazor projektu hostované webové sestavy</span><span class="sxs-lookup"><span data-stu-id="3e4b8-206">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="3e4b8-207">Přidání SignalR klientské knihovny</span><span class="sxs-lookup"><span data-stu-id="3e4b8-207">Add the SignalR client library</span></span>
> * <span data-ttu-id="3e4b8-208">Přidání SignalR rozbočovače</span><span class="sxs-lookup"><span data-stu-id="3e4b8-208">Add a SignalR hub</span></span>
> * <span data-ttu-id="3e4b8-209">Přidání SignalR služeb a koncového SignalR bodu pro rozbočovač</span><span class="sxs-lookup"><span data-stu-id="3e4b8-209">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="3e4b8-210">Přidat kód komponenty Razor pro chat</span><span class="sxs-lookup"><span data-stu-id="3e4b8-210">Add Razor component code for chat</span></span>

<span data-ttu-id="3e4b8-211">Další informace o Blazor vytváření aplikací Blazor najdete v dokumentaci:</span><span class="sxs-lookup"><span data-stu-id="3e4b8-211">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="3e4b8-212">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3e4b8-212">Additional resources</span></span>

* <xref:signalr/introduction>
