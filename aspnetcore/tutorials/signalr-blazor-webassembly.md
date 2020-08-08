---
title: Použít ASP.NET Core SignalR sBlazor WebAssembly
author: guardrex
description: Vytvořte aplikaci Chat, která používá ASP.NET Core SignalR s Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 3d7ae49142849c589a1a20c33d30e87747ad1935
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021624"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="c2b52-103">Použít ASP.NET Core SignalR sBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c2b52-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="c2b52-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c2b52-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c2b52-105">V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje SignalR Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="c2b52-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="c2b52-106">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="c2b52-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c2b52-107">Vytvoření Blazor WebAssembly projektu hostované aplikace</span><span class="sxs-lookup"><span data-stu-id="c2b52-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="c2b52-108">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="c2b52-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="c2b52-109">Přidání SignalR centra</span><span class="sxs-lookup"><span data-stu-id="c2b52-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="c2b52-110">Přidání SignalR služeb a koncového bodu pro SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="c2b52-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="c2b52-111">Přidat Razor kód komponenty pro chat</span><span class="sxs-lookup"><span data-stu-id="c2b52-111">Add Razor component code for chat</span></span>

<span data-ttu-id="c2b52-112">Na konci tohoto kurzu budete mít funkční chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c2b52-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="c2b52-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2b52-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c2b52-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="c2b52-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2b52-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2b52-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c2b52-116">[Visual Studio 2019 16,6 nebo novější](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="c2b52-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2b52-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2b52-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2b52-118">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2b52-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="c2b52-119">Visual Studio pro Mac verze 8,6 nebo novější</span><span class="sxs-lookup"><span data-stu-id="c2b52-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="c2b52-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c2b52-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="c2b52-121">Vytvoření projektu hostované Blazor WebAssembly aplikace</span><span class="sxs-lookup"><span data-stu-id="c2b52-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="c2b52-122">Postupujte podle pokynů pro výběr nástrojů:</span><span class="sxs-lookup"><span data-stu-id="c2b52-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2b52-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2b52-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="c2b52-124">Vyžaduje se Visual Studio 16,6 nebo novější a .NET Core SDK 3.1.300 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="c2b52-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="c2b52-125">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="c2b52-125">Create a new project.</span></span>

1. <span data-ttu-id="c2b52-126">Vyberte \*\* Blazor aplikace\*\* a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="c2b52-127">`BlazorSignalRApp`Do pole **název projektu** zadejte.</span><span class="sxs-lookup"><span data-stu-id="c2b52-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="c2b52-128">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="c2b52-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="c2b52-129">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-129">Select **Create**.</span></span>

1. <span data-ttu-id="c2b52-130">Vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="c2b52-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="c2b52-131">V části **Upřesnit**zaškrtněte políčko **ASP.NET Core hostované** .</span><span class="sxs-lookup"><span data-stu-id="c2b52-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="c2b52-132">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2b52-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2b52-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c2b52-134">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c2b52-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="c2b52-135">V Visual Studio Code otevřete složku projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="c2b52-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="c2b52-136">Když se zobrazí dialogové okno s přidáním assetů pro sestavení a ladění aplikace, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="c2b52-137">Visual Studio Code automaticky přidá `.vscode` složku s generovanými `launch.json` `tasks.json` soubory a.</span><span class="sxs-lookup"><span data-stu-id="c2b52-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2b52-138">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2b52-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c2b52-139">Nainstalujte nejnovější verzi [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) a proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="c2b52-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="c2b52-140">Vyberte **soubor**  >  **nové řešení** nebo vytvořte **Nový** projekt z **okna Start**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="c2b52-141">Na bočním panelu vyberte **Webová a konzolová**  >  **aplikace**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="c2b52-142">Vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="c2b52-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="c2b52-143">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-143">Select **Next**.</span></span>

1. <span data-ttu-id="c2b52-144">Potvrďte, že **ověřování** je nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="c2b52-145">Zaškrtněte políčko **ASP.NET Core hostované** .</span><span class="sxs-lookup"><span data-stu-id="c2b52-145">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="c2b52-146">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-146">Select **Next**.</span></span>

1. <span data-ttu-id="c2b52-147">Do pole **název projektu** název aplikace `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="c2b52-147">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="c2b52-148">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-148">Select **Create**.</span></span>

   <span data-ttu-id="c2b52-149">Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="c2b52-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="c2b52-150">Certifikát uživatele a řetězce klíčů je nutný k důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="c2b52-150">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="c2b52-151">Otevřete projekt tak, že přejdete do složky projektu a otevřete soubor řešení projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="c2b52-151">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c2b52-152">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c2b52-152">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="c2b52-153">V příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c2b52-153">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="c2b52-154">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="c2b52-154">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2b52-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2b52-155">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="c2b52-156">V **Průzkumník řešení**klikněte pravým tlačítkem na `BlazorSignalRApp.Client` projekt a vyberte **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-156">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="c2b52-157">V dialogovém okně **Spravovat balíčky NuGet** potvrďte, že je **zdroj balíčku** nastavený na `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="c2b52-157">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="c2b52-158">Když je vybraná možnost **Procházet** , `Microsoft.AspNetCore.SignalR.Client` do vyhledávacího pole zadejte.</span><span class="sxs-lookup"><span data-stu-id="c2b52-158">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="c2b52-159">Ve výsledcích hledání vyberte [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) balíček a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-159">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="c2b52-160">Pokud se zobrazí dialogové okno **Náhled změn** , vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-160">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="c2b52-161">Pokud se zobrazí dialogové okno pro **přijetí licence** , **Vyberte možnost Souhlasím** , pokud souhlasíte s licenčními podmínkami.</span><span class="sxs-lookup"><span data-stu-id="c2b52-161">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2b52-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2b52-162">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="c2b52-163">V **integrovaném terminálu** (**zobrazení**  >  **terminálu** z panelu nástrojů) spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c2b52-163">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2b52-164">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2b52-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c2b52-165">Na bočním panelu **řešení** klikněte pravým tlačítkem na `BlazorSignalRApp.Client` projekt a vyberte **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-165">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="c2b52-166">V dialogovém okně **Spravovat balíčky NuGet** potvrďte, že rozevírací seznam zdroj je nastavený na `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="c2b52-166">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="c2b52-167">Když je vybraná možnost **Procházet** , `Microsoft.AspNetCore.SignalR.Client` do vyhledávacího pole zadejte.</span><span class="sxs-lookup"><span data-stu-id="c2b52-167">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="c2b52-168">Ve výsledcích hledání zaškrtněte políčko vedle [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) balíčku a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="c2b52-168">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="c2b52-169">Pokud se zobrazí dialogové okno pro **přijetí licence** , vyberte **přijmout** , pokud souhlasíte s licenčními podmínkami.</span><span class="sxs-lookup"><span data-stu-id="c2b52-169">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c2b52-170">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c2b52-170">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="c2b52-171">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c2b52-171">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="c2b52-172">Přidání SignalR centra</span><span class="sxs-lookup"><span data-stu-id="c2b52-172">Add a SignalR hub</span></span>

<span data-ttu-id="c2b52-173">V `BlazorSignalRApp.Server` projektu vytvořte `Hubs` složku (plural) a přidejte následující `ChatHub` třídu ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="c2b52-173">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="c2b52-174">Přidání služeb a koncového bodu pro SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="c2b52-174">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="c2b52-175">V `BlazorSignalRApp.Server` projektu otevřete `Startup.cs` soubor.</span><span class="sxs-lookup"><span data-stu-id="c2b52-175">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="c2b52-176">Přidejte do `ChatHub` horní části souboru obor názvů pro třídu:</span><span class="sxs-lookup"><span data-stu-id="c2b52-176">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="c2b52-177">Přidejte SignalR služby middlewaru pro komprimaci a odezvu do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c2b52-177">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="c2b52-178">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c2b52-178">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="c2b52-179">V horní části Konfigurace kanálu zpracování použijte middleware pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="c2b52-179">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="c2b52-180">Mezi koncovými body řadičů a Fallback na straně klienta přidejte koncový bod pro centrum.</span><span class="sxs-lookup"><span data-stu-id="c2b52-180">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="c2b52-181">Přidat Razor kód komponenty pro chat</span><span class="sxs-lookup"><span data-stu-id="c2b52-181">Add Razor component code for chat</span></span>

1. <span data-ttu-id="c2b52-182">V `BlazorSignalRApp.Client` projektu otevřete `Pages/Index.razor` soubor.</span><span class="sxs-lookup"><span data-stu-id="c2b52-182">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="c2b52-183">Nahraďte kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c2b52-183">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="c2b52-184">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="c2b52-184">Run the app</span></span>

1. <span data-ttu-id="c2b52-185">Postupujte podle pokynů pro vaše nástroje:</span><span class="sxs-lookup"><span data-stu-id="c2b52-185">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2b52-186">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2b52-186">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c2b52-187">V **Průzkumník řešení**vyberte `BlazorSignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="c2b52-187">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="c2b52-188">Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd> + <kbd>F5</kbd> pro spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="c2b52-188">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="c2b52-189">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="c2b52-189">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c2b52-190">Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy.</span><span class="sxs-lookup"><span data-stu-id="c2b52-190">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="c2b52-191">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="c2b52-191">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signal):::::: No-Loc (Blazor WebAssembly)::: Ukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="c2b52-193">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="c2b52-193">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c2b52-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c2b52-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c2b52-195">Když VS Code nabídky pro vytvoření profilu spuštění pro serverovou aplikaci ( `.vscode/launch.json` ), `program` zobrazí se položka podobná následujícímu, aby odkazovala na sestavení aplikace ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="c2b52-195">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="c2b52-196">Stiskněte klávesu <kbd>F5</kbd> ke spuštění aplikace s laděním nebo <kbd>stisknutím klávesy CTRL</kbd> + <kbd>F5</kbd> pro spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="c2b52-196">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="c2b52-197">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="c2b52-197">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c2b52-198">Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy.</span><span class="sxs-lookup"><span data-stu-id="c2b52-198">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="c2b52-199">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="c2b52-199">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signal):::::: No-Loc (Blazor WebAssembly)::: Ukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="c2b52-201">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="c2b52-201">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c2b52-202">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c2b52-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c2b52-203">Na bočním panelu **řešení** vyberte `BlazorSignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="c2b52-203">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="c2b52-204">Stiskněte <kbd>⌘</kbd> + <kbd>↩</kbd> ke spuštění aplikace s laděním nebo <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> ke spuštění aplikace bez ladění.</span><span class="sxs-lookup"><span data-stu-id="c2b52-204">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="c2b52-205">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="c2b52-205">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c2b52-206">Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy.</span><span class="sxs-lookup"><span data-stu-id="c2b52-206">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="c2b52-207">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="c2b52-207">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signal):::::: No-Loc (Blazor WebAssembly)::: Ukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="c2b52-209">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="c2b52-209">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c2b52-210">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c2b52-210">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="c2b52-211">V příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c2b52-211">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="c2b52-212">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="c2b52-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c2b52-213">Zvolte buď prohlížeč, zadejte jméno a zprávu a vyberte tlačítko pro odeslání zprávy.</span><span class="sxs-lookup"><span data-stu-id="c2b52-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="c2b52-214">Název a zpráva se okamžitě zobrazí na obou stránkách:</span><span class="sxs-lookup"><span data-stu-id="c2b52-214">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signal):::::: No-Loc (Blazor WebAssembly)::: Ukázková aplikace je otevřená ve dvou oknech prohlížeče, které zobrazují vyměňované zprávy.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="c2b52-216">Quotes: *Star Trek VI:* nezjištěná země 1991 – nezjištěná &copy; [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="c2b52-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="c2b52-217">Další kroky</span><span class="sxs-lookup"><span data-stu-id="c2b52-217">Next steps</span></span>

<span data-ttu-id="c2b52-218">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="c2b52-218">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c2b52-219">Vytvoření Blazor WebAssembly projektu hostované aplikace</span><span class="sxs-lookup"><span data-stu-id="c2b52-219">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="c2b52-220">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="c2b52-220">Add the SignalR client library</span></span>
> * <span data-ttu-id="c2b52-221">Přidání SignalR centra</span><span class="sxs-lookup"><span data-stu-id="c2b52-221">Add a SignalR hub</span></span>
> * <span data-ttu-id="c2b52-222">Přidání SignalR služeb a koncového bodu pro SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="c2b52-222">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="c2b52-223">Přidat Razor kód komponenty pro chat</span><span class="sxs-lookup"><span data-stu-id="c2b52-223">Add Razor component code for chat</span></span>

<span data-ttu-id="c2b52-224">Další informace o vytváření Blazor aplikací najdete v Blazor dokumentaci:</span><span class="sxs-lookup"><span data-stu-id="c2b52-224">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="c2b52-225">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c2b52-225">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="c2b52-226">SignalRvyjednávání mezi zdroji pro ověřování</span><span class="sxs-lookup"><span data-stu-id="c2b52-226">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
