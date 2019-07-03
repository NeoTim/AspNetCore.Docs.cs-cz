---
title: Začínáme s knihovnou SignalR technologie ASP.NET Core
author: bradygaster
description: V tomto kurzu vytvoříte aplikaci chatu, který používá funkce SignalR technologie ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/30/2018
uid: tutorials/signalr
ms.openlocfilehash: 9a4296550a17ac2c348f2406e9f5b39877b02b59
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555924"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="2c0d6-103">Kurz: Začínáme s knihovnou SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c0d6-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

<span data-ttu-id="2c0d6-104">V tomto kurzu se naučíte se základy vytváření aplikace v reálném čase pomocí nástroje SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="2c0d6-105">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2c0d6-106">Vytvoření webového projektu.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-106">Create a web project.</span></span>
> * <span data-ttu-id="2c0d6-107">Přidání knihovny klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="2c0d6-108">Vytvoření rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="2c0d6-109">Nakonfigurujte projekt tak, aby používaly SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="2c0d6-110">Přidejte kód, který odesílá zprávy z libovolného klienta na všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="2c0d6-111">Na konci budete mít funkční aplikaci chatu:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-111">At the end, you'll have a working chat app:</span></span>

![Ukázková aplikace SignalR](signalr/_static/signalr-get-started-finished.png)

<span data-ttu-id="2c0d6-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2c0d6-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2c0d6-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="2c0d6-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2c0d6-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c0d6-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2c0d6-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c0d6-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2c0d6-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="2c0d6-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="2c0d6-118">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="2c0d6-118">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2c0d6-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c0d6-119">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="2c0d6-120">V nabídce vyberte **soubor > Nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-120">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="2c0d6-121">V **nový projekt** dialogového okna, vyberte **nainstalováno > Visual C# > Web > Webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-121">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="2c0d6-122">Pojmenujte projekt *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-122">Name the project *SignalRChat*.</span></span>

  ![Dialogové okno nového projektu v sadě Visual Studio](signalr/_static/signalr-new-project-dialog.png)

* <span data-ttu-id="2c0d6-124">Vyberte **webovou aplikaci** vytvořit projekt, který se používá pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-124">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="2c0d6-125">Vyberte cílovou architekturu **.NET Core**vyberte **2.2 technologie ASP.NET Core**a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-125">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Dialogové okno nového projektu v sadě Visual Studio](signalr/_static/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2c0d6-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c0d6-127">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="2c0d6-128">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) do složky, ve kterém bude vytvořena nová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-128">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="2c0d6-129">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-129">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2c0d6-130">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="2c0d6-130">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2c0d6-131">V nabídce vyberte **soubor > Nový řešení**.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-131">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="2c0d6-132">Vyberte **.NET Core > aplikace > webové aplikace ASP.NET Core** (nevybírejte **ASP.NET Core webové aplikace (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="2c0d6-132">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="2c0d6-133">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-133">Select **Next**.</span></span>

* <span data-ttu-id="2c0d6-134">Pojmenujte projekt *SignalRChat*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-134">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="2c0d6-135">Přidat klientské knihovně SignalR</span><span class="sxs-lookup"><span data-stu-id="2c0d6-135">Add the SignalR client library</span></span>

<span data-ttu-id="2c0d6-136">Je součástí serveru knihovny SignalR `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-136">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="2c0d6-137">Knihovny JavaScript klienta není automaticky zahrnut v projektu.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-137">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="2c0d6-138">V tomto kurzu použijete Správce knihovny (LibMan) k získání klientské knihovny z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-138">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="2c0d6-139">unpkg je síť pro doručování obsahu (CDN)), která může doručovat cokoli, co je součástí npm, Správce balíčků Node.js.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-139">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2c0d6-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c0d6-140">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="2c0d6-141">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **knihoven na straně klienta**.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-141">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="2c0d6-142">V **přidat knihovnu na straně klienta** dialogovém okně pro **poskytovatele** vyberte **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-142">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="2c0d6-143">Pro **knihovny**, zadejte `@aspnet/signalr@1`a vyberte nejnovější verzi, která není ve verzi preview.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-143">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![Přidat dialog knihoven na straně klienta - knihovně](signalr/_static/libman1.png)

* <span data-ttu-id="2c0d6-145">Vyberte **zvolte konkrétní soubory**, rozbalte *dist a prohlížeče* a pak zvolte položku *signalr.js* a *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-145">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="2c0d6-146">Nastavte **cílové umístění** k *wwwroot/lib/signalr/* a vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-146">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Přidat dialog knihoven na straně klienta – vyberte soubory a cíl](signalr/_static/libman2.png)

  <span data-ttu-id="2c0d6-148">LibMan vytvoří *wwwroot/lib/signalr* složky a kopie vybrané soubory.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-148">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2c0d6-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c0d6-149">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="2c0d6-150">V integrovaném terminálu spusťte následující příkaz k instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-150">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="2c0d6-151">Spusťte následující příkaz s použitím LibMan získat klientské knihovně SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-151">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="2c0d6-152">Budete muset počkat několik sekund, než výstup.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-152">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="2c0d6-153">Parametry určete následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-153">The parameters specify the following options:</span></span>
  * <span data-ttu-id="2c0d6-154">Použití zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-154">Use the unpkg provider.</span></span>
  * <span data-ttu-id="2c0d6-155">Kopírovat soubory *wwwroot/lib/signalr* cíl.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-155">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="2c0d6-156">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-156">Copy only the specified files.</span></span>

  <span data-ttu-id="2c0d6-157">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-157">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2c0d6-158">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="2c0d6-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2c0d6-159">V **terminálu**, spusťte následující příkaz k instalaci LibMan.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-159">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="2c0d6-160">Přejděte do složky projektu (ten, který obsahuje *SignalRChat.csproj* souboru).</span><span class="sxs-lookup"><span data-stu-id="2c0d6-160">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="2c0d6-161">Spusťte následující příkaz s použitím LibMan získat klientské knihovně SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-161">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="2c0d6-162">Parametry určete následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-162">The parameters specify the following options:</span></span>
  * <span data-ttu-id="2c0d6-163">Použití zprostředkovatele unpkg.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-163">Use the unpkg provider.</span></span>
  * <span data-ttu-id="2c0d6-164">Kopírovat soubory *wwwroot/lib/signalr* cíl.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-164">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="2c0d6-165">Zkopírujte pouze zadané soubory.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-165">Copy only the specified files.</span></span>

  <span data-ttu-id="2c0d6-166">Výstup bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-166">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="2c0d6-167">Vytvoření rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="2c0d6-167">Create a SignalR hub</span></span>

<span data-ttu-id="2c0d6-168">A *centra* je třída, která slouží jako základní kanál, který zpracovává vnitřní komunikaci klient server.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-168">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="2c0d6-169">Ve složce projektu SignalRChat vytvořit *rozbočovače* složky.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-169">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="2c0d6-170">V *rozbočovače* složku, vytvořte *ChatHub.cs* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-170">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample/Hubs/ChatHub.cs)]

  <span data-ttu-id="2c0d6-171">`ChatHub` Třída dědí z funkce SignalR `Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-171">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="2c0d6-172">`Hub` Třída spravuje připojení, skupiny a zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-172">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="2c0d6-173">`SendMessage` Metodu je možné vyvolat v připojených klientovi umožní odeslat zprávu pro všechny klienty.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-173">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="2c0d6-174">Klientský kód jazyka JavaScript, který volá metodu se zobrazí v pozdější části kurzu.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-174">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="2c0d6-175">Kód SignalR je asynchronní zajistit maximální škálovatelnosti.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-175">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="2c0d6-176">Konfigurace funkce SignalR</span><span class="sxs-lookup"><span data-stu-id="2c0d6-176">Configure SignalR</span></span>

<span data-ttu-id="2c0d6-177">Na serveru funkce SignalR nastavené předat požadavky SignalR SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-177">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="2c0d6-178">Přidejte následující zvýrazněný kód do *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-178">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="2c0d6-179">Tyto změny přidejte do systém injektáž závislostí ASP.NET Core a middleware kanálu SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-179">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="2c0d6-180">Přidejte kód klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="2c0d6-180">Add SignalR client code</span></span>

* <span data-ttu-id="2c0d6-181">Nahraďte obsah *Pages\Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-181">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample/Pages/Index.cshtml)]

  <span data-ttu-id="2c0d6-182">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-182">The preceding code:</span></span>

  * <span data-ttu-id="2c0d6-183">Vytvoří textová pole pro název a text zprávy a tlačítka Odeslat.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-183">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="2c0d6-184">Vytvoří seznam s `id="messagesList"` pro zobrazení zprávy, které byly přijaty z rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-184">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="2c0d6-185">Obsahuje odkazy na skript ke knihovně SignalR a *chat.js* aplikační kód, který vytvoříte v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-185">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="2c0d6-186">V *wwwroot/js* složku, vytvořte *chat.js* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-186">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample/wwwroot/js/chat.js)]

  <span data-ttu-id="2c0d6-187">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-187">The preceding code:</span></span>

  * <span data-ttu-id="2c0d6-188">Vytvoří a spustí připojení.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-188">Creates and starts a connection.</span></span>
  * <span data-ttu-id="2c0d6-189">Přidá tlačítko Odeslat obslužnou rutinu, která odesílá zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-189">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="2c0d6-190">Přidá objekt připojení obslužnou rutinu, která přijímá zprávy z centra a přidá je do seznamu.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-190">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="2c0d6-191">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="2c0d6-191">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2c0d6-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c0d6-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c0d6-193">Stisknutím klávesy **CTRL + F5** a spusťte tak aplikaci bez ladění.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-193">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2c0d6-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c0d6-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2c0d6-195">V integrovaném terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-195">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2c0d6-196">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="2c0d6-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2c0d6-197">V nabídce vyberte **spuštění > Spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-197">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="2c0d6-198">Zkopírujte adresu URL z adresního řádku, otevřete jinou instanci prohlížeče nebo karty a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-198">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="2c0d6-199">Zvolte buď prohlížeče, zadejte název a zprávu a vybrat **poslat zprávu** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-199">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="2c0d6-200">Název a zpráva se zobrazí na obě stránky okamžitě.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-200">The name and message are displayed on both pages instantly.</span></span>

  ![Ukázková aplikace SignalR](signalr/_static/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="2c0d6-202">Pokud aplikace nebude fungovat, otevřete prohlížeč vývojářské nástroje (F12) a přejděte do konzoly.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-202">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="2c0d6-203">Můžou se zobrazovat chyby související s kódem HTML a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-203">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="2c0d6-204">Předpokládejme například, kam si ukládáte *signalr.js* v jiné složce než řízené.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-204">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="2c0d6-205">V takovém případě nebude fungovat odkaz na tento soubor a uvidíte v konzole chybu 404.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-205">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="2c0d6-206">![Chyba nenalezení signalr.js](signalr/_static/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="2c0d6-206">![signalr.js not found error](signalr/_static/f12-console.png)</span></span>

## <a name="next-steps"></a><span data-ttu-id="2c0d6-207">Další kroky</span><span class="sxs-lookup"><span data-stu-id="2c0d6-207">Next steps</span></span>

<span data-ttu-id="2c0d6-208">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-208">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2c0d6-209">Vytvoření projektu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-209">Create a web app project.</span></span>
> * <span data-ttu-id="2c0d6-210">Přidání knihovny klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-210">Add the SignalR client library.</span></span>
> * <span data-ttu-id="2c0d6-211">Vytvoření rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-211">Create a SignalR hub.</span></span>
> * <span data-ttu-id="2c0d6-212">Nakonfigurujte projekt tak, aby používaly SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-212">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="2c0d6-213">Přidejte kód, který používá Centrum pro odesílání zpráv z libovolného klienta na všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="2c0d6-213">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="2c0d6-214">Další informace o funkci SignalR naleznete v úvodu:</span><span class="sxs-lookup"><span data-stu-id="2c0d6-214">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="2c0d6-215">Úvod do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="2c0d6-215">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
