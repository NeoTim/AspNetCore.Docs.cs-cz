---
title: Začínáme s nástrojem ASP.NET Core Signal
author: bradygaster
description: V tomto kurzu vytvoříte aplikaci Chat, která používá nástroj ASP.NET Core Signal.
ms.author: bradyg
ms.custom: mvc
ms.date: 07/08/2019
uid: tutorials/signalr
ms.openlocfilehash: 2dfa994b9763a0139cb70cbf9847ac3b02b568e4
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081974"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="47ec7-103">Kurz: Začínáme s nástrojem ASP.NET Core Signal</span><span class="sxs-lookup"><span data-stu-id="47ec7-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="47ec7-104">V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje Signal.</span><span class="sxs-lookup"><span data-stu-id="47ec7-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="47ec7-105">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="47ec7-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="47ec7-106">Vytvoření webového projektu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-106">Create a web project.</span></span>
> * <span data-ttu-id="47ec7-107">Přidejte klientskou knihovnu signalizace.</span><span class="sxs-lookup"><span data-stu-id="47ec7-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="47ec7-108">Vytvořte centrum signalizace.</span><span class="sxs-lookup"><span data-stu-id="47ec7-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="47ec7-109">Nakonfigurujte projekt tak, aby používal signál.</span><span class="sxs-lookup"><span data-stu-id="47ec7-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="47ec7-110">Přidejte kód, který odesílá zprávy od libovolného klienta ke všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="47ec7-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="47ec7-111">Na konci budete mít funkční chatovací aplikaci:</span><span class="sxs-lookup"><span data-stu-id="47ec7-111">At the end, you'll have a working chat app:</span></span>

![Ukázková aplikace pro Signal](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="47ec7-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="47ec7-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47ec7-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47ec7-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="47ec7-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47ec7-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="47ec7-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="47ec7-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="47ec7-117">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="47ec7-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47ec7-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47ec7-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="47ec7-119">V nabídce vyberte **soubor > nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="47ec7-120">V dialogovém okně **vytvořit nový projekt** vyberte **ASP.NET Core webová aplikace**a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="47ec7-121">V dialogovém okně **Konfigurovat nový projekt** pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="47ec7-122">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte **.net Core** a **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-122">In the **Create a new ASP.NET Core Web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="47ec7-123">Vyberte **Webová aplikace** , chcete-li vytvořit projekt, který používá Razor Pages a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="47ec7-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47ec7-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="47ec7-126">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="47ec7-127">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="47ec7-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="47ec7-128">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="47ec7-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="47ec7-129">V nabídce vyberte **soubor > nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="47ec7-130">Vyberte **.NET Core > App > Web Application** (nevybírejte možnost **Webová aplikace (Model-View-Controller)** ) a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="47ec7-131">Ujistěte se, že je **Cílová architektura** nastavená na **.NET Core 3,0**, a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="47ec7-132">Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="47ec7-133">Přidat klientskou knihovnu signálů</span><span class="sxs-lookup"><span data-stu-id="47ec7-133">Add the SignalR client library</span></span>

<span data-ttu-id="47ec7-134">Knihovna serveru signalizace je součástí sdílené architektury ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="47ec7-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="47ec7-135">Klientská knihovna pro JavaScript není automaticky obsažena v projektu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="47ec7-136">Pro tento kurz použijte Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="47ec7-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="47ec7-137">unpkg je síť pro doručování obsahu (CDN), která může doručovat cokoli, co najdete v NPM, správce balíčků Node. js.</span><span class="sxs-lookup"><span data-stu-id="47ec7-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47ec7-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47ec7-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="47ec7-139">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Přidat** > **knihovnu na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="47ec7-140">V dialogovém okně **přidat knihovnu na straně klienta** vyberte pro **poskytovatele** možnost **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="47ec7-141">V případě **knihovny**zadejte `@aspnet/signalr@next`.</span><span class="sxs-lookup"><span data-stu-id="47ec7-141">For **Library**, enter `@aspnet/signalr@next`.</span></span>
<!-- when 3.0 is released, change @next to @latest -->

* <span data-ttu-id="47ec7-142">Vyberte možnost **zvolit konkrétní soubory**, rozbalte složku *DIST/prohlížeč* a vyberte možnost *signaler. js* a *Signal. min. js*.</span><span class="sxs-lookup"><span data-stu-id="47ec7-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="47ec7-143">Nastavte **cílové umístění** na *wwwroot/lib/Signal/* a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-143">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr knihovny](signalr/_static/3.x/libman1.png)

  <span data-ttu-id="47ec7-145">LibMan vytvoří složku *wwwroot/lib/signaler* a zkopíruje na ni vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="47ec7-145">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="47ec7-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47ec7-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="47ec7-147">V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="47ec7-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="47ec7-148">Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="47ec7-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="47ec7-149">Než začnete výstup zobrazovat, možná budete muset počkat několik sekund.</span><span class="sxs-lookup"><span data-stu-id="47ec7-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="47ec7-150">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="47ec7-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="47ec7-151">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="47ec7-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="47ec7-152">Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .</span><span class="sxs-lookup"><span data-stu-id="47ec7-152">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="47ec7-153">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="47ec7-153">Copy only the specified files.</span></span>

  <span data-ttu-id="47ec7-154">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="47ec7-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="47ec7-155">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="47ec7-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="47ec7-156">V **terminálu**spusťte následující příkaz, kterým nainstalujete LibMan.</span><span class="sxs-lookup"><span data-stu-id="47ec7-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="47ec7-157">Přejděte do složky projektu (ta, která obsahuje soubor *SignalRChat. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="47ec7-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="47ec7-158">Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="47ec7-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="47ec7-159">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="47ec7-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="47ec7-160">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="47ec7-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="47ec7-161">Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .</span><span class="sxs-lookup"><span data-stu-id="47ec7-161">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="47ec7-162">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="47ec7-162">Copy only the specified files.</span></span>

  <span data-ttu-id="47ec7-163">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="47ec7-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="47ec7-164">Vytvoření centra signalizace</span><span class="sxs-lookup"><span data-stu-id="47ec7-164">Create a SignalR hub</span></span>

<span data-ttu-id="47ec7-165">*Centrum* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="47ec7-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="47ec7-166">Ve složce projektu SignalRChat vytvořte složku *Centers* .</span><span class="sxs-lookup"><span data-stu-id="47ec7-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="47ec7-167">Ve složce *Centers* vytvořte soubor *ChatHub.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="47ec7-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="47ec7-168">Třída dědí z `Hub` třídy signaler. `ChatHub`</span><span class="sxs-lookup"><span data-stu-id="47ec7-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="47ec7-169">`Hub` Třída spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="47ec7-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="47ec7-170">`SendMessage` Metoda může být volána připojeným klientem pro odeslání zprávy všem klientům.</span><span class="sxs-lookup"><span data-stu-id="47ec7-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="47ec7-171">JavaScriptový kód klienta, který volá metodu, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="47ec7-172">Kód signalizace je asynchronní pro zajištění maximální škálovatelnosti.</span><span class="sxs-lookup"><span data-stu-id="47ec7-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="47ec7-173">Konfigurovat signál</span><span class="sxs-lookup"><span data-stu-id="47ec7-173">Configure SignalR</span></span>

<span data-ttu-id="47ec7-174">Server signalizace musí být nakonfigurován tak, aby předával požadavky na signalizaci.</span><span class="sxs-lookup"><span data-stu-id="47ec7-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="47ec7-175">Do souboru *Startup.cs* přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="47ec7-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=6,30,58)]

  <span data-ttu-id="47ec7-176">Tyto změny přidávají signál k systémům injektáže a směrování závislostí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="47ec7-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="47ec7-177">Přidat kód klienta signalizace</span><span class="sxs-lookup"><span data-stu-id="47ec7-177">Add SignalR client code</span></span>

* <span data-ttu-id="47ec7-178">Nahraďte obsah v *Pages\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="47ec7-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="47ec7-179">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="47ec7-179">The preceding code:</span></span>

  * <span data-ttu-id="47ec7-180">Vytvoří textová pole pro text jméno a zprávu a tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="47ec7-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="47ec7-181">Vytvoří seznam s nástrojem `id="messagesList"` pro zobrazení zpráv, které jsou přijímány z centra signalizace.</span><span class="sxs-lookup"><span data-stu-id="47ec7-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="47ec7-182">Obsahuje odkazy na skripty pro signál a kód aplikace *chat. js* , který vytvoříte v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="47ec7-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="47ec7-183">Ve složce *wwwroot/js* vytvořte soubor *chat. js* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="47ec7-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="47ec7-184">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="47ec7-184">The preceding code:</span></span>

  * <span data-ttu-id="47ec7-185">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="47ec7-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="47ec7-186">Přidá k tlačítku Odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="47ec7-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="47ec7-187">Přidá do objektu Connection obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="47ec7-188">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="47ec7-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47ec7-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47ec7-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="47ec7-190">Stisknutím **kombinace kláves CTRL + F5** aplikaci spusťte bez ladění.</span><span class="sxs-lookup"><span data-stu-id="47ec7-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="47ec7-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47ec7-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="47ec7-192">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="47ec7-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="47ec7-193">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="47ec7-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="47ec7-194">V nabídce vyberte **spustit > spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="47ec7-195">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="47ec7-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="47ec7-196">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat zprávu** .</span><span class="sxs-lookup"><span data-stu-id="47ec7-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="47ec7-197">Název a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="47ec7-197">The name and message are displayed on both pages instantly.</span></span>

  ![Ukázková aplikace pro Signal](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="47ec7-199">Pokud aplikace nefunguje, otevřete nástroje pro vývojáře v prohlížeči (F12) a pokračujte na konzolu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="47ec7-200">Můžou se zobrazit chyby týkající se kódu HTML a JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="47ec7-201">Předpokládejme například, že umístíte *signál. js* do jiné složky než směrovaná.</span><span class="sxs-lookup"><span data-stu-id="47ec7-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="47ec7-202">V takovém případě odkaz na tento soubor nebude fungovat a v konzole se zobrazí chyba 404.</span><span class="sxs-lookup"><span data-stu-id="47ec7-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="47ec7-203">![Chyba signalizace. js nebyla nalezena.](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="47ec7-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="47ec7-204">Pokud se zobrazí chyba ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY v Chrome nebo NS_ERROR_NET_INADEQUATE_SECURITY v prohlížeči Firefox, spusťte tyto příkazy a aktualizujte svůj vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="47ec7-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome or NS_ERROR_NET_INADEQUATE_SECURITY in Firefox, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="47ec7-205">Další postup</span><span class="sxs-lookup"><span data-stu-id="47ec7-205">Next steps</span></span>

<span data-ttu-id="47ec7-206">Další informace o signalizaci najdete v úvodu:</span><span class="sxs-lookup"><span data-stu-id="47ec7-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="47ec7-207">Úvod k signalizaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47ec7-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="47ec7-208">V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje Signal.</span><span class="sxs-lookup"><span data-stu-id="47ec7-208">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="47ec7-209">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="47ec7-209">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="47ec7-210">Vytvoření webového projektu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-210">Create a web project.</span></span>
> * <span data-ttu-id="47ec7-211">Přidejte klientskou knihovnu signalizace.</span><span class="sxs-lookup"><span data-stu-id="47ec7-211">Add the SignalR client library.</span></span>
> * <span data-ttu-id="47ec7-212">Vytvořte centrum signalizace.</span><span class="sxs-lookup"><span data-stu-id="47ec7-212">Create a SignalR hub.</span></span>
> * <span data-ttu-id="47ec7-213">Nakonfigurujte projekt tak, aby používal signál.</span><span class="sxs-lookup"><span data-stu-id="47ec7-213">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="47ec7-214">Přidejte kód, který odesílá zprávy od libovolného klienta ke všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="47ec7-214">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="47ec7-215">Na konci budete mít funkční chatovací aplikaci:</span><span class="sxs-lookup"><span data-stu-id="47ec7-215">At the end, you'll have a working chat app:</span></span>

![Ukázková aplikace pro Signal](signalr/_static/2.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="47ec7-217">Požadavky</span><span class="sxs-lookup"><span data-stu-id="47ec7-217">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47ec7-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47ec7-218">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="47ec7-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47ec7-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="47ec7-220">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="47ec7-220">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="47ec7-221">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="47ec7-221">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47ec7-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47ec7-222">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="47ec7-223">V nabídce vyberte **soubor > nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-223">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="47ec7-224">V dialogovém okně **Nový projekt** vyberte možnost **nainstalováno > C# webové aplikace Visual > Web > ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-224">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="47ec7-225">Pojmenujte projekt *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="47ec7-225">Name the project *SignalRChat*.</span></span>

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)

* <span data-ttu-id="47ec7-227">Vyberte **Webová aplikace** a vytvořte tak projekt, který používá Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="47ec7-227">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="47ec7-228">Vyberte cílovou architekturu **.NET Core**, vyberte **ASP.NET Core 2,2**a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-228">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="47ec7-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47ec7-230">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="47ec7-231">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-231">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="47ec7-232">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="47ec7-232">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="47ec7-233">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="47ec7-233">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="47ec7-234">V nabídce vyberte **soubor > nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-234">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="47ec7-235">Vyberte **.NET Core > App > ASP.NET Core Web App** (nevybírejte **ASP.NET Core Web App (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="47ec7-235">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="47ec7-236">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-236">Select **Next**.</span></span>

* <span data-ttu-id="47ec7-237">Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-237">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="47ec7-238">Přidat klientskou knihovnu signálů</span><span class="sxs-lookup"><span data-stu-id="47ec7-238">Add the SignalR client library</span></span>

<span data-ttu-id="47ec7-239">Knihovna serveru signalizace je obsažena v `Microsoft.AspNetCore.App` Metapackage.</span><span class="sxs-lookup"><span data-stu-id="47ec7-239">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="47ec7-240">Klientská knihovna pro JavaScript není automaticky obsažena v projektu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-240">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="47ec7-241">Pro tento kurz použijte Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="47ec7-241">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="47ec7-242">unpkg je síť pro doručování obsahu (CDN), která může doručovat cokoli, co najdete v NPM, správce balíčků Node. js.</span><span class="sxs-lookup"><span data-stu-id="47ec7-242">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47ec7-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47ec7-243">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="47ec7-244">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Přidat** > **knihovnu na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-244">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="47ec7-245">V dialogovém okně **přidat knihovnu na straně klienta** vyberte pro **poskytovatele** možnost **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-245">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="47ec7-246">V případě **knihovny**zadejte `@aspnet/signalr@1`a vyberte nejnovější verzi, která není ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="47ec7-246">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr knihovny](signalr/_static/2.x/libman1.png)

* <span data-ttu-id="47ec7-248">Vyberte možnost **zvolit konkrétní soubory**, rozbalte složku *DIST/prohlížeč* a vyberte možnost *signaler. js* a *Signal. min. js*.</span><span class="sxs-lookup"><span data-stu-id="47ec7-248">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="47ec7-249">Nastavte **cílové umístění** na *wwwroot/lib/Signal/* a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-249">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr souborů a cíle](signalr/_static/2.x/libman2.png)

  <span data-ttu-id="47ec7-251">LibMan vytvoří složku *wwwroot/lib/signaler* a zkopíruje na ni vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="47ec7-251">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="47ec7-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47ec7-252">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="47ec7-253">V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="47ec7-253">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="47ec7-254">Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="47ec7-254">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="47ec7-255">Než začnete výstup zobrazovat, možná budete muset počkat několik sekund.</span><span class="sxs-lookup"><span data-stu-id="47ec7-255">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="47ec7-256">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="47ec7-256">The parameters specify the following options:</span></span>
  * <span data-ttu-id="47ec7-257">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="47ec7-257">Use the unpkg provider.</span></span>
  * <span data-ttu-id="47ec7-258">Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .</span><span class="sxs-lookup"><span data-stu-id="47ec7-258">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="47ec7-259">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="47ec7-259">Copy only the specified files.</span></span>

  <span data-ttu-id="47ec7-260">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="47ec7-260">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="47ec7-261">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="47ec7-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="47ec7-262">V **terminálu**spusťte následující příkaz, kterým nainstalujete LibMan.</span><span class="sxs-lookup"><span data-stu-id="47ec7-262">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="47ec7-263">Přejděte do složky projektu (ta, která obsahuje soubor *SignalRChat. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="47ec7-263">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="47ec7-264">Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="47ec7-264">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="47ec7-265">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="47ec7-265">The parameters specify the following options:</span></span>
  * <span data-ttu-id="47ec7-266">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="47ec7-266">Use the unpkg provider.</span></span>
  * <span data-ttu-id="47ec7-267">Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .</span><span class="sxs-lookup"><span data-stu-id="47ec7-267">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="47ec7-268">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="47ec7-268">Copy only the specified files.</span></span>

  <span data-ttu-id="47ec7-269">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="47ec7-269">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="47ec7-270">Vytvoření centra signalizace</span><span class="sxs-lookup"><span data-stu-id="47ec7-270">Create a SignalR hub</span></span>

<span data-ttu-id="47ec7-271">*Centrum* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="47ec7-271">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="47ec7-272">Ve složce projektu SignalRChat vytvořte složku *Centers* .</span><span class="sxs-lookup"><span data-stu-id="47ec7-272">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="47ec7-273">Ve složce *Centers* vytvořte soubor *ChatHub.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="47ec7-273">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]

  <span data-ttu-id="47ec7-274">Třída dědí z `Hub` třídy signaler. `ChatHub`</span><span class="sxs-lookup"><span data-stu-id="47ec7-274">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="47ec7-275">`Hub` Třída spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="47ec7-275">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="47ec7-276">`SendMessage` Metoda může být volána připojeným klientem pro odeslání zprávy všem klientům.</span><span class="sxs-lookup"><span data-stu-id="47ec7-276">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="47ec7-277">JavaScriptový kód klienta, který volá metodu, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-277">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="47ec7-278">Kód signalizace je asynchronní pro zajištění maximální škálovatelnosti.</span><span class="sxs-lookup"><span data-stu-id="47ec7-278">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="47ec7-279">Konfigurovat signál</span><span class="sxs-lookup"><span data-stu-id="47ec7-279">Configure SignalR</span></span>

<span data-ttu-id="47ec7-280">Server signalizace musí být nakonfigurován tak, aby předával požadavky na signalizaci.</span><span class="sxs-lookup"><span data-stu-id="47ec7-280">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="47ec7-281">Do souboru *Startup.cs* přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="47ec7-281">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="47ec7-282">Tyto změny přidávají signál do systému vkládání závislostí ASP.NET Core a do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="47ec7-282">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="47ec7-283">Přidat kód klienta signalizace</span><span class="sxs-lookup"><span data-stu-id="47ec7-283">Add SignalR client code</span></span>

* <span data-ttu-id="47ec7-284">Nahraďte obsah v *Pages\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="47ec7-284">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]

  <span data-ttu-id="47ec7-285">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="47ec7-285">The preceding code:</span></span>

  * <span data-ttu-id="47ec7-286">Vytvoří textová pole pro text jméno a zprávu a tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="47ec7-286">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="47ec7-287">Vytvoří seznam s nástrojem `id="messagesList"` pro zobrazení zpráv, které jsou přijímány z centra signalizace.</span><span class="sxs-lookup"><span data-stu-id="47ec7-287">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="47ec7-288">Obsahuje odkazy na skripty pro signál a kód aplikace *chat. js* , který vytvoříte v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="47ec7-288">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="47ec7-289">Ve složce *wwwroot/js* vytvořte soubor *chat. js* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="47ec7-289">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]

  <span data-ttu-id="47ec7-290">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="47ec7-290">The preceding code:</span></span>

  * <span data-ttu-id="47ec7-291">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="47ec7-291">Creates and starts a connection.</span></span>
  * <span data-ttu-id="47ec7-292">Přidá k tlačítku Odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="47ec7-292">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="47ec7-293">Přidá do objektu Connection obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-293">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="47ec7-294">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="47ec7-294">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47ec7-295">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47ec7-295">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="47ec7-296">Stisknutím **kombinace kláves CTRL + F5** aplikaci spusťte bez ladění.</span><span class="sxs-lookup"><span data-stu-id="47ec7-296">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="47ec7-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47ec7-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="47ec7-298">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="47ec7-298">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="47ec7-299">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="47ec7-299">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="47ec7-300">V nabídce vyberte **spustit > spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="47ec7-300">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="47ec7-301">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="47ec7-301">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="47ec7-302">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat zprávu** .</span><span class="sxs-lookup"><span data-stu-id="47ec7-302">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="47ec7-303">Název a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="47ec7-303">The name and message are displayed on both pages instantly.</span></span>

  ![Ukázková aplikace pro Signal](signalr/_static/2.x/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="47ec7-305">Pokud aplikace nefunguje, otevřete nástroje pro vývojáře v prohlížeči (F12) a pokračujte na konzolu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-305">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="47ec7-306">Můžou se zobrazit chyby týkající se kódu HTML a JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="47ec7-306">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="47ec7-307">Předpokládejme například, že umístíte *signál. js* do jiné složky než směrovaná.</span><span class="sxs-lookup"><span data-stu-id="47ec7-307">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="47ec7-308">V takovém případě odkaz na tento soubor nebude fungovat a v konzole se zobrazí chyba 404.</span><span class="sxs-lookup"><span data-stu-id="47ec7-308">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="47ec7-309">![Chyba signalizace. js nebyla nalezena.](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="47ec7-309">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>

## <a name="next-steps"></a><span data-ttu-id="47ec7-310">Další postup</span><span class="sxs-lookup"><span data-stu-id="47ec7-310">Next steps</span></span>

<span data-ttu-id="47ec7-311">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="47ec7-311">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="47ec7-312">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="47ec7-312">Create a web app project.</span></span>
> * <span data-ttu-id="47ec7-313">Přidejte klientskou knihovnu signalizace.</span><span class="sxs-lookup"><span data-stu-id="47ec7-313">Add the SignalR client library.</span></span>
> * <span data-ttu-id="47ec7-314">Vytvořte centrum signalizace.</span><span class="sxs-lookup"><span data-stu-id="47ec7-314">Create a SignalR hub.</span></span>
> * <span data-ttu-id="47ec7-315">Nakonfigurujte projekt tak, aby používal signál.</span><span class="sxs-lookup"><span data-stu-id="47ec7-315">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="47ec7-316">Přidejte kód, který používá centrum k posílání zpráv od libovolného klienta ke všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="47ec7-316">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="47ec7-317">Další informace o signalizaci najdete v úvodu:</span><span class="sxs-lookup"><span data-stu-id="47ec7-317">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="47ec7-318">Úvod k signalizaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47ec7-318">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end
