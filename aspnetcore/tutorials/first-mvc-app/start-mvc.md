---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Naučte se, jak začít s ASP.NET Core MVC.
ms.author: riande
ms.date: 08/05/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 36f4811f876a6e35440445103a1f86ae06b31b6a
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022521"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="61c99-103">Začínáme s ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="61c99-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="61c99-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="61c99-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="61c99-105">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="61c99-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="61c99-106">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="61c99-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="61c99-107">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="61c99-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="61c99-108">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="61c99-108">Create a web app.</span></span>
> * <span data-ttu-id="61c99-109">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="61c99-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="61c99-110">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="61c99-110">Work with a database.</span></span>
> * <span data-ttu-id="61c99-111">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="61c99-111">Add search and validation.</span></span>

<span data-ttu-id="61c99-112">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="61c99-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="61c99-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="61c99-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="61c99-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c99-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="61c99-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c99-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="61c99-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="61c99-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="61c99-117">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="61c99-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="61c99-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c99-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="61c99-119">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="61c99-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="61c99-120">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="61c99-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="61c99-122">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="61c99-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="61c99-123">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="61c99-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)

* <span data-ttu-id="61c99-125">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="61c99-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="61c99-126">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="61c99-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="61c99-127">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="61c99-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="61c99-128">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="61c99-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="61c99-129">Toto je základní počáteční projekt.</span><span class="sxs-lookup"><span data-stu-id="61c99-129">This is a basic starter project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="61c99-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c99-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="61c99-131">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="61c99-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="61c99-132">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="61c99-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="61c99-133">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="61c99-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="61c99-134">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="61c99-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="61c99-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="61c99-135">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="61c99-136">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="61c99-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="61c99-137">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="61c99-137">Select **Yes**</span></span>

  * <span data-ttu-id="61c99-138">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="61c99-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="61c99-139">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="61c99-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="61c99-140">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="61c99-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="61c99-141">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="61c99-141">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="61c99-143">Vyberte možnost Webová aplikace **.NET Core** > **App** > **(Model-View-Controller)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="61c99-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="61c99-145">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílovou architekturu** **.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="61c99-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.0**.</span></span>

<!-- 
  ![macOS .NET Core 2.2 selection](./start-mvc/_static/new_project_22_vsmac.png)
-->

* <span data-ttu-id="61c99-146">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="61c99-146">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="61c99-147">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="61c99-147">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="61c99-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c99-148">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="61c99-149">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="61c99-149">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="61c99-150">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="61c99-150">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="61c99-151">Všimněte si, že se v `localhost:port#` adresním řádku zobrazuje `example.com`, a ne něco podobného.</span><span class="sxs-lookup"><span data-stu-id="61c99-151">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="61c99-152">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="61c99-152">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="61c99-153">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="61c99-153">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="61c99-154">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="61c99-154">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="61c99-155">Mnoho vývojářů upřednostňuje použít režim bez ladění pro rychlé spuštění aplikace a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="61c99-155">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="61c99-156">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="61c99-156">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="61c99-158">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="61c99-158">You can debug the app by selecting the **IIS Express** button</span></span>

  ![Služba IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="61c99-160">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="61c99-160">The following image shows the app:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="61c99-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c99-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="61c99-163">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="61c99-163">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="61c99-164">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="61c99-164">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="61c99-165">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="61c99-165">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="61c99-166">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="61c99-166">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="61c99-167">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="61c99-167">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="61c99-168">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="61c99-168">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="61c99-169">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="61c99-169">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="61c99-171">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="61c99-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="61c99-172">Kliknutím na **Spustit** > **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="61c99-172">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="61c99-173">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="61c99-173">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="61c99-174">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="61c99-174">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="61c99-175">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="61c99-175">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="61c99-176">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="61c99-176">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="61c99-177">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="61c99-177">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="61c99-178">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="61c99-178">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="61c99-179">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="61c99-179">The following image shows the app:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="61c99-181">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="61c99-181">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="61c99-182">Next</span><span class="sxs-lookup"><span data-stu-id="61c99-182">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="61c99-183">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="61c99-183">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="61c99-184">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="61c99-184">The app manages a database of movie titles.</span></span> <span data-ttu-id="61c99-185">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="61c99-185">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="61c99-186">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="61c99-186">Create a web app.</span></span>
> * <span data-ttu-id="61c99-187">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="61c99-187">Add and scaffold a model.</span></span>
> * <span data-ttu-id="61c99-188">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="61c99-188">Work with a database.</span></span>
> * <span data-ttu-id="61c99-189">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="61c99-189">Add search and validation.</span></span>

<span data-ttu-id="61c99-190">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="61c99-190">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="61c99-191">Požadavky</span><span class="sxs-lookup"><span data-stu-id="61c99-191">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="61c99-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c99-192">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="61c99-193">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c99-193">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="61c99-194">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="61c99-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="61c99-195">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="61c99-195">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="61c99-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c99-196">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="61c99-197">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="61c99-197">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="61c99-198">Selecct **ASP.NET Core webové aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="61c99-198">Selecct **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="61c99-200">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="61c99-200">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="61c99-201">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="61c99-201">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)


* <span data-ttu-id="61c99-203">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="61c99-203">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="61c99-204">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="61c99-204">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="61c99-205">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="61c99-205">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="61c99-206">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="61c99-206">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="61c99-207">Toto je základní počáteční projekt a je dobrým místem, kde začít.</span><span class="sxs-lookup"><span data-stu-id="61c99-207">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="61c99-208">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c99-208">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="61c99-209">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="61c99-209">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="61c99-210">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="61c99-210">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="61c99-211">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="61c99-211">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="61c99-212">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="61c99-212">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="61c99-213">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="61c99-213">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="61c99-214">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="61c99-214">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="61c99-215">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="61c99-215">Select **Yes**</span></span>

  * <span data-ttu-id="61c99-216">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="61c99-216">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="61c99-217">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="61c99-217">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="61c99-218">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="61c99-218">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="61c99-219">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="61c99-219">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="61c99-221">Vyberte možnost Webová aplikace **.NET Core** > **App** > **(Model-View-Controller)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="61c99-221">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="61c99-223">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou architekturu** **.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="61c99-223">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2,2 – výběr](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="61c99-225">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="61c99-225">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="61c99-226">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="61c99-226">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="61c99-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c99-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="61c99-228">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="61c99-228">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="61c99-229">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="61c99-229">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="61c99-230">Všimněte si, že se v `localhost:port#` adresním řádku zobrazuje `example.com`, a ne něco podobného.</span><span class="sxs-lookup"><span data-stu-id="61c99-230">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="61c99-231">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="61c99-231">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="61c99-232">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="61c99-232">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="61c99-233">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="61c99-233">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="61c99-234">Mnoho vývojářů upřednostňuje použít režim bez ladění pro rychlé spuštění aplikace a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="61c99-234">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="61c99-235">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="61c99-235">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="61c99-237">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="61c99-237">You can debug the app by selecting the **IIS Express** button</span></span>

  ![Služba IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="61c99-239">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="61c99-239">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="61c99-240">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="61c99-240">This app doesn't track personal information.</span></span> <span data-ttu-id="61c99-241">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="61c99-241">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="61c99-243">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="61c99-243">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="61c99-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c99-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="61c99-246">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="61c99-246">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="61c99-247">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="61c99-247">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="61c99-248">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="61c99-248">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="61c99-249">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="61c99-249">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="61c99-250">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="61c99-250">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="61c99-251">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="61c99-251">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="61c99-252">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="61c99-252">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="61c99-253">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="61c99-253">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="61c99-254">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="61c99-254">This app doesn't track personal information.</span></span> <span data-ttu-id="61c99-255">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="61c99-255">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="61c99-257">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="61c99-257">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="61c99-259">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="61c99-259">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="61c99-260">Kliknutím na **Spustit** > **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="61c99-260">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="61c99-261">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="61c99-261">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="61c99-262">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="61c99-262">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="61c99-263">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="61c99-263">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="61c99-264">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="61c99-264">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="61c99-265">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="61c99-265">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="61c99-266">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="61c99-266">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="61c99-267">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="61c99-267">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="61c99-268">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="61c99-268">This app doesn't track personal information.</span></span> <span data-ttu-id="61c99-269">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="61c99-269">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="61c99-271">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="61c99-271">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="61c99-273">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="61c99-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="61c99-274">Next</span><span class="sxs-lookup"><span data-stu-id="61c99-274">Next</span></span>](adding-controller.md)

::: moniker-end
