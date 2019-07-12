---
title: Začínáme s knihovnou SignalR technologie ASP.NET Core
author: bradygaster
description: V tomto kurzu vytvoříte aplikaci chatu, který používá funkce SignalR technologie ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 07/08/2019
uid: tutorials/signalr
ms.openlocfilehash: 5588cab1ebbe8fbd6d134025f07f9580ecafd89f
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855794"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="ba9ad-103">Kurz: Začínáme s knihovnou SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba9ad-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba9ad-104">V tomto kurzu se naučíte se základy vytváření aplikace v reálném čase pomocí nástroje SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="ba9ad-105">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ba9ad-106">Vytvoření webového projektu.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-106">Create a web project.</span></span>
> * <span data-ttu-id="ba9ad-107">Přidání knihovny klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="ba9ad-108">Vytvoření rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="ba9ad-109">Nakonfigurujte projekt tak, aby používaly SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="ba9ad-110">Přidejte kód, který odesílá zprávy z libovolného klienta na všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="ba9ad-111">Na konci budete mít funkční aplikaci chatu:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-111">At the end, you'll have a working chat app:</span></span>

![Ukázková aplikace SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="ba9ad-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ba9ad-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ba9ad-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9ad-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ba9ad-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba9ad-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ba9ad-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ba9ad-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="ba9ad-117">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="ba9ad-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ba9ad-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9ad-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="ba9ad-119">V nabídce vyberte **soubor > Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="ba9ad-120">V **vytvořte nový projekt** dialogového okna, vyberte **webové aplikace ASP.NET Core**a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="ba9ad-121">V **konfigurovat nový projekt** dialogového okna, název projektu *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="ba9ad-122">V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, vyberte **.NET Core** a **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-122">In the **Create a new ASP.NET Core Web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="ba9ad-123">Vyberte **webovou aplikaci** vytvořit projekt, který se používá pro stránky Razor, a potom vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Dialogové okno nového projektu v sadě Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ba9ad-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba9ad-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="ba9ad-126">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve kterém bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="ba9ad-127">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-127">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ba9ad-128">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ba9ad-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ba9ad-129">V nabídce vyberte **soubor > Nový řešení**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="ba9ad-130">Vyberte **.NET Core > aplikace > Webová aplikace** (nevybírejte **webové aplikace (Model-View-Controller)** ) a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="ba9ad-131">Ujistěte se, že **Cílová architektura** je nastavena na **.NET Core 3.0**a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="ba9ad-132">Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="ba9ad-133">Přidat klientské knihovně SignalR</span><span class="sxs-lookup"><span data-stu-id="ba9ad-133">Add the SignalR client library</span></span>

<span data-ttu-id="ba9ad-134">Server knihovny SignalR je součástí sdíleného rozhraní ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="ba9ad-135">Knihovny JavaScript klienta není automaticky zahrnut v projektu.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="ba9ad-136">V tomto kurzu použijete Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="ba9ad-137">unpkg je síť pro doručování obsahu (CDN)), která může doručovat cokoli, co je součástí npm, Správce balíčků Node.js.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ba9ad-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9ad-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="ba9ad-139">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **knihoven na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="ba9ad-140">V **přidat knihovnu na straně klienta** dialogovém okně pro **poskytovatele** vyberte **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="ba9ad-141">Pro **knihovny**, zadejte `@aspnet/signalr@next`.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-141">For **Library**, enter `@aspnet/signalr@next`.</span></span>
<!-- when 3.0 is released, change @next to @latest -->

* <span data-ttu-id="ba9ad-142">Vyberte **zvolte konkrétní soubory**, rozbalte *dist a prohlížeče* a pak zvolte položku *signalr.js* a *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="ba9ad-143">Nastavte **cílové umístění** k *wwwroot/lib/signalr/* a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-143">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Přidat dialog knihoven na straně klienta - knihovně](signalr/_static/3.x/libman1.png)

  <span data-ttu-id="ba9ad-145">LibMan vytvoří *wwwroot/lib/signalr* složky a kopie vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-145">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ba9ad-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba9ad-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="ba9ad-147">V integrovaném terminálu spusťte následující příkaz k instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="ba9ad-148">Spusťte následující příkaz s použitím LibMan získat klientské knihovně SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="ba9ad-149">Budete muset počkat několik sekund, než výstup.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="ba9ad-150">Parametry určete následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="ba9ad-151">Použití zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="ba9ad-152">Kopírovat soubory *wwwroot/lib/signalr* cíl.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-152">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="ba9ad-153">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-153">Copy only the specified files.</span></span>

  <span data-ttu-id="ba9ad-154">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ba9ad-155">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ba9ad-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ba9ad-156">V **terminálu**, spusťte následující příkaz k instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="ba9ad-157">Přejděte do složky projektu (ten, který obsahuje *SignalRChat.csproj* souboru).</span><span class="sxs-lookup"><span data-stu-id="ba9ad-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="ba9ad-158">Spusťte následující příkaz s použitím LibMan získat klientské knihovně SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="ba9ad-159">Parametry určete následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="ba9ad-160">Použití zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="ba9ad-161">Kopírovat soubory *wwwroot/lib/signalr* cíl.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-161">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="ba9ad-162">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-162">Copy only the specified files.</span></span>

  <span data-ttu-id="ba9ad-163">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="ba9ad-164">Vytvoření rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="ba9ad-164">Create a SignalR hub</span></span>

<span data-ttu-id="ba9ad-165">A *centra* je třída, která slouží jako základní kanál, který zpracovává vnitřní komunikaci klient server.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="ba9ad-166">Ve složce projektu SignalRChat vytvořit *rozbočovače* složky.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="ba9ad-167">V *rozbočovače* složku, vytvořte *ChatHub.cs* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="ba9ad-168">`ChatHub` Třída dědí z funkce SignalR `Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="ba9ad-169">`Hub` Třída spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="ba9ad-170">`SendMessage` Metodu je možné vyvolat v připojených klientovi umožní odeslat zprávu pro všechny klienty.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="ba9ad-171">Klientský kód jazyka JavaScript, který volá metodu se zobrazí v pozdější části kurzu.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="ba9ad-172">Kód SignalR je asynchronní zajistit maximální škálovatelnosti.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="ba9ad-173">Konfigurace funkce SignalR</span><span class="sxs-lookup"><span data-stu-id="ba9ad-173">Configure SignalR</span></span>

<span data-ttu-id="ba9ad-174">Na serveru funkce SignalR nastavené předat požadavky SignalR SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="ba9ad-175">Přidejte následující zvýrazněný kód do *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=6,30,58)]

  <span data-ttu-id="ba9ad-176">Těmito změnami se přidá SignalR pro vkládání závislostí ASP.NET Core a směrování systémy.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="ba9ad-177">Přidejte kód klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="ba9ad-177">Add SignalR client code</span></span>

* <span data-ttu-id="ba9ad-178">Nahraďte obsah *Pages\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="ba9ad-179">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-179">The preceding code:</span></span>

  * <span data-ttu-id="ba9ad-180">Vytvoří textová pole pro název a text zprávy a tlačítka Odeslat.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="ba9ad-181">Vytvoří seznam s `id="messagesList"` pro zobrazení zprávy, které byly přijaty z rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="ba9ad-182">Obsahuje odkazy na skript ke knihovně SignalR a *chat.js* aplikační kód, který vytvoříte v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="ba9ad-183">V *wwwroot/js* složku, vytvořte *chat.js* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="ba9ad-184">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-184">The preceding code:</span></span>

  * <span data-ttu-id="ba9ad-185">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="ba9ad-186">Přidá tlačítko Odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="ba9ad-187">Přidá objekt připojení obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="ba9ad-188">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="ba9ad-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ba9ad-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9ad-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ba9ad-190">Stisknutím klávesy **CTRL + F5** a spusťte tak aplikaci bez ladění.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ba9ad-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba9ad-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ba9ad-192">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-192">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ba9ad-193">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ba9ad-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ba9ad-194">V nabídce vyberte **spuštění > Spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="ba9ad-195">Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo karty a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="ba9ad-196">Zvolte buď prohlížeče, zadejte název a zprávu a vybrat **poslat zprávu** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="ba9ad-197">Název a zpráva se zobrazí na obě stránky okamžitě.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-197">The name and message are displayed on both pages instantly.</span></span>

  ![Ukázková aplikace SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="ba9ad-199">Pokud aplikace nebude fungovat, otevřete prohlížeč vývojářské nástroje (F12) a přejděte do konzoly.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="ba9ad-200">Můžou se zobrazovat chyby související s kódem HTML a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="ba9ad-201">Předpokládejme například, kam si ukládáte *signalr.js* v jiné složce než řízené.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="ba9ad-202">V takovém případě nebude fungovat odkaz na tento soubor a uvidíte v konzole chybu 404.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="ba9ad-203">![Chyba nenalezení signalr.js](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="ba9ad-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="ba9ad-204">Pokud se zobrazí chyba ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY v prohlížeči Chrome nebo NS_ERROR_NET_INADEQUATE_SECURITY ve Firefoxu, spusťte tyto příkazy se aktualizovat svůj vývojářský certifikát:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome or NS_ERROR_NET_INADEQUATE_SECURITY in Firefox, run these commands to update your development certificate:</span></span>
>   ```
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="ba9ad-205">Další postup</span><span class="sxs-lookup"><span data-stu-id="ba9ad-205">Next steps</span></span>

<span data-ttu-id="ba9ad-206">Další informace o funkci SignalR naleznete v úvodu:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ba9ad-207">Úvod do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="ba9ad-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba9ad-208">V tomto kurzu se naučíte se základy vytváření aplikace v reálném čase pomocí nástroje SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-208">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="ba9ad-209">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-209">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ba9ad-210">Vytvoření webového projektu.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-210">Create a web project.</span></span>
> * <span data-ttu-id="ba9ad-211">Přidání knihovny klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-211">Add the SignalR client library.</span></span>
> * <span data-ttu-id="ba9ad-212">Vytvoření rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-212">Create a SignalR hub.</span></span>
> * <span data-ttu-id="ba9ad-213">Nakonfigurujte projekt tak, aby používaly SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-213">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="ba9ad-214">Přidejte kód, který odesílá zprávy z libovolného klienta na všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-214">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="ba9ad-215">Na konci budete mít funkční aplikaci chatu:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-215">At the end, you'll have a working chat app:</span></span>

![Ukázková aplikace SignalR](signalr/_static/2.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="ba9ad-217">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ba9ad-217">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ba9ad-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9ad-218">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ba9ad-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba9ad-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ba9ad-220">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ba9ad-220">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="ba9ad-221">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="ba9ad-221">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ba9ad-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9ad-222">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="ba9ad-223">V nabídce vyberte **soubor > Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-223">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="ba9ad-224">V **nový projekt** dialogového okna, vyberte **nainstalováno > Visual C# > Web > Webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-224">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="ba9ad-225">Pojmenujte projekt *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-225">Name the project *SignalRChat*.</span></span>

  ![Dialogové okno nového projektu v sadě Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)

* <span data-ttu-id="ba9ad-227">Vyberte **webovou aplikaci** vytvořit projekt, který se používá pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-227">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="ba9ad-228">Vyberte cílovou architekturu **.NET Core**vyberte **2.2 technologie ASP.NET Core**a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-228">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Dialogové okno nového projektu v sadě Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ba9ad-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba9ad-230">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="ba9ad-231">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve kterém bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-231">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="ba9ad-232">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-232">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ba9ad-233">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ba9ad-233">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ba9ad-234">V nabídce vyberte **soubor > Nový řešení**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-234">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="ba9ad-235">Vyberte **.NET Core > aplikace > webové aplikace ASP.NET Core** (nevybírejte **ASP.NET Core webové aplikace (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="ba9ad-235">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="ba9ad-236">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-236">Select **Next**.</span></span>

* <span data-ttu-id="ba9ad-237">Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-237">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="ba9ad-238">Přidat klientské knihovně SignalR</span><span class="sxs-lookup"><span data-stu-id="ba9ad-238">Add the SignalR client library</span></span>

<span data-ttu-id="ba9ad-239">Je součástí serveru knihovny SignalR `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-239">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="ba9ad-240">Knihovny JavaScript klienta není automaticky zahrnut v projektu.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-240">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="ba9ad-241">V tomto kurzu použijete Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-241">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="ba9ad-242">unpkg je síť pro doručování obsahu (CDN)), která může doručovat cokoli, co je součástí npm, Správce balíčků Node.js.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-242">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ba9ad-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9ad-243">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="ba9ad-244">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **knihoven na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-244">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="ba9ad-245">V **přidat knihovnu na straně klienta** dialogovém okně pro **poskytovatele** vyberte **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-245">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="ba9ad-246">Pro **knihovny**, zadejte `@aspnet/signalr@1`a vyberte nejnovější verzi, která není ve verzi preview.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-246">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![Přidat dialog knihoven na straně klienta - knihovně](signalr/_static/2.x/libman1.png)

* <span data-ttu-id="ba9ad-248">Vyberte **zvolte konkrétní soubory**, rozbalte *dist a prohlížeče* a pak zvolte položku *signalr.js* a *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-248">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="ba9ad-249">Nastavte **cílové umístění** k *wwwroot/lib/signalr/* a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-249">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Přidat dialog knihoven na straně klienta – vyberte soubory a cíl](signalr/_static/2.x/libman2.png)

  <span data-ttu-id="ba9ad-251">LibMan vytvoří *wwwroot/lib/signalr* složky a kopie vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-251">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ba9ad-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba9ad-252">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="ba9ad-253">V integrovaném terminálu spusťte následující příkaz k instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-253">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="ba9ad-254">Spusťte následující příkaz s použitím LibMan získat klientské knihovně SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-254">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="ba9ad-255">Budete muset počkat několik sekund, než výstup.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-255">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="ba9ad-256">Parametry určete následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-256">The parameters specify the following options:</span></span>
  * <span data-ttu-id="ba9ad-257">Použití zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-257">Use the unpkg provider.</span></span>
  * <span data-ttu-id="ba9ad-258">Kopírovat soubory *wwwroot/lib/signalr* cíl.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-258">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="ba9ad-259">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-259">Copy only the specified files.</span></span>

  <span data-ttu-id="ba9ad-260">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-260">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ba9ad-261">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ba9ad-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ba9ad-262">V **terminálu**, spusťte následující příkaz k instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-262">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="ba9ad-263">Přejděte do složky projektu (ten, který obsahuje *SignalRChat.csproj* souboru).</span><span class="sxs-lookup"><span data-stu-id="ba9ad-263">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="ba9ad-264">Spusťte následující příkaz s použitím LibMan získat klientské knihovně SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-264">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="ba9ad-265">Parametry určete následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-265">The parameters specify the following options:</span></span>
  * <span data-ttu-id="ba9ad-266">Použití zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-266">Use the unpkg provider.</span></span>
  * <span data-ttu-id="ba9ad-267">Kopírovat soubory *wwwroot/lib/signalr* cíl.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-267">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="ba9ad-268">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-268">Copy only the specified files.</span></span>

  <span data-ttu-id="ba9ad-269">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-269">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="ba9ad-270">Vytvoření rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="ba9ad-270">Create a SignalR hub</span></span>

<span data-ttu-id="ba9ad-271">A *centra* je třída, která slouží jako základní kanál, který zpracovává vnitřní komunikaci klient server.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-271">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="ba9ad-272">Ve složce projektu SignalRChat vytvořit *rozbočovače* složky.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-272">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="ba9ad-273">V *rozbočovače* složku, vytvořte *ChatHub.cs* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-273">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]

  <span data-ttu-id="ba9ad-274">`ChatHub` Třída dědí z funkce SignalR `Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-274">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="ba9ad-275">`Hub` Třída spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-275">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="ba9ad-276">`SendMessage` Metodu je možné vyvolat v připojených klientovi umožní odeslat zprávu pro všechny klienty.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-276">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="ba9ad-277">Klientský kód jazyka JavaScript, který volá metodu se zobrazí v pozdější části kurzu.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-277">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="ba9ad-278">Kód SignalR je asynchronní zajistit maximální škálovatelnosti.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-278">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="ba9ad-279">Konfigurace funkce SignalR</span><span class="sxs-lookup"><span data-stu-id="ba9ad-279">Configure SignalR</span></span>

<span data-ttu-id="ba9ad-280">Na serveru funkce SignalR nastavené předat požadavky SignalR SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-280">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="ba9ad-281">Přidejte následující zvýrazněný kód do *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-281">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="ba9ad-282">Tyto změny přidejte do systém injektáž závislostí ASP.NET Core a middleware kanálu SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-282">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="ba9ad-283">Přidejte kód klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="ba9ad-283">Add SignalR client code</span></span>

* <span data-ttu-id="ba9ad-284">Nahraďte obsah *Pages\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-284">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]

  <span data-ttu-id="ba9ad-285">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-285">The preceding code:</span></span>

  * <span data-ttu-id="ba9ad-286">Vytvoří textová pole pro název a text zprávy a tlačítka Odeslat.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-286">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="ba9ad-287">Vytvoří seznam s `id="messagesList"` pro zobrazení zprávy, které byly přijaty z rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-287">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="ba9ad-288">Obsahuje odkazy na skript ke knihovně SignalR a *chat.js* aplikační kód, který vytvoříte v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-288">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="ba9ad-289">V *wwwroot/js* složku, vytvořte *chat.js* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-289">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]

  <span data-ttu-id="ba9ad-290">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-290">The preceding code:</span></span>

  * <span data-ttu-id="ba9ad-291">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-291">Creates and starts a connection.</span></span>
  * <span data-ttu-id="ba9ad-292">Přidá tlačítko Odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-292">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="ba9ad-293">Přidá objekt připojení obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-293">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="ba9ad-294">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="ba9ad-294">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ba9ad-295">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9ad-295">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ba9ad-296">Stisknutím klávesy **CTRL + F5** a spusťte tak aplikaci bez ladění.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-296">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ba9ad-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba9ad-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ba9ad-298">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-298">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ba9ad-299">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ba9ad-299">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ba9ad-300">V nabídce vyberte **spuštění > Spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-300">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="ba9ad-301">Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo karty a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-301">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="ba9ad-302">Zvolte buď prohlížeče, zadejte název a zprávu a vybrat **poslat zprávu** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-302">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="ba9ad-303">Název a zpráva se zobrazí na obě stránky okamžitě.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-303">The name and message are displayed on both pages instantly.</span></span>

  ![Ukázková aplikace SignalR](signalr/_static/2.x/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="ba9ad-305">Pokud aplikace nebude fungovat, otevřete prohlížeč vývojářské nástroje (F12) a přejděte do konzoly.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-305">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="ba9ad-306">Můžou se zobrazovat chyby související s kódem HTML a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-306">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="ba9ad-307">Předpokládejme například, kam si ukládáte *signalr.js* v jiné složce než řízené.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-307">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="ba9ad-308">V takovém případě nebude fungovat odkaz na tento soubor a uvidíte v konzole chybu 404.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-308">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="ba9ad-309">![Chyba nenalezení signalr.js](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="ba9ad-309">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>

## <a name="next-steps"></a><span data-ttu-id="ba9ad-310">Další postup</span><span class="sxs-lookup"><span data-stu-id="ba9ad-310">Next steps</span></span>

<span data-ttu-id="ba9ad-311">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-311">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ba9ad-312">Vytvoření projektu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-312">Create a web app project.</span></span>
> * <span data-ttu-id="ba9ad-313">Přidání knihovny klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-313">Add the SignalR client library.</span></span>
> * <span data-ttu-id="ba9ad-314">Vytvoření rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-314">Create a SignalR hub.</span></span>
> * <span data-ttu-id="ba9ad-315">Nakonfigurujte projekt tak, aby používaly SignalR.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-315">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="ba9ad-316">Přidejte kód, který používá Centrum pro odesílání zpráv z libovolného klienta na všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="ba9ad-316">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ba9ad-317">Další kroky</span><span class="sxs-lookup"><span data-stu-id="ba9ad-317">Next steps</span></span>

<span data-ttu-id="ba9ad-318">Další informace o funkci SignalR naleznete v úvodu:</span><span class="sxs-lookup"><span data-stu-id="ba9ad-318">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ba9ad-319">Úvod do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="ba9ad-319">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end