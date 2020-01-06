---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Naučte se, jak začít s ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: e70384a6f20f3ef06059ed6b51c76e923187c317
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354928"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="5a938-103">Začínáme s ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="5a938-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="5a938-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5a938-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="5a938-105">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="5a938-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="5a938-106">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="5a938-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="5a938-107">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="5a938-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5a938-108">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5a938-108">Create a web app.</span></span>
> * <span data-ttu-id="5a938-109">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="5a938-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="5a938-110">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="5a938-110">Work with a database.</span></span>
> * <span data-ttu-id="5a938-111">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="5a938-111">Add search and validation.</span></span>

<span data-ttu-id="5a938-112">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="5a938-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="5a938-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="5a938-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5a938-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a938-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5a938-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a938-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5a938-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5a938-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="5a938-117">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="5a938-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5a938-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a938-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a938-119">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="5a938-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="5a938-120">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5a938-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="5a938-122">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5a938-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="5a938-123">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="5a938-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)

* <span data-ttu-id="5a938-125">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5a938-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="5a938-126">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="5a938-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="5a938-127">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="5a938-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="5a938-128">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="5a938-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="5a938-129">Toto je základní počáteční projekt.</span><span class="sxs-lookup"><span data-stu-id="5a938-129">This is a basic starter project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5a938-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a938-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a938-131">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="5a938-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="5a938-132">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="5a938-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="5a938-133">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5a938-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5a938-134">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="5a938-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="5a938-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5a938-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="5a938-136">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="5a938-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="5a938-137">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="5a938-137">Select **Yes**</span></span>

  * <span data-ttu-id="5a938-138">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="5a938-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="5a938-139">`code -r MvcMovie`: načte do Visual Studio Code soubor projektu *MvcMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="5a938-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5a938-140">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5a938-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5a938-141">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="5a938-141">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="5a938-143">Vyberte možnost **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="5a938-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="5a938-145">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílovou architekturu** **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="5a938-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

<!-- 
  ![macOS .NET Core 2.2 selection](./start-mvc/_static/new_project_22_vsmac.png)
-->

* <span data-ttu-id="5a938-146">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5a938-146">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="5a938-147">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="5a938-147">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5a938-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a938-148">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5a938-149">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="5a938-149">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="5a938-150">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5a938-150">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5a938-151">Všimněte si, že se v adresním řádku zobrazuje `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a938-151">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a938-152">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="5a938-152">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a938-153">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="5a938-153">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="5a938-154">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="5a938-154">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a938-155">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="5a938-155">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="5a938-156">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="5a938-156">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="5a938-158">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="5a938-158">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="5a938-160">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="5a938-160">The following image shows the app:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5a938-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a938-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a938-163">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="5a938-163">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="5a938-164">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5a938-164">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="5a938-165">V adresním řádku se zobrazí `localhost:port:5001` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a938-165">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="5a938-166">Důvodem je, že `localhost` je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="5a938-166">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5a938-167">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="5a938-167">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="5a938-168">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="5a938-168">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a938-169">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="5a938-169">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5a938-171">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5a938-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5a938-172">Pokud chcete spustit aplikaci, vyberte **spustit** > **Spustit bez ladění** .</span><span class="sxs-lookup"><span data-stu-id="5a938-172">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="5a938-173">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="5a938-173">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="5a938-174">V adresním řádku se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a938-174">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a938-175">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="5a938-175">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a938-176">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="5a938-176">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5a938-177">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="5a938-177">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5a938-178">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="5a938-178">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="5a938-179">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="5a938-179">The following image shows the app:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="5a938-181">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="5a938-181">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5a938-182">Next</span><span class="sxs-lookup"><span data-stu-id="5a938-182">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="5a938-183">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="5a938-183">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="5a938-184">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="5a938-184">The app manages a database of movie titles.</span></span> <span data-ttu-id="5a938-185">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="5a938-185">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5a938-186">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5a938-186">Create a web app.</span></span>
> * <span data-ttu-id="5a938-187">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="5a938-187">Add and scaffold a model.</span></span>
> * <span data-ttu-id="5a938-188">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="5a938-188">Work with a database.</span></span>
> * <span data-ttu-id="5a938-189">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="5a938-189">Add search and validation.</span></span>

<span data-ttu-id="5a938-190">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="5a938-190">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="5a938-191">Požadavky</span><span class="sxs-lookup"><span data-stu-id="5a938-191">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5a938-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a938-192">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5a938-193">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a938-193">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5a938-194">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5a938-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="5a938-195">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="5a938-195">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5a938-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a938-196">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a938-197">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="5a938-197">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="5a938-198">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="5a938-198">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="5a938-200">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5a938-200">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="5a938-201">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="5a938-201">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)


* <span data-ttu-id="5a938-203">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5a938-203">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="5a938-204">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="5a938-204">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="5a938-205">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="5a938-205">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="5a938-206">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="5a938-206">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="5a938-207">Toto je základní počáteční projekt a je dobrým místem, kde začít.</span><span class="sxs-lookup"><span data-stu-id="5a938-207">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5a938-208">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a938-208">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a938-209">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="5a938-209">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="5a938-210">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="5a938-210">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="5a938-211">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5a938-211">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5a938-212">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="5a938-212">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="5a938-213">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5a938-213">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="5a938-214">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="5a938-214">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="5a938-215">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="5a938-215">Select **Yes**</span></span>

  * <span data-ttu-id="5a938-216">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="5a938-216">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="5a938-217">`code -r MvcMovie`: načte do Visual Studio Code soubor projektu *MvcMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="5a938-217">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5a938-218">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5a938-218">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5a938-219">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="5a938-219">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="5a938-221">Vyberte možnost **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="5a938-221">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="5a938-223">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou architekturu** **.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="5a938-223">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2,2 – výběr](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="5a938-225">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5a938-225">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="5a938-226">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="5a938-226">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5a938-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a938-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5a938-228">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="5a938-228">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="5a938-229">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5a938-229">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5a938-230">Všimněte si, že se v adresním řádku zobrazuje `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a938-230">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a938-231">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="5a938-231">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a938-232">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="5a938-232">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="5a938-233">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="5a938-233">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a938-234">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="5a938-234">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="5a938-235">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="5a938-235">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="5a938-237">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="5a938-237">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="5a938-239">Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním.</span><span class="sxs-lookup"><span data-stu-id="5a938-239">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5a938-240">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="5a938-240">This app doesn't track personal information.</span></span> <span data-ttu-id="5a938-241">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5a938-241">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="5a938-243">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="5a938-243">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5a938-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a938-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a938-246">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="5a938-246">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="5a938-247">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5a938-247">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="5a938-248">V adresním řádku se zobrazí `localhost:port:5001` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a938-248">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="5a938-249">Důvodem je, že `localhost` je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="5a938-249">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5a938-250">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="5a938-250">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="5a938-251">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="5a938-251">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5a938-252">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="5a938-252">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="5a938-253">Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním.</span><span class="sxs-lookup"><span data-stu-id="5a938-253">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5a938-254">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="5a938-254">This app doesn't track personal information.</span></span> <span data-ttu-id="5a938-255">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5a938-255">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="5a938-257">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="5a938-257">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5a938-259">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5a938-259">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5a938-260">Pokud chcete spustit aplikaci, vyberte **spustit** > **Spustit bez ladění** .</span><span class="sxs-lookup"><span data-stu-id="5a938-260">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="5a938-261">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="5a938-261">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="5a938-262">V adresním řádku se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5a938-262">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a938-263">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="5a938-263">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5a938-264">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="5a938-264">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5a938-265">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="5a938-265">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5a938-266">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="5a938-266">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="5a938-267">Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním.</span><span class="sxs-lookup"><span data-stu-id="5a938-267">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5a938-268">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="5a938-268">This app doesn't track personal information.</span></span> <span data-ttu-id="5a938-269">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5a938-269">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="5a938-271">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="5a938-271">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="5a938-273">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="5a938-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5a938-274">Next</span><span class="sxs-lookup"><span data-stu-id="5a938-274">Next</span></span>](adding-controller.md)

::: moniker-end
