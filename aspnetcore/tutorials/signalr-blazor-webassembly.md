---
title: Použití ASP.NET Core SignalR s Blazor WebAssembly
author: guardrex
description: Vytvořte aplikaci Chat, která používá ASP.NET Core SignalR s Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 8ef029af10f767ae505fddc636bb15f7e7c5e538
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102694"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="baa66-103">Použití ASP.NET Core SignalR s Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="baa66-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="baa66-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="baa66-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="baa66-105">V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí rozhraní SignalR Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="baa66-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="baa66-106">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="baa66-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="baa66-107">Vytvoření Blazor projektu hostované aplikace WebAssembly</span><span class="sxs-lookup"><span data-stu-id="baa66-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="baa66-108">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="baa66-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="baa66-109">Přidání SignalR centra</span><span class="sxs-lookup"><span data-stu-id="baa66-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="baa66-110">Přidání SignalR služeb a koncového bodu pro SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="baa66-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="baa66-111">Přidat Razor kód komponenty pro chat</span><span class="sxs-lookup"><span data-stu-id="baa66-111">Add Razor component code for chat</span></span>

<span data-ttu-id="baa66-112">Na konci tohoto kurzu budete mít funkční chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="baa66-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="baa66-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="baa66-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="baa66-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="baa66-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="baa66-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="baa66-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="baa66-116">[Visual Studio 2019 16,6 nebo novější](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="baa66-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="baa66-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="baa66-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="baa66-118">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="baa66-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="baa66-119">Visual Studio pro Mac verze 8,6 nebo novější</span><span class="sxs-lookup"><span data-stu-id="baa66-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="baa66-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="baa66-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="baa66-121">Vytvoření hostovaného Blazor projektu aplikace WebAssembly</span><span class="sxs-lookup"><span data-stu-id="baa66-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="baa66-122">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="baa66-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="baa66-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="baa66-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="baa66-124">Vyžaduje se Visual Studio 16,6 nebo novější a .NET Core SDK 3.1.300 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="baa66-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="baa66-125">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="baa66-125">Create a new project.</span></span>

1. <span data-ttu-id="baa66-126">Vyberte \*\* Blazor aplikace\*\* a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="baa66-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="baa66-127">Do pole **název projektu** zadejte "BlazorSignalRApp".</span><span class="sxs-lookup"><span data-stu-id="baa66-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="baa66-128">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="baa66-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="baa66-129">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="baa66-129">Select **Create**.</span></span>

1. <span data-ttu-id="baa66-130">Vyberte šablonu \*\* Blazor aplikace pro WebAssembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="baa66-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="baa66-131">V části **Upřesnit**zaškrtněte políčko **ASP.NET Core hostované** .</span><span class="sxs-lookup"><span data-stu-id="baa66-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="baa66-132">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="baa66-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="baa66-133">Pokud jste provedli upgrade nebo instalaci nové verze sady Visual Studio a Blazor Šablona sestavení (GAC) se nezobrazí v uživatelském rozhraní vs, přeinstalujte ji pomocí `dotnet new` příkazu uvedeného výše.</span><span class="sxs-lookup"><span data-stu-id="baa66-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="baa66-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="baa66-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="baa66-135">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="baa66-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="baa66-136">V Visual Studio Code otevřete složku projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="baa66-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="baa66-137">Když se zobrazí dialogové okno s přidáním assetů pro sestavení a ladění aplikace, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="baa66-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="baa66-138">Visual Studio Code automaticky přidá složku *. VSCode* , která vygenerovala *launch.js* a *tasks.jsna* soubory.</span><span class="sxs-lookup"><span data-stu-id="baa66-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="baa66-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="baa66-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="baa66-140">Nainstalujte nejnovější verzi [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) a proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="baa66-140">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="baa66-141">Vyberte **soubor**  >  **nové řešení** nebo vytvořte **Nový** projekt z **okna Start**.</span><span class="sxs-lookup"><span data-stu-id="baa66-141">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="baa66-142">Na bočním panelu vyberte **Webová a konzolová**  >  **aplikace**.</span><span class="sxs-lookup"><span data-stu-id="baa66-142">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="baa66-143">Vyberte šablonu \*\* Blazor aplikace pro WebAssembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="baa66-143">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="baa66-144">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="baa66-144">Select **Next**.</span></span>

   <span data-ttu-id="baa66-145">Potvrďte následující konfigurace:</span><span class="sxs-lookup"><span data-stu-id="baa66-145">Confirm the following configurations:</span></span>

   * <span data-ttu-id="baa66-146">**Cílová architektura** je nastavená na **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="baa66-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="baa66-147">**Ověřování** nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="baa66-147">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="baa66-148">Zaškrtněte políčko **ASP.NET Core hostované** .</span><span class="sxs-lookup"><span data-stu-id="baa66-148">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="baa66-149">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="baa66-149">Select **Next**.</span></span>

1. <span data-ttu-id="baa66-150">Do pole **název projektu** název aplikace `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="baa66-150">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="baa66-151">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="baa66-151">Select **Create**.</span></span>

   <span data-ttu-id="baa66-152">Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="baa66-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="baa66-153">Certifikát uživatele a řetězce klíčů je nutný k důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="baa66-153">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="baa66-154">Otevřete projekt tak, že přejdete do složky projektu a otevřete soubor řešení projektu (*. sln*).</span><span class="sxs-lookup"><span data-stu-id="baa66-154">Open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="baa66-155">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="baa66-155">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="baa66-156">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="baa66-156">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="baa66-157">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="baa66-157">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="baa66-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="baa66-158">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="baa66-159">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt **BlazorSignalRApp. Client** a vyberte možnost **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="baa66-159">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="baa66-160">V dialogovém okně **Spravovat balíčky NuGet** ověřte, že je **zdroj balíčku** nastavený na *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="baa66-160">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="baa66-161">Vyberte možnost **Procházet** a zadejte "Microsoft. AspNetCore. SignalR . Client do vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="baa66-161">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="baa66-162">Ve výsledcích hledání vyberte [Microsoft. AspNetCore. SignalR . Balíček klienta](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="baa66-162">In the search results, select the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Install**.</span></span>

1. <span data-ttu-id="baa66-163">Pokud se zobrazí dialogové okno **Náhled změn** , vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="baa66-163">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="baa66-164">Pokud se zobrazí dialogové okno pro **přijetí licence** , **Vyberte možnost Souhlasím** , pokud souhlasíte s licenčními podmínkami.</span><span class="sxs-lookup"><span data-stu-id="baa66-164">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="baa66-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="baa66-165">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="baa66-166">V **integrovaném terminálu** (**zobrazení**  >  **terminálu** z panelu nástrojů) spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="baa66-166">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="baa66-167">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="baa66-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="baa66-168">Na bočním panelu **řešení** klikněte pravým tlačítkem myši na projekt **BlazorSignalRApp. Client** a vyberte možnost **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="baa66-168">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="baa66-169">V dialogovém okně **Spravovat balíčky NuGet** potvrďte, že je rozevírací seznam zdroj nastavený na *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="baa66-169">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="baa66-170">Vyberte možnost **Procházet** a zadejte "Microsoft. AspNetCore. SignalR . Client do vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="baa66-170">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="baa66-171">Ve výsledcích hledání zaškrtněte políčko u: [Microsoft. AspNetCore. SignalR Balíček klienta](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="baa66-171">In the search results, select the check box next to the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Add Package**.</span></span>

1. <span data-ttu-id="baa66-172">Pokud se zobrazí dialogové okno pro **přijetí licence** , vyberte **přijmout** , pokud souhlasíte s licenčními podmínkami.</span><span class="sxs-lookup"><span data-stu-id="baa66-172">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="baa66-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="baa66-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="baa66-174">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="baa66-174">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="baa66-175">Přidání SignalR centra</span><span class="sxs-lookup"><span data-stu-id="baa66-175">Add a SignalR hub</span></span>

<span data-ttu-id="baa66-176">V projektu **BlazorSignalRApp. Server** vytvořte složku *Centers* (plural) a přidejte následující `ChatHub` třídu (*Centers/ChatHub. cs*):</span><span class="sxs-lookup"><span data-stu-id="baa66-176">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="baa66-177">Přidání služeb a koncového bodu pro SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="baa66-177">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="baa66-178">V projektu **BlazorSignalRApp. Server** otevřete soubor *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="baa66-178">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="baa66-179">Přidejte do `ChatHub` horní části souboru obor názvů pro třídu:</span><span class="sxs-lookup"><span data-stu-id="baa66-179">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="baa66-180">Přidejte SignalR služby middlewaru pro komprimaci a odezvu do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="baa66-180">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="baa66-181">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="baa66-181">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="baa66-182">V horní části Konfigurace kanálu zpracování použijte middleware pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="baa66-182">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="baa66-183">Mezi koncovými body řadičů a Fallback na straně klienta přidejte koncový bod pro centrum.</span><span class="sxs-lookup"><span data-stu-id="baa66-183">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="baa66-184">Přidat Razor kód komponenty pro chat</span><span class="sxs-lookup"><span data-stu-id="baa66-184">Add Razor component code for chat</span></span>

1. <span data-ttu-id="baa66-185">V projektu **BlazorSignalRApp. Client** otevřete soubor *pages/index. Razor* .</span><span class="sxs-lookup"><span data-stu-id="baa66-185">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="baa66-186">Nahraďte kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="baa66-186">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="baa66-187">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="baa66-187">Run the app</span></span>

1. <span data-ttu-id="baa66-188">Postupujte podle pokynů pro vaše nástroje:</span><span class="sxs-lookup"><span data-stu-id="baa66-188">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="baa66-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="baa66-189">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="baa66-190">V **Průzkumník řešení**vyberte projekt **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="baa66-190">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="baa66-191">Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd> + <kbd>F5</kbd> pro spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="baa66-191">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="baa66-192">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="baa66-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="baa66-193">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="baa66-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="baa66-194">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="baa66-194">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="baa66-195">![SignalRBlazorUkázková aplikace WebAssembly je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="baa66-195">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="baa66-196">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="baa66-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="baa66-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="baa66-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="baa66-198">Když VS Code nabídky vytvoří profil spuštění pro serverovou aplikaci (*. VSCode/launch.json*), `program` zobrazí se položka podobná následujícímu, aby odkazovala na sestavení aplikace ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="baa66-198">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="baa66-199">Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd> + <kbd>F5</kbd> pro spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="baa66-199">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="baa66-200">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="baa66-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="baa66-201">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="baa66-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="baa66-202">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="baa66-202">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="baa66-203">![SignalRBlazorUkázková aplikace WebAssembly je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="baa66-203">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="baa66-204">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="baa66-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="baa66-205">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="baa66-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="baa66-206">Na bočním panelu **řešení** vyberte projekt **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="baa66-206">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="baa66-207">Stiskněte <kbd>⌘</kbd> + <kbd>↩</kbd> ke spuštění aplikace s laděním nebo <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> ke spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="baa66-207">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="baa66-208">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="baa66-208">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="baa66-209">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="baa66-209">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="baa66-210">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="baa66-210">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="baa66-211">![SignalRBlazorUkázková aplikace WebAssembly je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="baa66-211">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="baa66-212">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="baa66-212">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="baa66-213">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="baa66-213">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="baa66-214">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="baa66-214">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="baa66-215">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="baa66-215">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="baa66-216">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="baa66-216">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="baa66-217">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="baa66-217">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="baa66-218">![SignalRBlazorUkázková aplikace WebAssembly je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="baa66-218">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="baa66-219">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="baa66-219">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="baa66-220">Další kroky</span><span class="sxs-lookup"><span data-stu-id="baa66-220">Next steps</span></span>

<span data-ttu-id="baa66-221">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="baa66-221">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="baa66-222">Vytvoření Blazor projektu hostované aplikace WebAssembly</span><span class="sxs-lookup"><span data-stu-id="baa66-222">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="baa66-223">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="baa66-223">Add the SignalR client library</span></span>
> * <span data-ttu-id="baa66-224">Přidání SignalR centra</span><span class="sxs-lookup"><span data-stu-id="baa66-224">Add a SignalR hub</span></span>
> * <span data-ttu-id="baa66-225">Přidání SignalR služeb a koncového bodu pro SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="baa66-225">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="baa66-226">Přidat Razor kód komponenty pro chat</span><span class="sxs-lookup"><span data-stu-id="baa66-226">Add Razor component code for chat</span></span>

<span data-ttu-id="baa66-227">Další informace o vytváření Blazor aplikací najdete v Blazor dokumentaci:</span><span class="sxs-lookup"><span data-stu-id="baa66-227">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="baa66-228">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="baa66-228">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="baa66-229">[SignalRvyjednávání mezi zdroji pro ověřování](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="baa66-229">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
