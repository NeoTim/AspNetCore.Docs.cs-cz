---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Naučte se, jak začít s ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 3df00af8e6bca0dbf2d7871f383dd67b465aa5da
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021169"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="62462-103">Začínáme s ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="62462-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="62462-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="62462-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="62462-105">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="62462-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="62462-106">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="62462-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="62462-107">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="62462-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="62462-108">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="62462-108">Create a web app.</span></span>
> * <span data-ttu-id="62462-109">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="62462-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="62462-110">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="62462-110">Work with a database.</span></span>
> * <span data-ttu-id="62462-111">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="62462-111">Add search and validation.</span></span>

<span data-ttu-id="62462-112">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="62462-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="62462-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="62462-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62462-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62462-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="62462-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62462-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62462-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="62462-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="62462-117">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="62462-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62462-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62462-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="62462-119">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="62462-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="62462-120">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="62462-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="62462-122">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="62462-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="62462-123">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="62462-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)

* <span data-ttu-id="62462-125">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="62462-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="62462-126">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="62462-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="62462-127">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="62462-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="62462-128">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="62462-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="62462-129">Toto je základní počáteční projekt.</span><span class="sxs-lookup"><span data-stu-id="62462-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="62462-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62462-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="62462-131">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="62462-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="62462-132">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="62462-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="62462-133">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="62462-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="62462-134">Změňte adresáře ( `cd` ) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="62462-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="62462-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="62462-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="62462-136">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="62462-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="62462-137">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="62462-137">Select **Yes**</span></span>

  * <span data-ttu-id="62462-138">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="62462-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="62462-139">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="62462-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62462-140">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="62462-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="62462-141">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="62462-141">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="62462-143">V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web (Model-View-Controller)**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="62462-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="62462-144">Ve verzi 8,6 nebo novější vyberte možnost Webová aplikace **webové a konzolová**  >  **aplikace**  >  **(model-zobrazení-kontroler)**  >  **Další**.</span><span class="sxs-lookup"><span data-stu-id="62462-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Výběr šablony webové aplikace v macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="62462-146">V dialogovém okně **Konfigurace nové webové aplikace** :</span><span class="sxs-lookup"><span data-stu-id="62462-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="62462-147">Potvrďte, že **ověřování** je nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="62462-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="62462-148">Pokud se zobrazí možnost výběru **cílové platformy**, vyberte nejnovější verzi 3. x.</span><span class="sxs-lookup"><span data-stu-id="62462-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="62462-149">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="62462-149">Select **Next**.</span></span>

* <span data-ttu-id="62462-150">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="62462-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS název projektu](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="62462-152">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="62462-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62462-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62462-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="62462-154">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="62462-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="62462-155">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="62462-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="62462-156">Všimněte si, že se v adresním řádku zobrazuje, `localhost:port#` a ne něco podobného `example.com` .</span><span class="sxs-lookup"><span data-stu-id="62462-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="62462-157">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="62462-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="62462-158">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="62462-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="62462-159">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="62462-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="62462-160">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="62462-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="62462-161">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="62462-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="62462-163">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="62462-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="62462-165">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="62462-165">The following image shows the app:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="62462-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62462-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="62462-168">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="62462-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="62462-169">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="62462-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="62462-170">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="62462-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="62462-171">To je proto, že `localhost` se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="62462-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="62462-172">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="62462-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="62462-173">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="62462-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="62462-174">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="62462-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62462-176">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="62462-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="62462-177">Kliknutím na **Spustit**  >  **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="62462-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="62462-178">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port` místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="62462-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="62462-179">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="62462-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="62462-180">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="62462-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="62462-181">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="62462-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="62462-182">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="62462-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="62462-183">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="62462-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="62462-184">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="62462-184">The following image shows the app:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="62462-186">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="62462-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="62462-187">Další</span><span class="sxs-lookup"><span data-stu-id="62462-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="62462-188">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="62462-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="62462-189">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="62462-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="62462-190">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="62462-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="62462-191">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="62462-191">Create a web app.</span></span>
> * <span data-ttu-id="62462-192">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="62462-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="62462-193">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="62462-193">Work with a database.</span></span>
> * <span data-ttu-id="62462-194">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="62462-194">Add search and validation.</span></span>

<span data-ttu-id="62462-195">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="62462-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="62462-196">Požadavky</span><span class="sxs-lookup"><span data-stu-id="62462-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62462-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62462-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="62462-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62462-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62462-199">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="62462-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="62462-200">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="62462-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62462-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62462-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="62462-202">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="62462-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="62462-203">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="62462-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="62462-205">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="62462-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="62462-206">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="62462-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)


* <span data-ttu-id="62462-208">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="62462-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="62462-209">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="62462-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="62462-210">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="62462-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="62462-211">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="62462-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="62462-212">Toto je základní počáteční projekt a je dobrým místem, kde začít.</span><span class="sxs-lookup"><span data-stu-id="62462-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="62462-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62462-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="62462-214">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="62462-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="62462-215">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="62462-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="62462-216">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="62462-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="62462-217">Změňte adresáře ( `cd` ) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="62462-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="62462-218">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="62462-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="62462-219">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="62462-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="62462-220">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="62462-220">Select **Yes**</span></span>

  * <span data-ttu-id="62462-221">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="62462-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="62462-222">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="62462-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62462-223">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="62462-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="62462-224">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="62462-224">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="62462-226">V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web (Model-View-Controller)**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="62462-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="62462-227">Ve verzi 8,6 nebo novější vyberte možnost Webová aplikace **webové a konzolová**  >  **aplikace**  >  **(model-zobrazení-kontroler)**  >  **Další**.</span><span class="sxs-lookup"><span data-stu-id="62462-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="62462-228">V dialogovém okně **Konfigurace nové webové aplikace** :</span><span class="sxs-lookup"><span data-stu-id="62462-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="62462-229">Potvrďte, že **ověřování** je nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="62462-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="62462-230">Pokud se zobrazí možnost výběru **cílové platformy**, vyberte nejnovější verzi 2. x.</span><span class="sxs-lookup"><span data-stu-id="62462-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="62462-231">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="62462-231">Select **Next**.</span></span>

* <span data-ttu-id="62462-232">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="62462-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="62462-233">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="62462-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="62462-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62462-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="62462-235">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="62462-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="62462-236">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="62462-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="62462-237">Všimněte si, že se v adresním řádku zobrazuje, `localhost:port#` a ne něco podobného `example.com` .</span><span class="sxs-lookup"><span data-stu-id="62462-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="62462-238">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="62462-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="62462-239">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="62462-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="62462-240">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="62462-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="62462-241">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="62462-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="62462-242">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="62462-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="62462-244">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="62462-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="62462-246">Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním.</span><span class="sxs-lookup"><span data-stu-id="62462-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="62462-247">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="62462-247">This app doesn't track personal information.</span></span> <span data-ttu-id="62462-248">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="62462-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="62462-250">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="62462-250">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="62462-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="62462-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="62462-253">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="62462-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="62462-254">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="62462-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="62462-255">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="62462-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="62462-256">To je proto, že `localhost` se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="62462-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="62462-257">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="62462-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="62462-258">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="62462-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="62462-259">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="62462-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="62462-260">Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním.</span><span class="sxs-lookup"><span data-stu-id="62462-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="62462-261">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="62462-261">This app doesn't track personal information.</span></span> <span data-ttu-id="62462-262">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="62462-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="62462-264">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="62462-264">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="62462-266">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="62462-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="62462-267">Kliknutím na **Spustit**  >  **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="62462-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="62462-268">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port` místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="62462-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="62462-269">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="62462-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="62462-270">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="62462-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="62462-271">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="62462-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="62462-272">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="62462-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="62462-273">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="62462-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="62462-274">Kliknutím na tlačítko **Přijmout** vyjádříte souhlas se sledováním.</span><span class="sxs-lookup"><span data-stu-id="62462-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="62462-275">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="62462-275">This app doesn't track personal information.</span></span> <span data-ttu-id="62462-276">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="62462-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="62462-278">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="62462-278">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="62462-280">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="62462-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="62462-281">Další</span><span class="sxs-lookup"><span data-stu-id="62462-281">Next</span></span>](adding-controller.md)

::: moniker-end
