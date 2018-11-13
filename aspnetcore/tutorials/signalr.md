---
title: Začínáme s knihovnou SignalR technologie ASP.NET Core
author: tdykstra
description: V tomto kurzu vytvoříte aplikaci chatu, který používá funkce SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/13/2018
uid: tutorials/signalr
ms.openlocfilehash: b7414b1981508f2424eccb147a44023058c7f97c
ms.sourcegitcommit: 1d6ab43eed9cb3df6211c22b97bb3a9351ec4419
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51597794"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="70577-103">Kurz: Začínáme s knihovnou SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70577-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

<span data-ttu-id="70577-104">V tomto kurzu se naučíte se základy vytváření aplikace v reálném čase pomocí nástroje SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="70577-105">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="70577-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="70577-106">Vytvoření webového projektu.</span><span class="sxs-lookup"><span data-stu-id="70577-106">Create a web project.</span></span>
> * <span data-ttu-id="70577-107">Přidání knihovny klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="70577-108">Vytvoření rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="70577-109">Nakonfigurujte projekt tak, aby používaly SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="70577-110">Přidejte kód, který odesílá zprávy z libovolného klienta na všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="70577-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="70577-111">Na konci budete mít funkční aplikaci chatu:</span><span class="sxs-lookup"><span data-stu-id="70577-111">At the end, you'll have a working chat app:</span></span>

![Ukázková aplikace SignalR](signalr/_static/signalr-get-started-finished.png)

<span data-ttu-id="70577-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="70577-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="70577-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="70577-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="70577-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70577-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="70577-116">[Visual Studio 2017 verze 15,8 nebo novější](https://www.visualstudio.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="70577-116">[Visual Studio 2017 version 15.8 or later](https://www.visualstudio.com/downloads/) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="70577-117">.NET core SDK 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="70577-117">.NET Core SDK 2.1 or later</span></span>](https://www.microsoft.com/net/download/all)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="70577-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70577-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="70577-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70577-119">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="70577-120">.NET core SDK 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="70577-120">.NET Core SDK 2.1 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="70577-121">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70577-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="70577-122">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="70577-122">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="70577-123">Visual Studio pro Mac verze 7.5.4 nebo novější</span><span class="sxs-lookup"><span data-stu-id="70577-123">Visual Studio for Mac version 7.5.4 or later</span></span>](https://www.visualstudio.com/downloads/)
* <span data-ttu-id="70577-124">[.NET core SDK 2.1 nebo novější](https://www.microsoft.com/net/download/all) (zahrnuty v instalaci sady Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="70577-124">[.NET Core SDK 2.1 or later](https://www.microsoft.com/net/download/all) (included in the Visual Studio install)</span></span>

---

## <a name="create-a-web-project"></a><span data-ttu-id="70577-125">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="70577-125">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="70577-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70577-126">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="70577-127">V nabídce vyberte **soubor > Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="70577-127">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="70577-128">V **nový projekt** dialogového okna, vyberte **nainstalováno > Visual C# > Web > Webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="70577-128">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="70577-129">Pojmenujte projekt *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="70577-129">Name the project *SignalRChat*.</span></span>

  ![Dialogové okno nového projektu v sadě Visual Studio](signalr/_static/signalr-new-project-dialog.png)

* <span data-ttu-id="70577-131">Vyberte **webovou aplikaci** vytvořit projekt, který se používá pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="70577-131">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="70577-132">Vyberte cílovou architekturu **.NET Core**vyberte **ASP.NET Core 2.1**a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="70577-132">Select a target framework of **.NET Core**, select **ASP.NET Core 2.1**, and click **OK**.</span></span>

  ![Dialogové okno nového projektu v sadě Visual Studio](signalr/_static/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="70577-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70577-134">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="70577-135">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve kterém bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="70577-135">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="70577-136">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="70577-136">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="70577-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="70577-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="70577-138">V nabídce vyberte **soubor > Nový řešení**.</span><span class="sxs-lookup"><span data-stu-id="70577-138">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="70577-139">Vyberte **.NET Core > aplikace > webové aplikace ASP.NET Core** (nevybírejte **ASP.NET Core webové aplikace (MVC)**).</span><span class="sxs-lookup"><span data-stu-id="70577-139">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="70577-140">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="70577-140">Select **Next**.</span></span>

* <span data-ttu-id="70577-141">Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="70577-141">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="70577-142">Přidat klientské knihovně SignalR</span><span class="sxs-lookup"><span data-stu-id="70577-142">Add the SignalR client library</span></span>

<span data-ttu-id="70577-143">Je součástí serveru knihovny SignalR `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all.</span><span class="sxs-lookup"><span data-stu-id="70577-143">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="70577-144">Knihovny JavaScript klienta není automaticky zahrnut v projektu.</span><span class="sxs-lookup"><span data-stu-id="70577-144">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="70577-145">V tomto kurzu použijete Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="70577-145">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="70577-146">unpkg je síť pro doručování obsahu (CDN)), která může doručovat cokoli, co je součástí npm, Správce balíčků Node.js.</span><span class="sxs-lookup"><span data-stu-id="70577-146">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="70577-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70577-147">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="70577-148">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **knihoven na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="70577-148">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="70577-149">V **přidat knihovnu na straně klienta** dialogovém okně pro **poskytovatele** vyberte **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="70577-149">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="70577-150">Pro **knihovny**, zadejte `@aspnet/signalr@1`a vyberte nejnovější verzi, která není ve verzi preview.</span><span class="sxs-lookup"><span data-stu-id="70577-150">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![Přidat dialog knihoven na straně klienta - knihovně](signalr/_static/libman1.png)

* <span data-ttu-id="70577-152">Vyberte **zvolte konkrétní soubory**, rozbalte *dist a prohlížeče* a pak zvolte položku *signalr.js* a *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="70577-152">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="70577-153">Nastavte **cílové umístění** k *wwwroot/lib/signalr/* a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="70577-153">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Přidat dialog knihoven na straně klienta – vyberte soubory a cíl](signalr/_static/libman2.png)

  <span data-ttu-id="70577-155">LibMan vytvoří *wwwroot/lib/signalr* složky a kopie vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="70577-155">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="70577-156">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70577-156">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="70577-157">V integrovaném terminálu spusťte následující příkaz k instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="70577-157">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="70577-158">Spusťte následující příkaz s použitím LibMan získat klientské knihovně SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-158">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="70577-159">Budete muset počkat několik sekund, než výstup.</span><span class="sxs-lookup"><span data-stu-id="70577-159">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="70577-160">Parametry určete následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="70577-160">The parameters specify the following options:</span></span>
  * <span data-ttu-id="70577-161">Použití zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="70577-161">Use the unpkg provider.</span></span>
  * <span data-ttu-id="70577-162">Kopírovat soubory *wwwroot/lib/signalr* cíl.</span><span class="sxs-lookup"><span data-stu-id="70577-162">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="70577-163">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="70577-163">Copy only the specified files.</span></span>

  <span data-ttu-id="70577-164">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="70577-164">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="70577-165">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="70577-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="70577-166">V **terminálu**, spusťte následující příkaz k instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="70577-166">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="70577-167">Přejděte do složky projektu (ten, který obsahuje *SignalRChat.csproj* souboru).</span><span class="sxs-lookup"><span data-stu-id="70577-167">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="70577-168">Spusťte následující příkaz s použitím LibMan získat klientské knihovně SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-168">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="70577-169">Parametry určete následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="70577-169">The parameters specify the following options:</span></span>
  * <span data-ttu-id="70577-170">Použití zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="70577-170">Use the unpkg provider.</span></span>
  * <span data-ttu-id="70577-171">Kopírovat soubory *wwwroot/lib/signalr* cíl.</span><span class="sxs-lookup"><span data-stu-id="70577-171">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="70577-172">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="70577-172">Copy only the specified files.</span></span>

  <span data-ttu-id="70577-173">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="70577-173">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="70577-174">Vytvoření rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="70577-174">Create a SignalR hub</span></span>

<span data-ttu-id="70577-175">A *centra* je třída, která slouží jako základní kanál, který zpracovává vnitřní komunikaci klient server.</span><span class="sxs-lookup"><span data-stu-id="70577-175">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="70577-176">Ve složce projektu SignalRChat vytvořit *rozbočovače* složky.</span><span class="sxs-lookup"><span data-stu-id="70577-176">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="70577-177">V *rozbočovače* složku, vytvořte *ChatHub.cs* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="70577-177">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample/Hubs/ChatHub.cs)]

  <span data-ttu-id="70577-178">`ChatHub` Třída dědí z funkce SignalR `Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="70577-178">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="70577-179">`Hub` Třída spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="70577-179">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="70577-180">`SendMessage` Metoda může být volán všech připojených klientů.</span><span class="sxs-lookup"><span data-stu-id="70577-180">The `SendMessage` method can be called by any connected client.</span></span> <span data-ttu-id="70577-181">Přijaté zprávy odešle všem klientům.</span><span class="sxs-lookup"><span data-stu-id="70577-181">It sends the received message to all clients.</span></span> <span data-ttu-id="70577-182">Kód SignalR je asynchronní zajistit maximální škálovatelnosti.</span><span class="sxs-lookup"><span data-stu-id="70577-182">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="70577-183">Konfigurace funkce SignalR</span><span class="sxs-lookup"><span data-stu-id="70577-183">Configure SignalR</span></span>

<span data-ttu-id="70577-184">Na serveru funkce SignalR nastavené předat požadavky SignalR SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-184">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="70577-185">Přidejte následující zvýrazněný kód do *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="70577-185">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="70577-186">Tyto změny přidejte do systém injektáž závislostí ASP.NET Core a middleware kanálu SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-186">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="70577-187">Přidejte kód klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="70577-187">Add SignalR client code</span></span>

* <span data-ttu-id="70577-188">Nahraďte obsah *Pages\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="70577-188">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample/Pages/Index.cshtml)]

  <span data-ttu-id="70577-189">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="70577-189">The preceding code:</span></span>

  * <span data-ttu-id="70577-190">Vytvoří textová pole pro název a text zprávy a tlačítka Odeslat.</span><span class="sxs-lookup"><span data-stu-id="70577-190">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="70577-191">Vytvoří seznam s `id="messagesList"` pro zobrazení zprávy, které byly přijaty z rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-191">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="70577-192">Obsahuje odkazy na skript ke knihovně SignalR a *chat.js* aplikační kód, který vytvoříte v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="70577-192">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="70577-193">V *wwwroot/js* složku, vytvořte *chat.js* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="70577-193">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample/wwwroot/js/chat.js)]

  <span data-ttu-id="70577-194">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="70577-194">The preceding code:</span></span>

  * <span data-ttu-id="70577-195">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="70577-195">Creates and starts a connection.</span></span>
  * <span data-ttu-id="70577-196">Přidá tlačítko Odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="70577-196">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="70577-197">Přidá objekt připojení obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="70577-197">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="70577-198">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="70577-198">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="70577-199">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70577-199">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="70577-200">Stisknutím klávesy **CTRL + F5** a spusťte tak aplikaci bez ladění.</span><span class="sxs-lookup"><span data-stu-id="70577-200">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="70577-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="70577-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="70577-202">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="70577-202">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat
  ```
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="70577-203">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="70577-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="70577-204">V nabídce vyberte **spuštění > Spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="70577-204">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="70577-205">Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo karty a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="70577-205">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="70577-206">Zvolte buď prohlížeče, zadejte název a zprávu a vybrat **poslat zprávu** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="70577-206">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="70577-207">Název a zpráva se zobrazí na obě stránky okamžitě.</span><span class="sxs-lookup"><span data-stu-id="70577-207">The name and message are displayed on both pages instantly.</span></span>

  ![Ukázková aplikace SignalR](signalr/_static/signalr-get-started-finished.png)

## <a name="next-steps"></a><span data-ttu-id="70577-209">Další kroky</span><span class="sxs-lookup"><span data-stu-id="70577-209">Next steps</span></span>

<span data-ttu-id="70577-210">V tomto kurzu jste zjistili, jak:</span><span class="sxs-lookup"><span data-stu-id="70577-210">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="70577-211">Vytvoření projektu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="70577-211">Create a web app project.</span></span>
> * <span data-ttu-id="70577-212">Přidání knihovny klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-212">Add the SignalR client library.</span></span>
> * <span data-ttu-id="70577-213">Vytvoření rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-213">Create a SignalR hub.</span></span>
> * <span data-ttu-id="70577-214">Nakonfigurujte projekt tak, aby používaly SignalR.</span><span class="sxs-lookup"><span data-stu-id="70577-214">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="70577-215">Přidejte kód, který používá Centrum pro odesílání zpráv z libovolného klienta na všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="70577-215">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="70577-216">Další informace o funkci SignalR naleznete v úvodu:</span><span class="sxs-lookup"><span data-stu-id="70577-216">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="70577-217">Úvod do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="70577-217">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
