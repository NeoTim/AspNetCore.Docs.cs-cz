---
title: Začínáme s ASP.NET CoreSignalR
author: bradygaster
description: V tomto kurzu vytvoříte chatovací aplikaci, která používá ASP.NET Core SignalR.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 869eb325ee95a78e4b16c61c5b0573bb094292e3
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994623"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="78300-103">Kurz: Začínáme s ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="78300-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78300-104">Tento kurz učí základy vytváření aplikace v reálném čase pomocí SignalR.</span><span class="sxs-lookup"><span data-stu-id="78300-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="78300-105">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="78300-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="78300-106">Vytvořte webový projekt.</span><span class="sxs-lookup"><span data-stu-id="78300-106">Create a web project.</span></span>
> * <span data-ttu-id="78300-107">Přidejte klientskou knihovnu SignalR.</span><span class="sxs-lookup"><span data-stu-id="78300-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="78300-108">Vytvořte rozbočovač SignalR.</span><span class="sxs-lookup"><span data-stu-id="78300-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="78300-109">Nakonfigurujte projekt tak, aby používal SignalR.</span><span class="sxs-lookup"><span data-stu-id="78300-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="78300-110">Přidejte kód, který odesílá zprávy z libovolného klienta všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="78300-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="78300-111">Na konci budete mít funkční chatovací aplikaci:</span><span class="sxs-lookup"><span data-stu-id="78300-111">At the end, you'll have a working chat app:</span></span>

![Ukázková aplikace SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="78300-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="78300-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="78300-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78300-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="78300-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78300-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78300-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="78300-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="78300-117">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="78300-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="78300-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78300-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="78300-119">V nabídce vyberte **Soubor > Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="78300-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="78300-120">V **dialogovém** okně Vytvořit nový projekt vyberte **ASP.NET Základní webová aplikace**a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="78300-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="78300-121">V dialogovém **okně Konfigurovat nový projekt pojmenujte** projekt *SignalRChat*a pak vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="78300-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="78300-122">V **dialogovém okně Vytvořit novou ASP.NET základní webovou aplikaci** vyberte **.NET Core** a **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="78300-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="78300-123">Výběrem **možnosti Webová aplikace** vytvořte projekt, který používá Razor Pages, a pak vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="78300-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Dialogové okno Nový projekt v sadě Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="78300-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78300-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="78300-126">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="78300-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="78300-127">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="78300-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78300-128">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="78300-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="78300-129">V nabídce vyberte **Soubor > nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="78300-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="78300-130">Vyberte **> aplikace > webové aplikace (Nevybírejte** **webovou aplikaci (Model-View-Controller)** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="78300-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="78300-131">Zkontrolujte, zda je **cílová architektura** nastavena na **.NET Core 3.0**, a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="78300-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="78300-132">Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="78300-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="78300-133">Přidání klientské knihovny SignalR</span><span class="sxs-lookup"><span data-stu-id="78300-133">Add the SignalR client library</span></span>

<span data-ttu-id="78300-134">Knihovna serveru SignalR je součástí ASP.NET sdíleného rozhraní Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="78300-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="78300-135">Klientská knihovna JavaScriptu není automaticky zahrnuta do projektu.</span><span class="sxs-lookup"><span data-stu-id="78300-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="78300-136">V tomto kurzu pomocí Správce knihovny (LibMan) získat klientskou knihovnu z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="78300-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="78300-137">unpkg je síť pro doručování obsahu (CDN), která může dodat cokoli, co se nachází v npm, správce balíčků Node.js.</span><span class="sxs-lookup"><span data-stu-id="78300-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="78300-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78300-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="78300-139">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a vyberte **přidat** > **knihovnu na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="78300-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="78300-140">V dialogovém okně **Přidat knihovnu na straně klienta** vyberte pro **zprostředkovatele** **hodnotu nepkg**.</span><span class="sxs-lookup"><span data-stu-id="78300-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="78300-141">Do **knihovny**zadejte . `@microsoft/signalr@latest`</span><span class="sxs-lookup"><span data-stu-id="78300-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="78300-142">Vyberte **Vybrat konkrétní soubory**, rozbalte složku *dist/browser* a vyberte *signalr.js* a *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="78300-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="78300-143">Nastavte **cílové umístění** na *wwwroot/js/signalr/* a vyberte **Instalovat**.</span><span class="sxs-lookup"><span data-stu-id="78300-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Dialogové okno Přidat knihovnu na straně klienta – vybrat knihovnu](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="78300-145">LibMan vytvoří složku *wwwroot/js/signalr* a zkopíruje do ní vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="78300-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="78300-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78300-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="78300-147">V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="78300-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="78300-148">Spusťte následující příkaz, abyste získali klientskou knihovnu SignalR pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="78300-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="78300-149">Možná budete muset počkat několik sekund, než se zzobrazuje výstup.</span><span class="sxs-lookup"><span data-stu-id="78300-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="78300-150">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="78300-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="78300-151">Použijte zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="78300-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="78300-152">Zkopírujte soubory do cíle *wwwroot/js/signalr.*</span><span class="sxs-lookup"><span data-stu-id="78300-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="78300-153">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="78300-153">Copy only the specified files.</span></span>

  <span data-ttu-id="78300-154">Výstup vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="78300-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78300-155">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="78300-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="78300-156">V **terminálu**spusťte následující příkaz pro instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="78300-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="78300-157">Přejděte do složky projektu (ten, který obsahuje soubor *SignalRChat.csproj).*</span><span class="sxs-lookup"><span data-stu-id="78300-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="78300-158">Spusťte následující příkaz, abyste získali klientskou knihovnu SignalR pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="78300-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="78300-159">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="78300-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="78300-160">Použijte zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="78300-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="78300-161">Zkopírujte soubory do cíle *wwwroot/js/signalr.*</span><span class="sxs-lookup"><span data-stu-id="78300-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="78300-162">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="78300-162">Copy only the specified files.</span></span>

  <span data-ttu-id="78300-163">Výstup vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="78300-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="78300-164">Vytvoření rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="78300-164">Create a SignalR hub</span></span>

<span data-ttu-id="78300-165">*Rozbočovač* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="78300-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="78300-166">Ve složce projektu SignalRChat vytvořte složku *Hubs.*</span><span class="sxs-lookup"><span data-stu-id="78300-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="78300-167">Ve složce *Rozbočovače* vytvořte *soubor ChatHub.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="78300-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="78300-168">Třída `ChatHub` dědí z SignalR `Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="78300-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="78300-169">Třída `Hub` spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="78300-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="78300-170">Metodu `SendMessage` může volat připojený klient a odeslat zprávu všem klientům.</span><span class="sxs-lookup"><span data-stu-id="78300-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="78300-171">JavaScript klientský kód, který volá metodu je zobrazen později v kurzu.</span><span class="sxs-lookup"><span data-stu-id="78300-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="78300-172">SignalR kód je asynchronní poskytnout maximální škálovatelnost.</span><span class="sxs-lookup"><span data-stu-id="78300-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="78300-173">Konfigurace signalismu</span><span class="sxs-lookup"><span data-stu-id="78300-173">Configure SignalR</span></span>

<span data-ttu-id="78300-174">Server SignalR musí být nakonfigurován tak, aby předává požadavky SignalR signalr.</span><span class="sxs-lookup"><span data-stu-id="78300-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="78300-175">Přidejte do *souboru Startup.cs* následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="78300-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="78300-176">Tyto změny přidat SignalR do ASP.NET systémy vkládání závislostí jádra a směrování.</span><span class="sxs-lookup"><span data-stu-id="78300-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="78300-177">Přidat klientský kód SignalR</span><span class="sxs-lookup"><span data-stu-id="78300-177">Add SignalR client code</span></span>

* <span data-ttu-id="78300-178">Nahraďte obsah *stránky\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="78300-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="78300-179">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="78300-179">The preceding code:</span></span>

  * <span data-ttu-id="78300-180">Vytvoří textová pole pro text názvu a zprávy a tlačítko odeslat.</span><span class="sxs-lookup"><span data-stu-id="78300-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="78300-181">Vytvoří seznam `id="messagesList"` s pro zobrazení zpráv, které jsou přijímány z rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="78300-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="78300-182">Zahrnuje odkazy na skripty SignalR a kód aplikace *chat.js,* který vytvoříte v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="78300-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="78300-183">Ve složce *wwwroot/js* vytvořte soubor *chat.js* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="78300-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="78300-184">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="78300-184">The preceding code:</span></span>

  * <span data-ttu-id="78300-185">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="78300-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="78300-186">Přidá k tlačítku odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="78300-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="78300-187">Přidá k objektu připojení obslužnou rutinu, která přijímá zprávy z rozbočovače a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="78300-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="78300-188">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="78300-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="78300-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78300-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="78300-190">Stisknutím **kláves CTRL+F5** spusťte aplikaci bez ladění.</span><span class="sxs-lookup"><span data-stu-id="78300-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="78300-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78300-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="78300-192">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="78300-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78300-193">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="78300-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="78300-194">V nabídce vyberte **Spustit > start bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="78300-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="78300-195">Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="78300-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="78300-196">Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat zprávu.**</span><span class="sxs-lookup"><span data-stu-id="78300-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="78300-197">Název a zpráva se zobrazí na obou stránkách okamžitě.</span><span class="sxs-lookup"><span data-stu-id="78300-197">The name and message are displayed on both pages instantly.</span></span>

  ![Ukázková aplikace SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="78300-199">Pokud aplikace nefunguje, otevřete nástroje pro vývojáře prohlížeče (F12) a přejděte do konzole.</span><span class="sxs-lookup"><span data-stu-id="78300-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="78300-200">Mohou se zobrazit chyby související s kódem HTML a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78300-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="78300-201">Předpokládejme například, že vložíte *soubor signalr.js* do jiné složky, než bylo nařízeno.</span><span class="sxs-lookup"><span data-stu-id="78300-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="78300-202">V takovém případě nebude odkaz na tento soubor fungovat a v konzole se zobrazí chyba 404.</span><span class="sxs-lookup"><span data-stu-id="78300-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="78300-203">![soubor signalr.js nebyl nalezen chyba](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="78300-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="78300-204">Pokud se v Chromu zobrazí chyba ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY, aktualizujte vývojový certifikát těmito příkazy:</span><span class="sxs-lookup"><span data-stu-id="78300-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="78300-205">Tento kurz učí základy vytváření aplikace v reálném čase pomocí SignalR.</span><span class="sxs-lookup"><span data-stu-id="78300-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="78300-206">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="78300-206">You learn how to:</span></span>   

> [!div class="checklist"]  
> * <span data-ttu-id="78300-207">Vytvořte webový projekt.</span><span class="sxs-lookup"><span data-stu-id="78300-207">Create a web project.</span></span>   
> * <span data-ttu-id="78300-208">Přidejte klientskou knihovnu SignalR.</span><span class="sxs-lookup"><span data-stu-id="78300-208">Add the SignalR client library.</span></span> 
> * <span data-ttu-id="78300-209">Vytvořte rozbočovač SignalR.</span><span class="sxs-lookup"><span data-stu-id="78300-209">Create a SignalR hub.</span></span>   
> * <span data-ttu-id="78300-210">Nakonfigurujte projekt tak, aby používal SignalR.</span><span class="sxs-lookup"><span data-stu-id="78300-210">Configure the project to use SignalR.</span></span>   
> * <span data-ttu-id="78300-211">Přidejte kód, který odesílá zprávy z libovolného klienta všem připojeným klientům.</span><span class="sxs-lookup"><span data-stu-id="78300-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="78300-212">Na konci budete mít funkční chatovou ![aplikaci: Ukázková aplikace SignalR](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="78300-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

## <a name="prerequisites"></a><span data-ttu-id="78300-213">Požadavky</span><span class="sxs-lookup"><span data-stu-id="78300-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="78300-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78300-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="78300-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78300-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78300-216">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="78300-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="78300-217">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="78300-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="78300-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78300-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="78300-219">V nabídce vyberte **Soubor > Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="78300-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="78300-220">V dialogovém okně **Nový projekt** vyberte **možnost Nainstalováno > jazyce Visual C# > Web > ASP.NET základní webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="78300-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="78300-221">Název projektu *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="78300-221">Name the project *SignalRChat*.</span></span> 

  ![Dialogové okno Nový projekt v sadě Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="78300-223">Vyberte **webovou aplikaci** a vytvořte projekt, který používá Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="78300-223">Select **Web Application** to create a project that uses Razor Pages.</span></span> 

* <span data-ttu-id="78300-224">Vyberte cílovou architekturu **rozhraní .NET Core**, vyberte **ASP.NET Core 2.2**a klepněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="78300-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Dialogové okno Nový projekt v sadě Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="78300-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78300-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="78300-227">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve které bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="78300-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="78300-228">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="78300-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78300-229">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="78300-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="78300-230">V nabídce vyberte **Soubor > nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="78300-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="78300-231">Vyberte **> > .NET Core > App ASP.NET Core Web App** (Nevybírejte ASP.NET Core Web App **(MVC).**</span><span class="sxs-lookup"><span data-stu-id="78300-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="78300-232">Vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="78300-232">Select **Next**.</span></span>  

* <span data-ttu-id="78300-233">Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="78300-233">Name the project *SignalRChat*, and then select **Create**.</span></span>   

--- 

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="78300-234">Přidání klientské knihovny SignalR</span><span class="sxs-lookup"><span data-stu-id="78300-234">Add the SignalR client library</span></span>   

<span data-ttu-id="78300-235">Knihovna serveru SignalR je `Microsoft.AspNetCore.App` součástí metabalíčku.</span><span class="sxs-lookup"><span data-stu-id="78300-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="78300-236">Klientská knihovna JavaScriptu není automaticky zahrnuta do projektu.</span><span class="sxs-lookup"><span data-stu-id="78300-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="78300-237">V tomto kurzu pomocí Správce knihovny (LibMan) získat klientskou knihovnu z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="78300-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="78300-238">unpkg je síť pro doručování obsahu (CDN), která může dodat cokoli, co se nachází v npm, správce balíčků Node.js.</span><span class="sxs-lookup"><span data-stu-id="78300-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="78300-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78300-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="78300-240">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a vyberte **přidat** > **knihovnu na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="78300-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="78300-241">V dialogovém okně **Přidat knihovnu na straně klienta** vyberte pro **zprostředkovatele** **hodnotu nepkg**.</span><span class="sxs-lookup"><span data-stu-id="78300-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="78300-242">Do **knihovny**zadejte `@microsoft/signalr@3`a vyberte nejnovější verzi, která není v náhledu.</span><span class="sxs-lookup"><span data-stu-id="78300-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Dialogové okno Přidat knihovnu na straně klienta – vybrat knihovnu](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="78300-244">Vyberte **Vybrat konkrétní soubory**, rozbalte složku *dist/browser* a vyberte *signalr.js* a *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="78300-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="78300-245">Nastavte **cílové umístění** na *wwwroot/lib/signalr/* a vyberte **Instalovat**.</span><span class="sxs-lookup"><span data-stu-id="78300-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Dialogové okno Přidat knihovnu na straně klienta – výběr souborů a cíl](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="78300-247">LibMan vytvoří složku *wwwroot/lib/signalr* a zkopíruje do ní vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="78300-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="78300-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78300-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="78300-249">V integrovaném terminálu spusťte následující příkaz pro instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="78300-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="78300-250">Spusťte následující příkaz, abyste získali klientskou knihovnu SignalR pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="78300-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="78300-251">Možná budete muset počkat několik sekund, než se zzobrazuje výstup.</span><span class="sxs-lookup"><span data-stu-id="78300-251">You might have to wait a few seconds before seeing output.</span></span>   

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="78300-252">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="78300-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="78300-253">Použijte zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="78300-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="78300-254">Zkopírujte soubory do cíle *wwwroot/lib/signalr.*</span><span class="sxs-lookup"><span data-stu-id="78300-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="78300-255">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="78300-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="78300-256">Výstup vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="78300-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78300-257">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="78300-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="78300-258">V **terminálu**spusťte následující příkaz pro instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="78300-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="78300-259">Přejděte do složky projektu (ten, který obsahuje soubor *SignalRChat.csproj).*</span><span class="sxs-lookup"><span data-stu-id="78300-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span> 

* <span data-ttu-id="78300-260">Spusťte následující příkaz, abyste získali klientskou knihovnu SignalR pomocí LibMan.</span><span class="sxs-lookup"><span data-stu-id="78300-260">Run the following command to get the SignalR client library by using LibMan.</span></span>  

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="78300-261">Parametry určují následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="78300-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="78300-262">Použijte zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="78300-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="78300-263">Zkopírujte soubory do cíle *wwwroot/lib/signalr.*</span><span class="sxs-lookup"><span data-stu-id="78300-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="78300-264">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="78300-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="78300-265">Výstup vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="78300-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a><span data-ttu-id="78300-266">Vytvoření rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="78300-266">Create a SignalR hub</span></span> 

<span data-ttu-id="78300-267">*Rozbočovač* je třída, která slouží jako kanál vysoké úrovně, který zpracovává komunikaci mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="78300-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="78300-268">Ve složce projektu SignalRChat vytvořte složku *Hubs.*</span><span class="sxs-lookup"><span data-stu-id="78300-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>    

* <span data-ttu-id="78300-269">Ve složce *Rozbočovače* vytvořte *soubor ChatHub.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="78300-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="78300-270">Třída `ChatHub` dědí z SignalR `Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="78300-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="78300-271">Třída `Hub` spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="78300-271">The `Hub` class manages connections, groups, and messaging.</span></span>    

  <span data-ttu-id="78300-272">Metodu `SendMessage` může volat připojený klient a odeslat zprávu všem klientům.</span><span class="sxs-lookup"><span data-stu-id="78300-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="78300-273">JavaScript klientský kód, který volá metodu je zobrazen později v kurzu.</span><span class="sxs-lookup"><span data-stu-id="78300-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="78300-274">SignalR kód je asynchronní poskytnout maximální škálovatelnost.</span><span class="sxs-lookup"><span data-stu-id="78300-274">SignalR code is asynchronous to provide maximum scalability.</span></span>  

## <a name="configure-signalr"></a><span data-ttu-id="78300-275">Konfigurace signalismu</span><span class="sxs-lookup"><span data-stu-id="78300-275">Configure SignalR</span></span>    

<span data-ttu-id="78300-276">Server SignalR musí být nakonfigurován tak, aby předává požadavky SignalR signalr.</span><span class="sxs-lookup"><span data-stu-id="78300-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>  

* <span data-ttu-id="78300-277">Přidejte do *souboru Startup.cs* následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="78300-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="78300-278">Tyto změny přidat SignalR do systému vkládání závislostí ASP.NET jádra a middleware kanálu.</span><span class="sxs-lookup"><span data-stu-id="78300-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>    

## <a name="add-signalr-client-code"></a><span data-ttu-id="78300-279">Přidat klientský kód SignalR</span><span class="sxs-lookup"><span data-stu-id="78300-279">Add SignalR client code</span></span>  

* <span data-ttu-id="78300-280">Nahraďte obsah *stránky\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="78300-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="78300-281">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="78300-281">The preceding code:</span></span>   

  * <span data-ttu-id="78300-282">Vytvoří textová pole pro text názvu a zprávy a tlačítko odeslat.</span><span class="sxs-lookup"><span data-stu-id="78300-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="78300-283">Vytvoří seznam `id="messagesList"` s pro zobrazení zpráv, které jsou přijímány z rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="78300-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span> 
  * <span data-ttu-id="78300-284">Zahrnuje odkazy na skripty SignalR a kód aplikace *chat.js,* který vytvoříte v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="78300-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>  

* <span data-ttu-id="78300-285">Ve složce *wwwroot/js* vytvořte soubor *chat.js* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="78300-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="78300-286">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="78300-286">The preceding code:</span></span>   

  * <span data-ttu-id="78300-287">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="78300-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="78300-288">Přidá k tlačítku odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="78300-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="78300-289">Přidá k objektu připojení obslužnou rutinu, která přijímá zprávy z rozbočovače a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="78300-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="78300-290">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="78300-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="78300-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78300-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="78300-292">Stisknutím **kláves CTRL+F5** spusťte aplikaci bez ladění.</span><span class="sxs-lookup"><span data-stu-id="78300-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="78300-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78300-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="78300-294">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="78300-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78300-295">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="78300-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="78300-296">V nabídce vyberte **Spustit > start bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="78300-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="78300-297">Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo kartu a vložte ji do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="78300-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="78300-298">Zvolte některý z prohlížečů, zadejte jméno a zprávu a vyberte tlačítko **Odeslat zprávu.**</span><span class="sxs-lookup"><span data-stu-id="78300-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="78300-299">Název a zpráva se zobrazí na obou stránkách okamžitě.</span><span class="sxs-lookup"><span data-stu-id="78300-299">The name and message are displayed on both pages instantly.</span></span>   

  <span data-ttu-id="78300-300">![SignalRukázková aplikace](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="78300-300">![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

> [!TIP]    
> <span data-ttu-id="78300-301">Pokud aplikace nefunguje, otevřete nástroje pro vývojáře prohlížeče (F12) a přejděte do konzole.</span><span class="sxs-lookup"><span data-stu-id="78300-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="78300-302">Mohou se zobrazit chyby související s kódem HTML a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="78300-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="78300-303">Předpokládejme například, že vložíte *soubor signalr.js* do jiné složky, než bylo nařízeno.</span><span class="sxs-lookup"><span data-stu-id="78300-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="78300-304">V takovém případě nebude odkaz na tento soubor fungovat a v konzole se zobrazí chyba 404.</span><span class="sxs-lookup"><span data-stu-id="78300-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="78300-305">![soubor signalr.js nebyl nalezen chyba](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="78300-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="78300-306">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="78300-306">Additional resources</span></span> 
* [<span data-ttu-id="78300-307">Youtube verze tohoto výukového programu</span><span class="sxs-lookup"><span data-stu-id="78300-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
