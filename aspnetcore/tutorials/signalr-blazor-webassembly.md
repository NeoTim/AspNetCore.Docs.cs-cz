---
title: Použít ASP.NET Core SignalR sBlazor WebAssembly
author: guardrex
description: Vytvořte aplikaci Chat, která používá ASP.NET Core SignalR s Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 5a58e7ae28842e2e8a0f3bae8f47e252839903fe
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408874"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="3a010-103">Použít ASP.NET Core SignalR sBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3a010-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="3a010-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3a010-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3a010-105">V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje SignalR Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="3a010-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="3a010-106">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="3a010-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3a010-107">Vytvoření Blazor WebAssembly projektu hostované aplikace</span><span class="sxs-lookup"><span data-stu-id="3a010-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="3a010-108">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="3a010-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="3a010-109">Přidání SignalR centra</span><span class="sxs-lookup"><span data-stu-id="3a010-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="3a010-110">Přidání SignalR služeb a koncového bodu pro SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="3a010-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="3a010-111">Přidat Razor kód komponenty pro chat</span><span class="sxs-lookup"><span data-stu-id="3a010-111">Add Razor component code for chat</span></span>

<span data-ttu-id="3a010-112">Na konci tohoto kurzu budete mít funkční chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3a010-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="3a010-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a010-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3a010-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3a010-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3a010-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a010-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3a010-116">[Visual Studio 2019 16,6 nebo novější](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="3a010-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3a010-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3a010-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3a010-118">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3a010-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="3a010-119">Visual Studio pro Mac verze 8,6 nebo novější</span><span class="sxs-lookup"><span data-stu-id="3a010-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="3a010-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3a010-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="3a010-121">Vytvoření projektu hostované Blazor WebAssembly aplikace</span><span class="sxs-lookup"><span data-stu-id="3a010-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="3a010-122">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="3a010-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3a010-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a010-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="3a010-124">Vyžaduje se Visual Studio 16,6 nebo novější a .NET Core SDK 3.1.300 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="3a010-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="3a010-125">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="3a010-125">Create a new project.</span></span>

1. <span data-ttu-id="3a010-126">Vyberte \*\* Blazor aplikace\*\* a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3a010-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="3a010-127">`BlazorSignalRApp`Do pole **název projektu** zadejte.</span><span class="sxs-lookup"><span data-stu-id="3a010-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="3a010-128">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="3a010-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3a010-129">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3a010-129">Select **Create**.</span></span>

1. <span data-ttu-id="3a010-130">Vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="3a010-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="3a010-131">V části **Upřesnit**zaškrtněte políčko **ASP.NET Core hostované** .</span><span class="sxs-lookup"><span data-stu-id="3a010-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="3a010-132">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3a010-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3a010-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3a010-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3a010-134">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3a010-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="3a010-135">V Visual Studio Code otevřete složku projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="3a010-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="3a010-136">Když se zobrazí dialogové okno s přidáním assetů pro sestavení a ladění aplikace, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="3a010-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="3a010-137">Visual Studio Code automaticky přidá `.vscode` složku s generovanými `launch.json` `tasks.json` soubory a.</span><span class="sxs-lookup"><span data-stu-id="3a010-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3a010-138">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3a010-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3a010-139">Nainstalujte nejnovější verzi [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) a proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="3a010-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="3a010-140">Vyberte **soubor**  >  **nové řešení** nebo vytvořte **Nový** projekt z **okna Start**.</span><span class="sxs-lookup"><span data-stu-id="3a010-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="3a010-141">Na bočním panelu vyberte **Webová a konzolová**  >  **aplikace**.</span><span class="sxs-lookup"><span data-stu-id="3a010-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="3a010-142">Vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="3a010-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3a010-143">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3a010-143">Select **Next**.</span></span>

   <span data-ttu-id="3a010-144">Potvrďte následující konfigurace:</span><span class="sxs-lookup"><span data-stu-id="3a010-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="3a010-145">**Cílová architektura** je nastavená na **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="3a010-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="3a010-146">**Ověřování** nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="3a010-146">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="3a010-147">Zaškrtněte políčko **ASP.NET Core hostované** .</span><span class="sxs-lookup"><span data-stu-id="3a010-147">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="3a010-148">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3a010-148">Select **Next**.</span></span>

1. <span data-ttu-id="3a010-149">Do pole **název projektu** název aplikace `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="3a010-149">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="3a010-150">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3a010-150">Select **Create**.</span></span>

   <span data-ttu-id="3a010-151">Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="3a010-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="3a010-152">Certifikát uživatele a řetězce klíčů je nutný k důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="3a010-152">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="3a010-153">Otevřete projekt tak, že přejdete do složky projektu a otevřete soubor řešení projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="3a010-153">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3a010-154">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3a010-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3a010-155">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3a010-155">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="3a010-156">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="3a010-156">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3a010-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a010-157">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="3a010-158">V **Průzkumník řešení**klikněte pravým tlačítkem na `BlazorSignalRApp.Client` projekt a vyberte **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3a010-158">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3a010-159">V dialogovém okně **Spravovat balíčky NuGet** potvrďte, že je **zdroj balíčku** nastavený na `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="3a010-159">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3a010-160">Když je vybraná možnost **Procházet** , `Microsoft.AspNetCore.SignalR.Client` do vyhledávacího pole zadejte.</span><span class="sxs-lookup"><span data-stu-id="3a010-160">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="3a010-161">Ve výsledcích hledání vyberte [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) balíček a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="3a010-161">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="3a010-162">Pokud se zobrazí dialogové okno **Náhled změn** , vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="3a010-162">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="3a010-163">Pokud se zobrazí dialogové okno pro **přijetí licence** , **Vyberte možnost Souhlasím** , pokud souhlasíte s licenčními podmínkami.</span><span class="sxs-lookup"><span data-stu-id="3a010-163">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3a010-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3a010-164">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="3a010-165">V **integrovaném terminálu** (**zobrazení**  >  **terminálu** z panelu nástrojů) spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3a010-165">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3a010-166">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3a010-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3a010-167">Na bočním panelu **řešení** klikněte pravým tlačítkem na `BlazorSignalRApp.Client` projekt a vyberte **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3a010-167">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3a010-168">V dialogovém okně **Spravovat balíčky NuGet** potvrďte, že rozevírací seznam zdroj je nastavený na `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="3a010-168">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3a010-169">Když je vybraná možnost **Procházet** , `Microsoft.AspNetCore.SignalR.Client` do vyhledávacího pole zadejte.</span><span class="sxs-lookup"><span data-stu-id="3a010-169">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="3a010-170">Ve výsledcích hledání zaškrtněte políčko vedle [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) balíčku a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="3a010-170">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="3a010-171">Pokud se zobrazí dialogové okno pro **přijetí licence** , vyberte **přijmout** , pokud souhlasíte s licenčními podmínkami.</span><span class="sxs-lookup"><span data-stu-id="3a010-171">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3a010-172">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3a010-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3a010-173">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3a010-173">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="3a010-174">Přidání SignalR centra</span><span class="sxs-lookup"><span data-stu-id="3a010-174">Add a SignalR hub</span></span>

<span data-ttu-id="3a010-175">V `BlazorSignalRApp.Server` projektu vytvořte `Hubs` složku (plural) a přidejte následující `ChatHub` třídu ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="3a010-175">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="3a010-176">Přidání služeb a koncového bodu pro SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="3a010-176">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="3a010-177">V `BlazorSignalRApp.Server` projektu otevřete `Startup.cs` soubor.</span><span class="sxs-lookup"><span data-stu-id="3a010-177">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="3a010-178">Přidejte do `ChatHub` horní části souboru obor názvů pro třídu:</span><span class="sxs-lookup"><span data-stu-id="3a010-178">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="3a010-179">Přidejte SignalR služby middlewaru pro komprimaci a odezvu do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3a010-179">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="3a010-180">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3a010-180">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="3a010-181">V horní části Konfigurace kanálu zpracování použijte middleware pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="3a010-181">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="3a010-182">Mezi koncovými body řadičů a Fallback na straně klienta přidejte koncový bod pro centrum.</span><span class="sxs-lookup"><span data-stu-id="3a010-182">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="3a010-183">Přidat Razor kód komponenty pro chat</span><span class="sxs-lookup"><span data-stu-id="3a010-183">Add Razor component code for chat</span></span>

1. <span data-ttu-id="3a010-184">V `BlazorSignalRApp.Client` projektu otevřete `Pages/Index.razor` soubor.</span><span class="sxs-lookup"><span data-stu-id="3a010-184">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="3a010-185">Nahraďte kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3a010-185">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="3a010-186">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="3a010-186">Run the app</span></span>

1. <span data-ttu-id="3a010-187">Postupujte podle pokynů pro vaše nástroje:</span><span class="sxs-lookup"><span data-stu-id="3a010-187">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3a010-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a010-188">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3a010-189">V **Průzkumník řešení**vyberte `BlazorSignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="3a010-189">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="3a010-190">Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd> + <kbd>F5</kbd> pro spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="3a010-190">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3a010-191">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="3a010-191">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3a010-192">Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy.</span><span class="sxs-lookup"><span data-stu-id="3a010-192">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3a010-193">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="3a010-193">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="3a010-194">![SignalRBlazor WebAssemblyukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="3a010-194">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="3a010-195">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3a010-195">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3a010-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3a010-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3a010-197">Když VS Code nabídky pro vytvoření profilu spuštění pro serverovou aplikaci ( `.vscode/launch.json` ), `program` zobrazí se položka podobná následujícímu, aby odkazovala na sestavení aplikace ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="3a010-197">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="3a010-198">Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd> + <kbd>F5</kbd> pro spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="3a010-198">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3a010-199">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="3a010-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3a010-200">Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy.</span><span class="sxs-lookup"><span data-stu-id="3a010-200">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3a010-201">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="3a010-201">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="3a010-202">![SignalRBlazor WebAssemblyukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="3a010-202">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="3a010-203">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3a010-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3a010-204">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3a010-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3a010-205">Na bočním panelu **řešení** vyberte `BlazorSignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="3a010-205">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="3a010-206">Stiskněte <kbd>⌘</kbd> + <kbd>↩</kbd> ke spuštění aplikace s laděním nebo <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> ke spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="3a010-206">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3a010-207">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="3a010-207">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3a010-208">Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy.</span><span class="sxs-lookup"><span data-stu-id="3a010-208">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3a010-209">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="3a010-209">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="3a010-210">![SignalRBlazor WebAssemblyukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="3a010-210">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="3a010-211">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3a010-211">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3a010-212">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3a010-212">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="3a010-213">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3a010-213">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="3a010-214">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="3a010-214">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3a010-215">Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy.</span><span class="sxs-lookup"><span data-stu-id="3a010-215">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3a010-216">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="3a010-216">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="3a010-217">![SignalRBlazor WebAssemblyukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="3a010-217">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="3a010-218">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3a010-218">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="3a010-219">Další kroky</span><span class="sxs-lookup"><span data-stu-id="3a010-219">Next steps</span></span>

<span data-ttu-id="3a010-220">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="3a010-220">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3a010-221">Vytvoření Blazor WebAssembly projektu hostované aplikace</span><span class="sxs-lookup"><span data-stu-id="3a010-221">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="3a010-222">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="3a010-222">Add the SignalR client library</span></span>
> * <span data-ttu-id="3a010-223">Přidání SignalR centra</span><span class="sxs-lookup"><span data-stu-id="3a010-223">Add a SignalR hub</span></span>
> * <span data-ttu-id="3a010-224">Přidání SignalR služeb a koncového bodu pro SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="3a010-224">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="3a010-225">Přidat Razor kód komponenty pro chat</span><span class="sxs-lookup"><span data-stu-id="3a010-225">Add Razor component code for chat</span></span>

<span data-ttu-id="3a010-226">Další informace o vytváření Blazor aplikací najdete v Blazor dokumentaci:</span><span class="sxs-lookup"><span data-stu-id="3a010-226">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="3a010-227">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3a010-227">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="3a010-228">[SignalRvyjednávání mezi zdroji pro ověřování](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="3a010-228">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
