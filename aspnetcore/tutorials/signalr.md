---
title: Začínáme s nástrojem ASP.NET Core Signal
author: bradygaster
description: V tomto kurzu vytvoříte aplikaci Chat, která používá nástroj ASP.NET Core Signal.
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2019
uid: tutorials/signalr
ms.openlocfilehash: 7a6574bd3c463f0890f5dc076944f1ab0f0c919a
ms.sourcegitcommit: e54672f5c493258dc449fac5b98faf47eb123b28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71248401"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="3c15c-103">Kurz: Začínáme s nástrojem ASP.NET Core Signal</span><span class="sxs-lookup"><span data-stu-id="3c15c-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3c15c-104">V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje Signal.</span><span class="sxs-lookup"><span data-stu-id="3c15c-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="3c15c-105">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="3c15c-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c15c-106">Vytvoření webového projektu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-106">Create a web project.</span></span>
> * <span data-ttu-id="3c15c-107">Přidejte klientskou knihovnu signalizace.</span><span class="sxs-lookup"><span data-stu-id="3c15c-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="3c15c-108">Vytvořte centrum signalizace.</span><span class="sxs-lookup"><span data-stu-id="3c15c-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="3c15c-109">Nakonfigurujte projekt tak, aby používal signál.</span><span class="sxs-lookup"><span data-stu-id="3c15c-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="3c15c-110">Přidejte kód, který odesílá zprávy od libovolného klienta ke všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="3c15c-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="3c15c-111">Na konci budete mít funkční chatovací aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3c15c-111">At the end, you'll have a working chat app:</span></span>

![Ukázková aplikace pro Signal](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="3c15c-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3c15c-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3c15c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c15c-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3c15c-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c15c-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3c15c-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3c15c-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="3c15c-117">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="3c15c-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3c15c-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c15c-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="3c15c-119">V nabídce vyberte **soubor > nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="3c15c-120">V dialogovém okně **vytvořit nový projekt** vyberte **ASP.NET Core webová aplikace**a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="3c15c-121">V dialogovém okně **Konfigurovat nový projekt** pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="3c15c-122">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte **.net Core** a **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-122">In the **Create a new ASP.NET Core Web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="3c15c-123">Vyberte **Webová aplikace** , chcete-li vytvořit projekt, který používá Razor Pages a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3c15c-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c15c-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="3c15c-126">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="3c15c-127">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3c15c-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3c15c-128">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3c15c-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c15c-129">V nabídce vyberte **soubor > nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="3c15c-130">Vyberte **.NET Core > App > Web Application** (nevybírejte možnost **Webová aplikace (Model-View-Controller)** ) a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="3c15c-131">Ujistěte se, že je **Cílová architektura** nastavená na **.NET Core 3,0**, a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="3c15c-132">Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="3c15c-133">Přidat klientskou knihovnu signálů</span><span class="sxs-lookup"><span data-stu-id="3c15c-133">Add the SignalR client library</span></span>

<span data-ttu-id="3c15c-134">Knihovna serveru signalizace je součástí sdílené architektury ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="3c15c-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="3c15c-135">Klientská knihovna pro JavaScript není automaticky obsažena v projektu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="3c15c-136">Pro tento kurz použijte Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="3c15c-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="3c15c-137">unpkg je síť pro doručování obsahu (CDN), která může doručovat cokoli, co najdete v NPM, správce balíčků Node. js.</span><span class="sxs-lookup"><span data-stu-id="3c15c-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3c15c-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c15c-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="3c15c-139">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Přidat** > **knihovnu na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="3c15c-140">V dialogovém okně **přidat knihovnu na straně klienta** vyberte pro **poskytovatele** možnost **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="3c15c-141">V případě **knihovny**zadejte `@aspnet/signalr@next`.</span><span class="sxs-lookup"><span data-stu-id="3c15c-141">For **Library**, enter `@aspnet/signalr@next`.</span></span>
<!-- when 3.0 is released, change @next to @latest -->

* <span data-ttu-id="3c15c-142">Vyberte možnost **zvolit konkrétní soubory**, rozbalte složku *DIST/prohlížeč* a vyberte možnost *signaler. js* a *Signal. min. js*.</span><span class="sxs-lookup"><span data-stu-id="3c15c-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="3c15c-143">Nastavte **cílové umístění** na *wwwroot/lib/Signal/* a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-143">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr knihovny](signalr/_static/3.x/libman1.png)

  <span data-ttu-id="3c15c-145">LibMan vytvoří složku *wwwroot/lib/signaler* a zkopíruje na ni vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="3c15c-145">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3c15c-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c15c-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="3c15c-147">V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="3c15c-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="3c15c-148">Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="3c15c-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="3c15c-149">Než začnete výstup zobrazovat, možná budete muset počkat několik sekund.</span><span class="sxs-lookup"><span data-stu-id="3c15c-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="3c15c-150">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="3c15c-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="3c15c-151">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="3c15c-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="3c15c-152">Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .</span><span class="sxs-lookup"><span data-stu-id="3c15c-152">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="3c15c-153">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="3c15c-153">Copy only the specified files.</span></span>

  <span data-ttu-id="3c15c-154">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3c15c-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3c15c-155">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3c15c-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c15c-156">V **terminálu**spusťte následující příkaz, kterým nainstalujete LibMan.</span><span class="sxs-lookup"><span data-stu-id="3c15c-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="3c15c-157">Přejděte do složky projektu (ta, která obsahuje soubor *SignalRChat. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="3c15c-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="3c15c-158">Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="3c15c-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="3c15c-159">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="3c15c-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="3c15c-160">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="3c15c-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="3c15c-161">Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .</span><span class="sxs-lookup"><span data-stu-id="3c15c-161">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="3c15c-162">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="3c15c-162">Copy only the specified files.</span></span>

  <span data-ttu-id="3c15c-163">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3c15c-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="3c15c-164">Vytvoření centra signalizace</span><span class="sxs-lookup"><span data-stu-id="3c15c-164">Create a SignalR hub</span></span>

<span data-ttu-id="3c15c-165">*Centrum* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="3c15c-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="3c15c-166">Ve složce projektu SignalRChat vytvořte složku *Centers* .</span><span class="sxs-lookup"><span data-stu-id="3c15c-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="3c15c-167">Ve složce *Centers* vytvořte soubor *ChatHub.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3c15c-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="3c15c-168">Třída dědí z `Hub` třídy signaler. `ChatHub`</span><span class="sxs-lookup"><span data-stu-id="3c15c-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="3c15c-169">`Hub` Třída spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="3c15c-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="3c15c-170">`SendMessage` Metoda může být volána připojeným klientem pro odeslání zprávy všem klientům.</span><span class="sxs-lookup"><span data-stu-id="3c15c-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="3c15c-171">JavaScriptový kód klienta, který volá metodu, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="3c15c-172">Kód signalizace je asynchronní pro zajištění maximální škálovatelnosti.</span><span class="sxs-lookup"><span data-stu-id="3c15c-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="3c15c-173">Konfigurovat signál</span><span class="sxs-lookup"><span data-stu-id="3c15c-173">Configure SignalR</span></span>

<span data-ttu-id="3c15c-174">Server signalizace musí být nakonfigurován tak, aby předával požadavky na signalizaci.</span><span class="sxs-lookup"><span data-stu-id="3c15c-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="3c15c-175">Do souboru *Startup.cs* přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="3c15c-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=6,30,58)]

  <span data-ttu-id="3c15c-176">Tyto změny přidávají signál k systémům injektáže a směrování závislostí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c15c-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="3c15c-177">Přidat kód klienta signalizace</span><span class="sxs-lookup"><span data-stu-id="3c15c-177">Add SignalR client code</span></span>

* <span data-ttu-id="3c15c-178">Nahraďte obsah v *Pages\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3c15c-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="3c15c-179">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="3c15c-179">The preceding code:</span></span>

  * <span data-ttu-id="3c15c-180">Vytvoří textová pole pro text jméno a zprávu a tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="3c15c-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="3c15c-181">Vytvoří seznam s nástrojem `id="messagesList"` pro zobrazení zpráv, které jsou přijímány z centra signalizace.</span><span class="sxs-lookup"><span data-stu-id="3c15c-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="3c15c-182">Obsahuje odkazy na skripty pro signál a kód aplikace *chat. js* , který vytvoříte v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="3c15c-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="3c15c-183">Ve složce *wwwroot/js* vytvořte soubor *chat. js* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3c15c-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="3c15c-184">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="3c15c-184">The preceding code:</span></span>

  * <span data-ttu-id="3c15c-185">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="3c15c-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="3c15c-186">Přidá k tlačítku Odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="3c15c-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="3c15c-187">Přidá do objektu Connection obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="3c15c-188">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="3c15c-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3c15c-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c15c-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c15c-190">Stisknutím **kombinace kláves CTRL + F5** aplikaci spusťte bez ladění.</span><span class="sxs-lookup"><span data-stu-id="3c15c-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3c15c-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c15c-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3c15c-192">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3c15c-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3c15c-193">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3c15c-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c15c-194">V nabídce vyberte **spustit > spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="3c15c-195">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="3c15c-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="3c15c-196">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat zprávu** .</span><span class="sxs-lookup"><span data-stu-id="3c15c-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="3c15c-197">Název a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="3c15c-197">The name and message are displayed on both pages instantly.</span></span>

  ![Ukázková aplikace pro Signal](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="3c15c-199">Pokud aplikace nefunguje, otevřete nástroje pro vývojáře v prohlížeči (F12) a pokračujte na konzolu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="3c15c-200">Můžou se zobrazit chyby týkající se kódu HTML a JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="3c15c-201">Předpokládejme například, že umístíte *signál. js* do jiné složky než směrovaná.</span><span class="sxs-lookup"><span data-stu-id="3c15c-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="3c15c-202">V takovém případě odkaz na tento soubor nebude fungovat a v konzole se zobrazí chyba 404.</span><span class="sxs-lookup"><span data-stu-id="3c15c-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="3c15c-203">![Chyba signalizace. js nebyla nalezena.](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="3c15c-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="3c15c-204">Pokud se zobrazí chyba ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY v Chrome nebo NS_ERROR_NET_INADEQUATE_SECURITY v prohlížeči Firefox, spusťte tyto příkazy a aktualizujte svůj vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="3c15c-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome or NS_ERROR_NET_INADEQUATE_SECURITY in Firefox, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="3c15c-205">Další kroky</span><span class="sxs-lookup"><span data-stu-id="3c15c-205">Next steps</span></span>

<span data-ttu-id="3c15c-206">Další informace o signalizaci najdete v úvodu:</span><span class="sxs-lookup"><span data-stu-id="3c15c-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3c15c-207">Úvod k signalizaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c15c-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3c15c-208">V tomto kurzu se naučíte základy vytváření aplikací v reálném čase pomocí nástroje Signal.</span><span class="sxs-lookup"><span data-stu-id="3c15c-208">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="3c15c-209">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="3c15c-209">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c15c-210">Vytvoření webového projektu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-210">Create a web project.</span></span>
> * <span data-ttu-id="3c15c-211">Přidejte klientskou knihovnu signalizace.</span><span class="sxs-lookup"><span data-stu-id="3c15c-211">Add the SignalR client library.</span></span>
> * <span data-ttu-id="3c15c-212">Vytvořte centrum signalizace.</span><span class="sxs-lookup"><span data-stu-id="3c15c-212">Create a SignalR hub.</span></span>
> * <span data-ttu-id="3c15c-213">Nakonfigurujte projekt tak, aby používal signál.</span><span class="sxs-lookup"><span data-stu-id="3c15c-213">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="3c15c-214">Přidejte kód, který odesílá zprávy od libovolného klienta ke všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="3c15c-214">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="3c15c-215">Na konci budete mít funkční chatovací aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3c15c-215">At the end, you'll have a working chat app:</span></span>

![Ukázková aplikace pro Signal](signalr/_static/2.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="3c15c-217">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3c15c-217">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3c15c-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c15c-218">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3c15c-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c15c-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3c15c-220">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3c15c-220">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="3c15c-221">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="3c15c-221">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3c15c-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c15c-222">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="3c15c-223">V nabídce vyberte **soubor > nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-223">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="3c15c-224">V dialogovém okně **Nový projekt** vyberte možnost **nainstalováno > C# webové aplikace Visual > Web > ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-224">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="3c15c-225">Pojmenujte projekt *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="3c15c-225">Name the project *SignalRChat*.</span></span>

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)

* <span data-ttu-id="3c15c-227">Vyberte **Webová aplikace** a vytvořte tak projekt, který používá Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3c15c-227">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="3c15c-228">Vyberte cílovou architekturu **.NET Core**, vyberte **ASP.NET Core 2,2**a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-228">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Dialogové okno Nový projekt v aplikaci Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3c15c-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c15c-230">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="3c15c-231">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-231">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="3c15c-232">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3c15c-232">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3c15c-233">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3c15c-233">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c15c-234">V nabídce vyberte **soubor > nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-234">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="3c15c-235">Vyberte **.NET Core > App > ASP.NET Core Web App** (nevybírejte **ASP.NET Core Web App (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="3c15c-235">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="3c15c-236">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-236">Select **Next**.</span></span>

* <span data-ttu-id="3c15c-237">Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-237">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="3c15c-238">Přidat klientskou knihovnu signálů</span><span class="sxs-lookup"><span data-stu-id="3c15c-238">Add the SignalR client library</span></span>

<span data-ttu-id="3c15c-239">Knihovna serveru signalizace je obsažena v `Microsoft.AspNetCore.App` Metapackage.</span><span class="sxs-lookup"><span data-stu-id="3c15c-239">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="3c15c-240">Klientská knihovna pro JavaScript není automaticky obsažena v projektu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-240">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="3c15c-241">Pro tento kurz použijte Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="3c15c-241">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="3c15c-242">unpkg je síť pro doručování obsahu (CDN), která může doručovat cokoli, co najdete v NPM, správce balíčků Node. js.</span><span class="sxs-lookup"><span data-stu-id="3c15c-242">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3c15c-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c15c-243">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="3c15c-244">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Přidat** > **knihovnu na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-244">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="3c15c-245">V dialogovém okně **přidat knihovnu na straně klienta** vyberte pro **poskytovatele** možnost **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-245">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="3c15c-246">V případě **knihovny**zadejte `@aspnet/signalr@1`a vyberte nejnovější verzi, která není ve verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="3c15c-246">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr knihovny](signalr/_static/2.x/libman1.png)

* <span data-ttu-id="3c15c-248">Vyberte možnost **zvolit konkrétní soubory**, rozbalte složku *DIST/prohlížeč* a vyberte možnost *signaler. js* a *Signal. min. js*.</span><span class="sxs-lookup"><span data-stu-id="3c15c-248">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="3c15c-249">Nastavte **cílové umístění** na *wwwroot/lib/Signal/* a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-249">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr souborů a cíle](signalr/_static/2.x/libman2.png)

  <span data-ttu-id="3c15c-251">LibMan vytvoří složku *wwwroot/lib/signaler* a zkopíruje na ni vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="3c15c-251">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3c15c-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c15c-252">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="3c15c-253">V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="3c15c-253">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="3c15c-254">Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="3c15c-254">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="3c15c-255">Než začnete výstup zobrazovat, možná budete muset počkat několik sekund.</span><span class="sxs-lookup"><span data-stu-id="3c15c-255">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="3c15c-256">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="3c15c-256">The parameters specify the following options:</span></span>
  * <span data-ttu-id="3c15c-257">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="3c15c-257">Use the unpkg provider.</span></span>
  * <span data-ttu-id="3c15c-258">Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .</span><span class="sxs-lookup"><span data-stu-id="3c15c-258">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="3c15c-259">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="3c15c-259">Copy only the specified files.</span></span>

  <span data-ttu-id="3c15c-260">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3c15c-260">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3c15c-261">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3c15c-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c15c-262">V **terminálu**spusťte následující příkaz, kterým nainstalujete LibMan.</span><span class="sxs-lookup"><span data-stu-id="3c15c-262">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="3c15c-263">Přejděte do složky projektu (ta, která obsahuje soubor *SignalRChat. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="3c15c-263">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="3c15c-264">Spusťte následující příkaz, který načte klientskou knihovnu pro signalizaci pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="3c15c-264">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="3c15c-265">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="3c15c-265">The parameters specify the following options:</span></span>
  * <span data-ttu-id="3c15c-266">Použijte poskytovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="3c15c-266">Use the unpkg provider.</span></span>
  * <span data-ttu-id="3c15c-267">Zkopírujte soubory do cílového umístění *wwwroot/lib/Signal* .</span><span class="sxs-lookup"><span data-stu-id="3c15c-267">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="3c15c-268">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="3c15c-268">Copy only the specified files.</span></span>

  <span data-ttu-id="3c15c-269">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3c15c-269">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="3c15c-270">Vytvoření centra signalizace</span><span class="sxs-lookup"><span data-stu-id="3c15c-270">Create a SignalR hub</span></span>

<span data-ttu-id="3c15c-271">*Centrum* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="3c15c-271">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="3c15c-272">Ve složce projektu SignalRChat vytvořte složku *Centers* .</span><span class="sxs-lookup"><span data-stu-id="3c15c-272">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="3c15c-273">Ve složce *Centers* vytvořte soubor *ChatHub.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3c15c-273">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]

  <span data-ttu-id="3c15c-274">Třída dědí z `Hub` třídy signaler. `ChatHub`</span><span class="sxs-lookup"><span data-stu-id="3c15c-274">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="3c15c-275">`Hub` Třída spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="3c15c-275">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="3c15c-276">`SendMessage` Metoda může být volána připojeným klientem pro odeslání zprávy všem klientům.</span><span class="sxs-lookup"><span data-stu-id="3c15c-276">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="3c15c-277">JavaScriptový kód klienta, který volá metodu, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-277">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="3c15c-278">Kód signalizace je asynchronní pro zajištění maximální škálovatelnosti.</span><span class="sxs-lookup"><span data-stu-id="3c15c-278">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="3c15c-279">Konfigurovat signál</span><span class="sxs-lookup"><span data-stu-id="3c15c-279">Configure SignalR</span></span>

<span data-ttu-id="3c15c-280">Server signalizace musí být nakonfigurován tak, aby předával požadavky na signalizaci.</span><span class="sxs-lookup"><span data-stu-id="3c15c-280">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="3c15c-281">Do souboru *Startup.cs* přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="3c15c-281">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="3c15c-282">Tyto změny přidávají signál do systému vkládání závislostí ASP.NET Core a do kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="3c15c-282">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="3c15c-283">Přidat kód klienta signalizace</span><span class="sxs-lookup"><span data-stu-id="3c15c-283">Add SignalR client code</span></span>

* <span data-ttu-id="3c15c-284">Nahraďte obsah v *Pages\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3c15c-284">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]

  <span data-ttu-id="3c15c-285">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="3c15c-285">The preceding code:</span></span>

  * <span data-ttu-id="3c15c-286">Vytvoří textová pole pro text jméno a zprávu a tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="3c15c-286">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="3c15c-287">Vytvoří seznam s nástrojem `id="messagesList"` pro zobrazení zpráv, které jsou přijímány z centra signalizace.</span><span class="sxs-lookup"><span data-stu-id="3c15c-287">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="3c15c-288">Obsahuje odkazy na skripty pro signál a kód aplikace *chat. js* , který vytvoříte v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="3c15c-288">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="3c15c-289">Ve složce *wwwroot/js* vytvořte soubor *chat. js* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3c15c-289">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]

  <span data-ttu-id="3c15c-290">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="3c15c-290">The preceding code:</span></span>

  * <span data-ttu-id="3c15c-291">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="3c15c-291">Creates and starts a connection.</span></span>
  * <span data-ttu-id="3c15c-292">Přidá k tlačítku Odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="3c15c-292">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="3c15c-293">Přidá do objektu Connection obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-293">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="3c15c-294">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="3c15c-294">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3c15c-295">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c15c-295">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3c15c-296">Stisknutím **kombinace kláves CTRL + F5** aplikaci spusťte bez ladění.</span><span class="sxs-lookup"><span data-stu-id="3c15c-296">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3c15c-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c15c-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3c15c-298">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3c15c-298">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3c15c-299">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3c15c-299">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3c15c-300">V nabídce vyberte **spustit > spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="3c15c-300">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="3c15c-301">Zkopírujte adresu URL z panelu Adresa, otevřete jinou instanci nebo kartu prohlížeče a vložte adresu URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="3c15c-301">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="3c15c-302">Zvolte buď prohlížeč, zadejte jméno a zprávu a klikněte na tlačítko **Odeslat zprávu** .</span><span class="sxs-lookup"><span data-stu-id="3c15c-302">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="3c15c-303">Název a zpráva se okamžitě zobrazí na obou stránkách.</span><span class="sxs-lookup"><span data-stu-id="3c15c-303">The name and message are displayed on both pages instantly.</span></span>

  ![Ukázková aplikace pro Signal](signalr/_static/2.x/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="3c15c-305">Pokud aplikace nefunguje, otevřete nástroje pro vývojáře v prohlížeči (F12) a pokračujte na konzolu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-305">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="3c15c-306">Můžou se zobrazit chyby týkající se kódu HTML a JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="3c15c-306">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="3c15c-307">Předpokládejme například, že umístíte *signál. js* do jiné složky než směrovaná.</span><span class="sxs-lookup"><span data-stu-id="3c15c-307">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="3c15c-308">V takovém případě odkaz na tento soubor nebude fungovat a v konzole se zobrazí chyba 404.</span><span class="sxs-lookup"><span data-stu-id="3c15c-308">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="3c15c-309">![Chyba signalizace. js nebyla nalezena.](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="3c15c-309">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c15c-310">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3c15c-310">Additional resources</span></span>

* [<span data-ttu-id="3c15c-311">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="3c15c-311">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)

## <a name="next-steps"></a><span data-ttu-id="3c15c-312">Další kroky</span><span class="sxs-lookup"><span data-stu-id="3c15c-312">Next steps</span></span>

<span data-ttu-id="3c15c-313">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="3c15c-313">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c15c-314">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="3c15c-314">Create a web app project.</span></span>
> * <span data-ttu-id="3c15c-315">Přidejte klientskou knihovnu signalizace.</span><span class="sxs-lookup"><span data-stu-id="3c15c-315">Add the SignalR client library.</span></span>
> * <span data-ttu-id="3c15c-316">Vytvořte centrum signalizace.</span><span class="sxs-lookup"><span data-stu-id="3c15c-316">Create a SignalR hub.</span></span>
> * <span data-ttu-id="3c15c-317">Nakonfigurujte projekt tak, aby používal signál.</span><span class="sxs-lookup"><span data-stu-id="3c15c-317">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="3c15c-318">Přidejte kód, který používá centrum k posílání zpráv od libovolného klienta ke všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="3c15c-318">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="3c15c-319">Další informace o signalizaci najdete v úvodu:</span><span class="sxs-lookup"><span data-stu-id="3c15c-319">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3c15c-320">Úvod k signalizaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c15c-320">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end
