---
title: Začínáme s ASP.NET CoreSignalR
author: bradygaster
description: V tomto kurzu vytvoříte aplikaci Chat, která používá ASP.NET Core SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: 51b9eae0d4746001696e0795467eaf4c0ab2c990
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022027"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a><span data-ttu-id="1b480-103">Kurz: Začínáme s ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="1b480-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1b480-104">V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí SignalR .</span><span class="sxs-lookup"><span data-stu-id="1b480-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="1b480-105">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="1b480-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1b480-106">Vytvořte webový projekt.</span><span class="sxs-lookup"><span data-stu-id="1b480-106">Create a web project.</span></span>
> * <span data-ttu-id="1b480-107">Přidejte SignalR klientskou knihovnu.</span><span class="sxs-lookup"><span data-stu-id="1b480-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="1b480-108">Vytvořte SignalR centrum.</span><span class="sxs-lookup"><span data-stu-id="1b480-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="1b480-109">Nakonfigurujte projekt tak, aby se používal SignalR .</span><span class="sxs-lookup"><span data-stu-id="1b480-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="1b480-110">Přidejte kód, který odesílá zprávy od libovolného klienta ke všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="1b480-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="1b480-111">Na konci budete mít funkční chatovací aplikaci:</span><span class="sxs-lookup"><span data-stu-id="1b480-111">At the end, you'll have a working chat app:</span></span>

![::: No-Loc (Signaler)::: Ukázková aplikace](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="1b480-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="1b480-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b480-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b480-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b480-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b480-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b480-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1b480-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="1b480-117">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="1b480-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b480-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b480-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="1b480-119">V nabídce vyberte **soubor > nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="1b480-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="1b480-120">V dialogovém okně **vytvořit nový projekt** vyberte **ASP.NET Core webová aplikace**a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="1b480-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="1b480-121">V dialogovém okně **Konfigurovat nový projekt** pojmenujte \* SignalR chat\*do projektu a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1b480-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="1b480-122">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte **.net Core** a **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="1b480-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="1b480-123">Vyberte **Webová aplikace** , chcete-li vytvořit projekt, který používá Razor stránky, a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1b480-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b480-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b480-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="1b480-126">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="1b480-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="1b480-127">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="1b480-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b480-128">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1b480-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1b480-129">V nabídce vyberte **soubor > nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="1b480-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="1b480-130">Vyberte **.NET Core > App > Web Application** (nevybírejte možnost **Webová aplikace (Model-View-Controller)**) a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="1b480-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="1b480-131">Ujistěte se, že je **Cílová architektura** nastavená na **.NET Core 3,0**, a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="1b480-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="1b480-132">Pojmenujte \* SignalR chat\*do projektu a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1b480-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="1b480-133">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="1b480-133">Add the SignalR client library</span></span>

<span data-ttu-id="1b480-134">SignalRKnihovna serveru je součástí sdílené architektury ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="1b480-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="1b480-135">Klientská knihovna pro JavaScript není automaticky obsažena v projektu.</span><span class="sxs-lookup"><span data-stu-id="1b480-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="1b480-136">Pro tento kurz použijte Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="1b480-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="1b480-137">unpkg je síť pro doručování obsahu (CDN), která může doručovat cokoli, co najdete v NPM, správce balíčků Node.js.</span><span class="sxs-lookup"><span data-stu-id="1b480-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b480-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b480-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="1b480-139">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Přidat** > **knihovnu na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="1b480-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="1b480-140">V dialogovém okně **přidat knihovnu na straně klienta** vyberte pro **poskytovatele** možnost **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="1b480-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="1b480-141">V případě **knihovny**zadejte `@microsoft/signalr@latest` .</span><span class="sxs-lookup"><span data-stu-id="1b480-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="1b480-142">Vyberte **zvolit konkrétní soubory**, rozbalte složku *DIST/prohlížeč* a vyberte *signalr.js* a *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="1b480-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="1b480-143">Nastavte **cílové umístění** na *wwwroot/js/signál/* a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="1b480-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr knihovny](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="1b480-145">LibMan vytvoří složku *wwwroot/js/signaler* a zkopíruje na ni vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="1b480-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b480-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b480-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="1b480-147">V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="1b480-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="1b480-148">Spusťte následující příkaz, který načte SignalR klientskou knihovnu pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="1b480-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="1b480-149">Než začnete výstup zobrazovat, možná budete muset počkat několik sekund.</span><span class="sxs-lookup"><span data-stu-id="1b480-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="1b480-150">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="1b480-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="1b480-151">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="1b480-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="1b480-152">Zkopírujte soubory do cílového umístění *wwwroot/js/Signal* .</span><span class="sxs-lookup"><span data-stu-id="1b480-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="1b480-153">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="1b480-153">Copy only the specified files.</span></span>

  <span data-ttu-id="1b480-154">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1b480-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b480-155">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1b480-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1b480-156">V **terminálu**spusťte následující příkaz, kterým nainstalujete LibMan.</span><span class="sxs-lookup"><span data-stu-id="1b480-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="1b480-157">Přejděte do složky projektu (ta, která obsahuje soubor \* SignalR chat. csproj\* ).</span><span class="sxs-lookup"><span data-stu-id="1b480-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="1b480-158">Spusťte následující příkaz, který načte SignalR klientskou knihovnu pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="1b480-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="1b480-159">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="1b480-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="1b480-160">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="1b480-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="1b480-161">Zkopírujte soubory do cílového umístění *wwwroot/js/Signal* .</span><span class="sxs-lookup"><span data-stu-id="1b480-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="1b480-162">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="1b480-162">Copy only the specified files.</span></span>

  <span data-ttu-id="1b480-163">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1b480-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="1b480-164">Vytvoření SignalR centra</span><span class="sxs-lookup"><span data-stu-id="1b480-164">Create a SignalR hub</span></span>

<span data-ttu-id="1b480-165">*Centrum* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="1b480-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="1b480-166">Ve SignalR složce projektu chatu vytvořte složku *Centers* .</span><span class="sxs-lookup"><span data-stu-id="1b480-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="1b480-167">Ve složce *Centers* vytvořte soubor *ChatHub.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1b480-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="1b480-168">`ChatHub`Třída dědí z SignalR `Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="1b480-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="1b480-169">`Hub`Třída spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="1b480-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="1b480-170">`SendMessage`Metoda může být volána připojeným klientem pro odeslání zprávy všem klientům.</span><span class="sxs-lookup"><span data-stu-id="1b480-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="1b480-171">JavaScriptový kód klienta, který volá metodu, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="1b480-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="1b480-172">SignalRkód je asynchronní pro zajištění maximální škálovatelnosti.</span><span class="sxs-lookup"><span data-stu-id="1b480-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-no-locsignalr"></a><span data-ttu-id="1b480-173">KonfiguraceSignalR</span><span class="sxs-lookup"><span data-stu-id="1b480-173">Configure SignalR</span></span>

<span data-ttu-id="1b480-174">SignalRServer musí být nakonfigurován na předávání SignalR požadavků SignalR .</span><span class="sxs-lookup"><span data-stu-id="1b480-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="1b480-175">Do souboru *Startup.cs* přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="1b480-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="1b480-176">Tyto změny se přidají SignalR do systémů pro vkládání a směrování závislostí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1b480-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="1b480-177">Přidat SignalR klientský kód</span><span class="sxs-lookup"><span data-stu-id="1b480-177">Add SignalR client code</span></span>

* <span data-ttu-id="1b480-178">Nahraďte obsah v *Pages\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1b480-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="1b480-179">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="1b480-179">The preceding code:</span></span>

  * <span data-ttu-id="1b480-180">Vytvoří textová pole pro text jméno a zprávu a tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="1b480-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="1b480-181">Vytvoří seznam s `id="messagesList"` pro zobrazení zpráv přijatých z SignalR centra.</span><span class="sxs-lookup"><span data-stu-id="1b480-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="1b480-182">Obsahuje odkazy na skripty SignalR a *chat.js* kód aplikace, který vytvoříte v následujícím kroku.</span><span class="sxs-lookup"><span data-stu-id="1b480-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="1b480-183">Ve složce *wwwroot/js* vytvořte *chat.js* soubor s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1b480-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="1b480-184">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="1b480-184">The preceding code:</span></span>

  * <span data-ttu-id="1b480-185">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="1b480-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="1b480-186">Přidá k tlačítku Odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="1b480-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="1b480-187">Přidá do objektu Connection obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="1b480-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="1b480-188">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="1b480-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b480-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b480-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1b480-190">Stisknutím **kombinace kláves CTRL + F5** aplikaci spusťte bez ladění.</span><span class="sxs-lookup"><span data-stu-id="1b480-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b480-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b480-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1b480-192">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1b480-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b480-193">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1b480-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1b480-194">V nabídce vyberte **spustit > spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="1b480-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="1b480-195">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="1b480-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="1b480-196">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat zprávu** .</span><span class="sxs-lookup"><span data-stu-id="1b480-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="1b480-197">Název a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="1b480-197">The name and message are displayed on both pages instantly.</span></span>

  ![::: No-Loc (Signaler)::: Ukázková aplikace](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="1b480-199">Pokud aplikace nefunguje, otevřete nástroje pro vývojáře v prohlížeči (F12) a pokračujte na konzolu.</span><span class="sxs-lookup"><span data-stu-id="1b480-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="1b480-200">Můžou se zobrazit chyby týkající se kódu HTML a JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="1b480-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="1b480-201">Předpokládejme například, že jste umístili *signalr.js* do jiné složky, než je směrovaná.</span><span class="sxs-lookup"><span data-stu-id="1b480-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="1b480-202">V takovém případě odkaz na tento soubor nebude fungovat a v konzole se zobrazí chyba 404.</span><span class="sxs-lookup"><span data-stu-id="1b480-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="1b480-203">![Chyba přisignalr.js nenalezena](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="1b480-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="1b480-204">Pokud se zobrazí chyba ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY v Chrome, spusťte tyto příkazy a aktualizujte svůj vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="1b480-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1b480-205">V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí SignalR .</span><span class="sxs-lookup"><span data-stu-id="1b480-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="1b480-206">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="1b480-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="1b480-207">Vytvořte webový projekt.</span><span class="sxs-lookup"><span data-stu-id="1b480-207">Create a web project.</span></span>   
> * <span data-ttu-id="1b480-208">Přidejte SignalR klientskou knihovnu.</span><span class="sxs-lookup"><span data-stu-id="1b480-208">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="1b480-209">Vytvořte SignalR centrum.</span><span class="sxs-lookup"><span data-stu-id="1b480-209">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="1b480-210">Nakonfigurujte projekt tak, aby se používal SignalR .</span><span class="sxs-lookup"><span data-stu-id="1b480-210">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="1b480-211">Přidejte kód, který odesílá zprávy od libovolného klienta ke všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="1b480-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="1b480-212">Na konci budete mít funkční chatovací aplikaci: ![ ::: No-Loc (Signal)::: Ukázková aplikace](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="1b480-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="1b480-213">Požadavky</span><span class="sxs-lookup"><span data-stu-id="1b480-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="1b480-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b480-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b480-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b480-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b480-216">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1b480-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="1b480-217">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="1b480-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="1b480-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b480-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="1b480-219">V nabídce vyberte **soubor > nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="1b480-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="1b480-220">V dialogovém okně **Nový projekt** vyberte možnost **nainstalováno > Visual C# > web > ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="1b480-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="1b480-221">Pojmenujte \* SignalR chat\*projektu.</span><span class="sxs-lookup"><span data-stu-id="1b480-221">Name the project *SignalRChat*.</span></span>   

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="1b480-223">Vyberte **Webová aplikace** a vytvořte tak projekt, který používá Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="1b480-223">Select **Web Application** to create a project that uses Razor Pages.</span></span>   

* <span data-ttu-id="1b480-224">Vyberte cílovou architekturu **.NET Core**, vyberte **ASP.NET Core 2,2**a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="1b480-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b480-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b480-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="1b480-227">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="1b480-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="1b480-228">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="1b480-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b480-229">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1b480-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="1b480-230">V nabídce vyberte **soubor > nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="1b480-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="1b480-231">Vyberte **.NET Core > App > ASP.NET Core Web App** (nevybírejte **ASP.NET Core Web App (MVC)**).</span><span class="sxs-lookup"><span data-stu-id="1b480-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="1b480-232">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="1b480-232">Select **Next**.</span></span>  

* <span data-ttu-id="1b480-233">Pojmenujte \* SignalR chat\*do projektu a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1b480-233">Name the project *SignalRChat*, and then select **Create**.</span></span> 

--- 

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="1b480-234">Přidat SignalR klientskou knihovnu</span><span class="sxs-lookup"><span data-stu-id="1b480-234">Add the SignalR client library</span></span> 

<span data-ttu-id="1b480-235">SignalRKnihovna serveru je obsažena v `Microsoft.AspNetCore.App` Metapackage.</span><span class="sxs-lookup"><span data-stu-id="1b480-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="1b480-236">Klientská knihovna pro JavaScript není automaticky obsažena v projektu.</span><span class="sxs-lookup"><span data-stu-id="1b480-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="1b480-237">Pro tento kurz použijte Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="1b480-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="1b480-238">unpkg je síť pro doručování obsahu (CDN), která může doručovat cokoli, co najdete v NPM, správce balíčků Node.js.</span><span class="sxs-lookup"><span data-stu-id="1b480-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>   

# <a name="visual-studio"></a>[<span data-ttu-id="1b480-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b480-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="1b480-240">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Přidat** > **knihovnu na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="1b480-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="1b480-241">V dialogovém okně **přidat knihovnu na straně klienta** vyberte pro **poskytovatele** možnost **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="1b480-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="1b480-242">V případě **knihovny**zadejte `@microsoft/signalr@3` a vyberte nejnovější verzi, která není ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="1b480-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr knihovny](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="1b480-244">Vyberte **zvolit konkrétní soubory**, rozbalte složku *DIST/prohlížeč* a vyberte *signalr.js* a *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="1b480-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="1b480-245">Nastavte **cílové umístění** na *wwwroot/lib/Signal/* a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="1b480-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr souborů a cíle](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="1b480-247">LibMan vytvoří složku *wwwroot/lib/signaler* a zkopíruje na ni vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="1b480-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b480-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b480-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="1b480-249">V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="1b480-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="1b480-250">Spusťte následující příkaz, který načte SignalR klientskou knihovnu pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="1b480-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="1b480-251">Než začnete výstup zobrazovat, možná budete muset počkat několik sekund.</span><span class="sxs-lookup"><span data-stu-id="1b480-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="1b480-252">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="1b480-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="1b480-253">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="1b480-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="1b480-254">Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .</span><span class="sxs-lookup"><span data-stu-id="1b480-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="1b480-255">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="1b480-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="1b480-256">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1b480-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b480-257">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1b480-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="1b480-258">V **terminálu**spusťte následující příkaz, kterým nainstalujete LibMan.</span><span class="sxs-lookup"><span data-stu-id="1b480-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="1b480-259">Přejděte do složky projektu (ta, která obsahuje soubor \* SignalR chat. csproj\* ).</span><span class="sxs-lookup"><span data-stu-id="1b480-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>   

* <span data-ttu-id="1b480-260">Spusťte následující příkaz, který načte SignalR klientskou knihovnu pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="1b480-260">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="1b480-261">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="1b480-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="1b480-262">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="1b480-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="1b480-263">Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .</span><span class="sxs-lookup"><span data-stu-id="1b480-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="1b480-264">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="1b480-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="1b480-265">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1b480-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="1b480-266">Vytvoření SignalR centra</span><span class="sxs-lookup"><span data-stu-id="1b480-266">Create a SignalR hub</span></span>   

<span data-ttu-id="1b480-267">*Centrum* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="1b480-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="1b480-268">Ve SignalR složce projektu chatu vytvořte složku *Centers* .</span><span class="sxs-lookup"><span data-stu-id="1b480-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>  

* <span data-ttu-id="1b480-269">Ve složce *Centers* vytvořte soubor *ChatHub.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1b480-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="1b480-270">`ChatHub`Třída dědí z SignalR `Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="1b480-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="1b480-271">`Hub`Třída spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="1b480-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="1b480-272">`SendMessage`Metoda může být volána připojeným klientem pro odeslání zprávy všem klientům.</span><span class="sxs-lookup"><span data-stu-id="1b480-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="1b480-273">JavaScriptový kód klienta, který volá metodu, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="1b480-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="1b480-274">SignalRkód je asynchronní pro zajištění maximální škálovatelnosti.</span><span class="sxs-lookup"><span data-stu-id="1b480-274">SignalR code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-no-locsignalr"></a><span data-ttu-id="1b480-275">KonfiguraceSignalR</span><span class="sxs-lookup"><span data-stu-id="1b480-275">Configure SignalR</span></span>  

<span data-ttu-id="1b480-276">SignalRServer musí být nakonfigurován na předávání SignalR požadavků SignalR .</span><span class="sxs-lookup"><span data-stu-id="1b480-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="1b480-277">Do souboru *Startup.cs* přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="1b480-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="1b480-278">Tyto změny se přidají SignalR do systému vkládání závislostí ASP.NET Core a do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="1b480-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="1b480-279">Přidat SignalR klientský kód</span><span class="sxs-lookup"><span data-stu-id="1b480-279">Add SignalR client code</span></span>    

* <span data-ttu-id="1b480-280">Nahraďte obsah v *Pages\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1b480-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="1b480-281">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="1b480-281">The preceding code:</span></span>   

  * <span data-ttu-id="1b480-282">Vytvoří textová pole pro text jméno a zprávu a tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="1b480-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="1b480-283">Vytvoří seznam s `id="messagesList"` pro zobrazení zpráv přijatých z SignalR centra.</span><span class="sxs-lookup"><span data-stu-id="1b480-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="1b480-284">Obsahuje odkazy na skripty SignalR a *chat.js* kód aplikace, který vytvoříte v následujícím kroku.</span><span class="sxs-lookup"><span data-stu-id="1b480-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="1b480-285">Ve složce *wwwroot/js* vytvořte *chat.js* soubor s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1b480-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="1b480-286">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="1b480-286">The preceding code:</span></span>   

  * <span data-ttu-id="1b480-287">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="1b480-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="1b480-288">Přidá k tlačítku Odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="1b480-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="1b480-289">Přidá do objektu Connection obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="1b480-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="1b480-290">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="1b480-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="1b480-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b480-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="1b480-292">Stisknutím **kombinace kláves CTRL + F5** aplikaci spusťte bez ladění.</span><span class="sxs-lookup"><span data-stu-id="1b480-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b480-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b480-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="1b480-294">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1b480-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b480-295">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1b480-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1b480-296">V nabídce vyberte **spustit > spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="1b480-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="1b480-297">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="1b480-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="1b480-298">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat zprávu** .</span><span class="sxs-lookup"><span data-stu-id="1b480-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="1b480-299">Název a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="1b480-299">The name and message are displayed on both pages instantly.</span></span>   

  ![::: No-Loc (Signaler)::: Ukázková aplikace](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="1b480-301">Pokud aplikace nefunguje, otevřete nástroje pro vývojáře v prohlížeči (F12) a pokračujte na konzolu.</span><span class="sxs-lookup"><span data-stu-id="1b480-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="1b480-302">Můžou se zobrazit chyby týkající se kódu HTML a JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="1b480-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="1b480-303">Předpokládejme například, že jste umístili *signalr.js* do jiné složky, než je směrovaná.</span><span class="sxs-lookup"><span data-stu-id="1b480-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="1b480-304">V takovém případě odkaz na tento soubor nebude fungovat a v konzole se zobrazí chyba 404.</span><span class="sxs-lookup"><span data-stu-id="1b480-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="1b480-305">![Chyba přisignalr.js nenalezena](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="1b480-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="1b480-306">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1b480-306">Additional resources</span></span> 
* [<span data-ttu-id="1b480-307">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="1b480-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
