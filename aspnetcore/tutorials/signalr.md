---
title: Začínáme s nástrojem ASP.NET Core Signal
author: bradygaster
description: V tomto kurzu vytvoříte aplikaci Chat, která používá nástroj ASP.NET Core Signal.
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/signalr
ms.openlocfilehash: 843416cf00c9241f8c05b1aba041ebbe7a05cf80
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037664"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="5befc-103">Kurz: Začínáme s nástrojem ASP.NET Core Signal</span><span class="sxs-lookup"><span data-stu-id="5befc-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

<span data-ttu-id="5befc-104">V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje Signal.</span><span class="sxs-lookup"><span data-stu-id="5befc-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="5befc-105">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="5befc-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5befc-106">Vytvoření webového projektu.</span><span class="sxs-lookup"><span data-stu-id="5befc-106">Create a web project.</span></span>
> * <span data-ttu-id="5befc-107">Přidejte klientskou knihovnu signalizace.</span><span class="sxs-lookup"><span data-stu-id="5befc-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="5befc-108">Vytvořte centrum signalizace.</span><span class="sxs-lookup"><span data-stu-id="5befc-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="5befc-109">Nakonfigurujte projekt tak, aby používal signál.</span><span class="sxs-lookup"><span data-stu-id="5befc-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="5befc-110">Přidejte kód, který odesílá zprávy od libovolného klienta ke všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="5befc-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="5befc-111">Na konci budete mít funkční chatovací aplikaci:</span><span class="sxs-lookup"><span data-stu-id="5befc-111">At the end, you'll have a working chat app:</span></span>

![Ukázková aplikace pro Signal](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="5befc-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="5befc-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5befc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5befc-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5befc-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5befc-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5befc-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5befc-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="5befc-117">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="5befc-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5befc-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5befc-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="5befc-119">V nabídce vyberte **soubor > nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="5befc-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="5befc-120">V dialogovém okně **vytvořit nový projekt** vyberte **ASP.NET Core webová aplikace**a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5befc-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="5befc-121">V dialogovém okně **Konfigurovat nový projekt** pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5befc-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="5befc-122">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte **.net Core** a **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="5befc-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="5befc-123">Vyberte **Webová aplikace** , chcete-li vytvořit projekt, který používá Razor Pages a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5befc-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5befc-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5befc-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="5befc-126">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="5befc-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="5befc-127">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5befc-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5befc-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5befc-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5befc-129">V nabídce vyberte **soubor > nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="5befc-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="5befc-130">Vyberte **.NET Core > App > Web Application** (nevybírejte možnost **Webová aplikace (Model-View-Controller)** ) a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5befc-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="5befc-131">Ujistěte se, že je **Cílová architektura** nastavená na **.NET Core 3,0**, a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5befc-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="5befc-132">Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5befc-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="5befc-133">Přidat klientskou knihovnu signálů</span><span class="sxs-lookup"><span data-stu-id="5befc-133">Add the SignalR client library</span></span>

<span data-ttu-id="5befc-134">Knihovna serveru signalizace je součástí sdílené architektury ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="5befc-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="5befc-135">Klientská knihovna pro JavaScript není automaticky obsažena v projektu.</span><span class="sxs-lookup"><span data-stu-id="5befc-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="5befc-136">Pro tento kurz použijte Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="5befc-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="5befc-137">unpkg je síť pro doručování obsahu (CDN), která může doručovat cokoli, co najdete v NPM, správce balíčků Node. js.</span><span class="sxs-lookup"><span data-stu-id="5befc-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5befc-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5befc-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="5befc-139">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Přidat** > **Klientská knihovna**.</span><span class="sxs-lookup"><span data-stu-id="5befc-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="5befc-140">V dialogovém okně **přidat knihovnu na straně klienta** vyberte pro **poskytovatele** možnost **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="5befc-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="5befc-141">V případě **knihovny**zadejte `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="5befc-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="5befc-142">Vyberte možnost **zvolit konkrétní soubory**, rozbalte složku *DIST/prohlížeč* a vyberte možnost *signaler. js* a *Signal. min. js*.</span><span class="sxs-lookup"><span data-stu-id="5befc-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="5befc-143">Nastavte **cílové umístění** na *wwwroot/js/signál/* a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="5befc-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr knihovny](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="5befc-145">LibMan vytvoří složku *wwwroot/js/signaler* a zkopíruje na ni vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="5befc-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5befc-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5befc-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="5befc-147">V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="5befc-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="5befc-148">Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="5befc-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="5befc-149">Než začnete výstup zobrazovat, možná budete muset počkat několik sekund.</span><span class="sxs-lookup"><span data-stu-id="5befc-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="5befc-150">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="5befc-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="5befc-151">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="5befc-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="5befc-152">Zkopírujte soubory do cílového umístění *wwwroot/js/Signal* .</span><span class="sxs-lookup"><span data-stu-id="5befc-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="5befc-153">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="5befc-153">Copy only the specified files.</span></span>

  <span data-ttu-id="5befc-154">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5befc-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5befc-155">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5befc-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5befc-156">V **terminálu**spusťte následující příkaz, kterým nainstalujete LibMan.</span><span class="sxs-lookup"><span data-stu-id="5befc-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="5befc-157">Přejděte do složky projektu (ta, která obsahuje soubor *SignalRChat. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="5befc-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="5befc-158">Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="5befc-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="5befc-159">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="5befc-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="5befc-160">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="5befc-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="5befc-161">Zkopírujte soubory do cílového umístění *wwwroot/js/Signal* .</span><span class="sxs-lookup"><span data-stu-id="5befc-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="5befc-162">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="5befc-162">Copy only the specified files.</span></span>

  <span data-ttu-id="5befc-163">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5befc-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="5befc-164">Vytvoření centra signalizace</span><span class="sxs-lookup"><span data-stu-id="5befc-164">Create a SignalR hub</span></span>

<span data-ttu-id="5befc-165">*Centrum* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="5befc-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="5befc-166">Ve složce projektu SignalRChat vytvořte složku *Centers* .</span><span class="sxs-lookup"><span data-stu-id="5befc-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="5befc-167">Ve složce *Centers* vytvořte soubor *ChatHub.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5befc-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="5befc-168">Třída `ChatHub` dědí z třídy Signaler `Hub`.</span><span class="sxs-lookup"><span data-stu-id="5befc-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="5befc-169">Třída `Hub` spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="5befc-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="5befc-170">Metodu `SendMessage` lze volat připojeným klientem pro odeslání zprávy všem klientům.</span><span class="sxs-lookup"><span data-stu-id="5befc-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="5befc-171">JavaScriptový kód klienta, který volá metodu, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5befc-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="5befc-172">Kód signalizace je asynchronní pro zajištění maximální škálovatelnosti.</span><span class="sxs-lookup"><span data-stu-id="5befc-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="5befc-173">Konfigurovat signál</span><span class="sxs-lookup"><span data-stu-id="5befc-173">Configure SignalR</span></span>

<span data-ttu-id="5befc-174">Server signalizace musí být nakonfigurován tak, aby předával požadavky na signalizaci.</span><span class="sxs-lookup"><span data-stu-id="5befc-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="5befc-175">Do souboru *Startup.cs* přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="5befc-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="5befc-176">Tyto změny přidávají signál k systémům injektáže a směrování závislostí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5befc-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="5befc-177">Přidat kód klienta signalizace</span><span class="sxs-lookup"><span data-stu-id="5befc-177">Add SignalR client code</span></span>

* <span data-ttu-id="5befc-178">Nahraďte obsah v *Pages\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5befc-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="5befc-179">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="5befc-179">The preceding code:</span></span>

  * <span data-ttu-id="5befc-180">Vytvoří textová pole pro text jméno a zprávu a tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="5befc-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="5befc-181">Vytvoří seznam s `id="messagesList"` pro zobrazení zpráv, které byly přijaty z centra signalizace.</span><span class="sxs-lookup"><span data-stu-id="5befc-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="5befc-182">Obsahuje odkazy na skripty pro signál a kód aplikace *chat. js* , který vytvoříte v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="5befc-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="5befc-183">Ve složce *wwwroot/js* vytvořte soubor *chat. js* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5befc-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="5befc-184">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="5befc-184">The preceding code:</span></span>

  * <span data-ttu-id="5befc-185">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="5befc-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="5befc-186">Přidá k tlačítku Odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="5befc-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="5befc-187">Přidá do objektu Connection obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="5befc-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="5befc-188">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="5befc-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5befc-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5befc-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5befc-190">Stisknutím **kombinace kláves CTRL + F5** aplikaci spusťte bez ladění.</span><span class="sxs-lookup"><span data-stu-id="5befc-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5befc-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5befc-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5befc-192">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5befc-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5befc-193">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5befc-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5befc-194">V nabídce vyberte **spustit > spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="5befc-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="5befc-195">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="5befc-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="5befc-196">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat zprávu** .</span><span class="sxs-lookup"><span data-stu-id="5befc-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="5befc-197">Název a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="5befc-197">The name and message are displayed on both pages instantly.</span></span>

  ![Ukázková aplikace pro Signal](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="5befc-199">Pokud aplikace nefunguje, otevřete nástroje pro vývojáře v prohlížeči (F12) a pokračujte na konzolu.</span><span class="sxs-lookup"><span data-stu-id="5befc-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="5befc-200">Můžou se zobrazit chyby týkající se kódu HTML a JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="5befc-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="5befc-201">Předpokládejme například, že umístíte *signál. js* do jiné složky než směrovaná.</span><span class="sxs-lookup"><span data-stu-id="5befc-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="5befc-202">V takovém případě odkaz na tento soubor nebude fungovat a v konzole se zobrazí chyba 404.</span><span class="sxs-lookup"><span data-stu-id="5befc-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="5befc-203">@no__t -0signalr. js se nenašla chyba @ no__t-1.</span><span class="sxs-lookup"><span data-stu-id="5befc-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="5befc-204">Pokud se zobrazí chyba ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY v Chrome, spusťte tyto příkazy a aktualizujte svůj vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="5befc-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="5befc-205">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5befc-205">Next steps</span></span>

<span data-ttu-id="5befc-206">Další informace o signalizaci najdete v úvodu:</span><span class="sxs-lookup"><span data-stu-id="5befc-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5befc-207">Úvod k signalizaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5befc-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
