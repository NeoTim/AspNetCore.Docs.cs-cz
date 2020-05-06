---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Naučte se, jak začít s ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 0b28e99d9c56d92e157e9010880e58ec5165196a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774982"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="3b361-103">Začínáme s ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="3b361-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="3b361-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3b361-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="3b361-105">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="3b361-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="3b361-106">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="3b361-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="3b361-107">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="3b361-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3b361-108">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b361-108">Create a web app.</span></span>
> * <span data-ttu-id="3b361-109">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="3b361-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="3b361-110">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="3b361-110">Work with a database.</span></span>
> * <span data-ttu-id="3b361-111">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="3b361-111">Add search and validation.</span></span>

<span data-ttu-id="3b361-112">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="3b361-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="3b361-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3b361-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b361-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b361-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b361-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b361-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b361-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b361-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="3b361-117">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="3b361-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b361-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b361-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b361-119">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="3b361-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="3b361-120">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3b361-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="3b361-122">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b361-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="3b361-123">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="3b361-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)

* <span data-ttu-id="3b361-125">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b361-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="3b361-126">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="3b361-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="3b361-127">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="3b361-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="3b361-128">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="3b361-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="3b361-129">Toto je základní počáteční projekt.</span><span class="sxs-lookup"><span data-stu-id="3b361-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b361-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b361-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3b361-131">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="3b361-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="3b361-132">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="3b361-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="3b361-133">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3b361-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3b361-134">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="3b361-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="3b361-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3b361-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="3b361-136">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="3b361-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="3b361-137">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="3b361-137">Select **Yes**</span></span>

  * <span data-ttu-id="3b361-138">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="3b361-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="3b361-139">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3b361-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b361-140">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b361-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3b361-141">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="3b361-141">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="3b361-143">Vyberte možnost Webová aplikace **.NET Core** > **App** > **(Model-View-Controller)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="3b361-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![dialog pro nový projekt v macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="3b361-145">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílovou architekturu** **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="3b361-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3,1 – výběr](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="3b361-147">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b361-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="3b361-148">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="3b361-148">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b361-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b361-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3b361-150">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="3b361-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="3b361-151">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b361-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="3b361-152">Všimněte si, že se v `localhost:port#` adresním řádku zobrazuje `example.com`, a ne něco podobného.</span><span class="sxs-lookup"><span data-stu-id="3b361-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3b361-153">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="3b361-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3b361-154">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="3b361-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="3b361-155">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="3b361-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3b361-156">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="3b361-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="3b361-157">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="3b361-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="3b361-159">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="3b361-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="3b361-161">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3b361-161">The following image shows the app:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b361-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b361-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3b361-164">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="3b361-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="3b361-165">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3b361-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="3b361-166">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3b361-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="3b361-167">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="3b361-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="3b361-168">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="3b361-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="3b361-169">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="3b361-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3b361-170">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="3b361-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b361-172">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b361-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3b361-173">Kliknutím na **Spustit** > **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b361-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="3b361-174">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="3b361-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="3b361-175">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3b361-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3b361-176">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="3b361-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3b361-177">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="3b361-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="3b361-178">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="3b361-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="3b361-179">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="3b361-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="3b361-180">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="3b361-180">The following image shows the app:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="3b361-182">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="3b361-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3b361-183">Další</span><span class="sxs-lookup"><span data-stu-id="3b361-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="3b361-184">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="3b361-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="3b361-185">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="3b361-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="3b361-186">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="3b361-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3b361-187">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b361-187">Create a web app.</span></span>
> * <span data-ttu-id="3b361-188">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="3b361-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="3b361-189">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="3b361-189">Work with a database.</span></span>
> * <span data-ttu-id="3b361-190">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="3b361-190">Add search and validation.</span></span>

<span data-ttu-id="3b361-191">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="3b361-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="3b361-192">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3b361-192">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b361-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b361-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b361-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b361-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b361-195">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b361-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="3b361-196">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="3b361-196">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b361-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b361-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b361-198">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="3b361-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="3b361-199">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3b361-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="3b361-201">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b361-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="3b361-202">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="3b361-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)


* <span data-ttu-id="3b361-204">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b361-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="3b361-205">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="3b361-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="3b361-206">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="3b361-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="3b361-207">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="3b361-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="3b361-208">Toto je základní počáteční projekt a je dobrým místem, kde začít.</span><span class="sxs-lookup"><span data-stu-id="3b361-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b361-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b361-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3b361-210">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="3b361-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="3b361-211">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="3b361-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="3b361-212">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3b361-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3b361-213">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="3b361-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="3b361-214">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3b361-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="3b361-215">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="3b361-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="3b361-216">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="3b361-216">Select **Yes**</span></span>

  * <span data-ttu-id="3b361-217">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="3b361-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="3b361-218">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3b361-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b361-219">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b361-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3b361-220">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="3b361-220">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="3b361-222">Vyberte možnost Webová aplikace **.NET Core** > **App** > **(Model-View-Controller)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="3b361-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![dialog pro nový projekt v macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="3b361-224">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou architekturu** **.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="3b361-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2,2 – výběr](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="3b361-226">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b361-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="3b361-227">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="3b361-227">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b361-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b361-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3b361-229">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="3b361-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="3b361-230">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b361-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="3b361-231">Všimněte si, že se v `localhost:port#` adresním řádku zobrazuje `example.com`, a ne něco podobného.</span><span class="sxs-lookup"><span data-stu-id="3b361-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3b361-232">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="3b361-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3b361-233">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="3b361-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="3b361-234">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="3b361-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3b361-235">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="3b361-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="3b361-236">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="3b361-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="3b361-238">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="3b361-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="3b361-240">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="3b361-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="3b361-241">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="3b361-241">This app doesn't track personal information.</span></span> <span data-ttu-id="3b361-242">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="3b361-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="3b361-244">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="3b361-244">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b361-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b361-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3b361-247">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="3b361-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="3b361-248">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3b361-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="3b361-249">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3b361-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="3b361-250">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="3b361-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="3b361-251">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="3b361-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="3b361-252">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="3b361-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3b361-253">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="3b361-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="3b361-254">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="3b361-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="3b361-255">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="3b361-255">This app doesn't track personal information.</span></span> <span data-ttu-id="3b361-256">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="3b361-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="3b361-258">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="3b361-258">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b361-260">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b361-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3b361-261">Kliknutím na **Spustit** > **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b361-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="3b361-262">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="3b361-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="3b361-263">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3b361-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3b361-264">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="3b361-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3b361-265">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="3b361-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="3b361-266">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="3b361-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="3b361-267">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="3b361-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="3b361-268">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="3b361-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="3b361-269">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="3b361-269">This app doesn't track personal information.</span></span> <span data-ttu-id="3b361-270">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="3b361-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="3b361-272">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="3b361-272">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="3b361-274">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="3b361-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3b361-275">Další</span><span class="sxs-lookup"><span data-stu-id="3b361-275">Next</span></span>](adding-controller.md)

::: moniker-end
