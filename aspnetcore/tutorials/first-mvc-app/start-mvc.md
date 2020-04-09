---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Přečtěte si, jak začít s ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662475"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="91474-103">Začínáme s ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="91474-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="91474-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="91474-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="91474-105">Tento kurz učí základy vytváření ASP.NET webové aplikace Core MVC.</span><span class="sxs-lookup"><span data-stu-id="91474-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="91474-106">Aplikace spravuje databázi filmových titulů.</span><span class="sxs-lookup"><span data-stu-id="91474-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="91474-107">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="91474-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="91474-108">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="91474-108">Create a web app.</span></span>
> * <span data-ttu-id="91474-109">Přidejte a lešení modelu.</span><span class="sxs-lookup"><span data-stu-id="91474-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="91474-110">Práce s databází.</span><span class="sxs-lookup"><span data-stu-id="91474-110">Work with a database.</span></span>
> * <span data-ttu-id="91474-111">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="91474-111">Add search and validation.</span></span>

<span data-ttu-id="91474-112">Na konci máte aplikaci, která může spravovat a zobrazovat filmová data.</span><span class="sxs-lookup"><span data-stu-id="91474-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="91474-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="91474-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91474-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91474-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="91474-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91474-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91474-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="91474-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="91474-117">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="91474-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91474-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91474-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91474-119">V sadě Visual Studio vyberte **Vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="91474-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="91474-120">Vyberte **ASP.NET základní webovou aplikaci** a pak vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="91474-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![nová ASP.NET základní webová aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="91474-122">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="91474-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="91474-123">Je důležité pojmenovat projekt **MvcMovie,** takže při kopírování kódu se bude shodovat jmenovcí prostor.</span><span class="sxs-lookup"><span data-stu-id="91474-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nová ASP.NET základní webová aplikace](start-mvc/_static/config.png)

* <span data-ttu-id="91474-125">Vyberte **webovou aplikaci (model-view-controller)** a pak vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="91474-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="91474-126">Dialogové okno Nový projekt, .NET Core v levém podokně, ASP.NET web Core</span><span class="sxs-lookup"><span data-stu-id="91474-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="91474-127">Visual Studio použilo výchozí šablonu pro projekt MVC, který jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="91474-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="91474-128">Máte pracovní aplikaci právě teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="91474-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="91474-129">Jedná se o základní startovací projekt.</span><span class="sxs-lookup"><span data-stu-id="91474-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91474-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91474-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91474-131">Kurz předpokládá familaritu s VS Code.</span><span class="sxs-lookup"><span data-stu-id="91474-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="91474-132">Další informace najdete [v tématu Začínáme s nápovědou VS Code](https://code.visualstudio.com/docs) a [Visual Studio Code.](#visual-studio-code-help)</span><span class="sxs-lookup"><span data-stu-id="91474-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="91474-133">Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="91474-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="91474-134">Změňte adresáře (`cd`) na složku, která bude projekt obsahovat.</span><span class="sxs-lookup"><span data-stu-id="91474-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="91474-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="91474-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="91474-136">V programu MvcMovie se zobrazí dialogové okno s **požadovanými datovými zdroji k sestavení a laděním. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="91474-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="91474-137">Vybrat **ano**</span><span class="sxs-lookup"><span data-stu-id="91474-137">Select **Yes**</span></span>

  * <span data-ttu-id="91474-138">`dotnet new mvc -o MvcMovie`: vytvoří nový ASP.NET core mvc projektu ve složce *MvcMovie.*</span><span class="sxs-lookup"><span data-stu-id="91474-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="91474-139">`code -r MvcMovie`: Načte soubor projektu *MvcMovie.csproj* v kódu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="91474-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91474-140">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="91474-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91474-141">Vyberte **možnost Nové řešení souboru** > **New Solution**.</span><span class="sxs-lookup"><span data-stu-id="91474-141">Select **File** > **New Solution**.</span></span>

  ![macOS Nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="91474-143">Vyberte webovou **aplikaci** > **.NET Core** > App **Web Application (Model-View-Controller)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="91474-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Dialogové okno Nový projekt](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="91474-145">V dialogovém **okně Konfigurovat nové webové rozhraní API ASP.NET** nastavte **cílovou architekturu** **rozhraní .NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="91474-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![výběr macOS .NET Core 3.1](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="91474-147">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="91474-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="91474-148">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="91474-148">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91474-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91474-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91474-150">Chcete-li aplikaci spustit v režimu bez ladění, vyberte **Ctrl-F5.**</span><span class="sxs-lookup"><span data-stu-id="91474-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="91474-151">Visual Studio spustí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="91474-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="91474-152">Všimněte si, `localhost:port#` že se `example.com`zobrazuje adresní řádek a ne něco jako .</span><span class="sxs-lookup"><span data-stu-id="91474-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="91474-153">Je to `localhost` proto, že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="91474-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="91474-154">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="91474-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="91474-155">Spuštění aplikace s Ctrl + F5 (non-ladění režimu) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč, a vidět změny kódu.</span><span class="sxs-lookup"><span data-stu-id="91474-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="91474-156">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="91474-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="91474-157">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **Ladění:**</span><span class="sxs-lookup"><span data-stu-id="91474-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="91474-159">Aplikaci můžete ladit výběrem tlačítka **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="91474-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="91474-161">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="91474-161">The following image shows the app:</span></span>

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="91474-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91474-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91474-164">Stisknutím kláves Ctrl+F5 spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="91474-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="91474-165">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `https://localhost:5001`a přejde na .</span><span class="sxs-lookup"><span data-stu-id="91474-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="91474-166">Adresní řádek `localhost:port:5001` zobrazuje a `example.com`ne něco jako .</span><span class="sxs-lookup"><span data-stu-id="91474-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="91474-167">To proto, `localhost` že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="91474-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="91474-168">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="91474-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="91474-169">Spuštění aplikace s Ctrl + F5 (non-ladění režimu) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč, a vidět změny kódu.</span><span class="sxs-lookup"><span data-stu-id="91474-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="91474-170">Mnoho vývojářů dává přednost použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="91474-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91474-172">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="91474-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="91474-173">Chcete-li aplikaci spustit spustit bez ladění, vyberte spustit**spustit bez ladění.** **Run** > </span><span class="sxs-lookup"><span data-stu-id="91474-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="91474-174">Visual Studio for Mac spustí [server Kestrel,](xref:fundamentals/servers/index#kestrel) spustí `http://localhost:port`prohlížeč a přejde na , kde *port* je náhodně vybrané číslo portu.</span><span class="sxs-lookup"><span data-stu-id="91474-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="91474-175">Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako .</span><span class="sxs-lookup"><span data-stu-id="91474-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="91474-176">Je to `localhost` proto, že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="91474-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="91474-177">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="91474-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="91474-178">Když aplikaci spustíte, zobrazí se jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="91474-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="91474-179">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit.**</span><span class="sxs-lookup"><span data-stu-id="91474-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="91474-180">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="91474-180">The following image shows the app:</span></span>

  ![Domovská stránka nebo stránka Rejstříku](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="91474-182">V další části tohoto kurzu se dozvíte o MVC a začnete psát nějaký kód.</span><span class="sxs-lookup"><span data-stu-id="91474-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="91474-183">Další</span><span class="sxs-lookup"><span data-stu-id="91474-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="91474-184">Tento kurz učí základy vytváření ASP.NET webové aplikace Core MVC.</span><span class="sxs-lookup"><span data-stu-id="91474-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="91474-185">Aplikace spravuje databázi filmových titulů.</span><span class="sxs-lookup"><span data-stu-id="91474-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="91474-186">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="91474-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="91474-187">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="91474-187">Create a web app.</span></span>
> * <span data-ttu-id="91474-188">Přidejte a lešení modelu.</span><span class="sxs-lookup"><span data-stu-id="91474-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="91474-189">Práce s databází.</span><span class="sxs-lookup"><span data-stu-id="91474-189">Work with a database.</span></span>
> * <span data-ttu-id="91474-190">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="91474-190">Add search and validation.</span></span>

<span data-ttu-id="91474-191">Na konci máte aplikaci, která může spravovat a zobrazovat filmová data.</span><span class="sxs-lookup"><span data-stu-id="91474-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="91474-192">Požadavky</span><span class="sxs-lookup"><span data-stu-id="91474-192">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91474-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91474-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="91474-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91474-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91474-195">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="91474-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="91474-196">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="91474-196">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91474-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91474-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91474-198">V sadě Visual Studio vyberte **Vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="91474-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="91474-199">Vyberte **ASP.NET základní webovou aplikaci** a pak vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="91474-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![nová ASP.NET základní webová aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="91474-201">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="91474-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="91474-202">Je důležité pojmenovat projekt **MvcMovie,** takže při kopírování kódu se bude shodovat jmenovcí prostor.</span><span class="sxs-lookup"><span data-stu-id="91474-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nová ASP.NET základní webová aplikace](start-mvc/_static/config.png)


* <span data-ttu-id="91474-204">Vyberte **webovou aplikaci (model-view-controller)** a pak vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="91474-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="91474-205">Dialogové okno Nový projekt, .NET Core v levém podokně, ASP.NET web Core</span><span class="sxs-lookup"><span data-stu-id="91474-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="91474-206">Visual Studio použilo výchozí šablonu pro projekt MVC, který jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="91474-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="91474-207">Máte pracovní aplikaci právě teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="91474-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="91474-208">Jedná se o základní startovací projekt a je to dobré místo pro začátek.</span><span class="sxs-lookup"><span data-stu-id="91474-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="91474-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91474-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91474-210">Kurz předpokládá familaritu s VS Code.</span><span class="sxs-lookup"><span data-stu-id="91474-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="91474-211">Další informace najdete [v tématu Začínáme s nápovědou VS Code](https://code.visualstudio.com/docs) a [Visual Studio Code.](#visual-studio-code-help)</span><span class="sxs-lookup"><span data-stu-id="91474-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="91474-212">Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="91474-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="91474-213">Změňte adresáře (`cd`) na složku, která bude projekt obsahovat.</span><span class="sxs-lookup"><span data-stu-id="91474-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="91474-214">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="91474-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="91474-215">V programu MvcMovie se zobrazí dialogové okno s **požadovanými datovými zdroji k sestavení a laděním. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="91474-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="91474-216">Vybrat **ano**</span><span class="sxs-lookup"><span data-stu-id="91474-216">Select **Yes**</span></span>

  * <span data-ttu-id="91474-217">`dotnet new mvc -o MvcMovie`: vytvoří nový ASP.NET core mvc projektu ve složce *MvcMovie.*</span><span class="sxs-lookup"><span data-stu-id="91474-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="91474-218">`code -r MvcMovie`: Načte soubor projektu *MvcMovie.csproj* v kódu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="91474-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91474-219">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="91474-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91474-220">Vyberte **možnost Nové řešení souboru** > **New Solution**.</span><span class="sxs-lookup"><span data-stu-id="91474-220">Select **File** > **New Solution**.</span></span>

  ![macOS Nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="91474-222">Vyberte webovou **aplikaci** > **.NET Core** > App **Web Application (Model-View-Controller)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="91474-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Dialogové okno Nový projekt](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="91474-224">V dialogovém **okně Konfigurovat nové ASP.NET základní webové rozhraní API** přijměte výchozí **cílovou architekturu** **rozhraní .NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="91474-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![výběr macOS .NET Core 2.2](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="91474-226">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="91474-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="91474-227">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="91474-227">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91474-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91474-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91474-229">Chcete-li aplikaci spustit v režimu bez ladění, vyberte **Ctrl-F5.**</span><span class="sxs-lookup"><span data-stu-id="91474-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="91474-230">Visual Studio spustí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="91474-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="91474-231">Všimněte si, `localhost:port#` že se `example.com`zobrazuje adresní řádek a ne něco jako .</span><span class="sxs-lookup"><span data-stu-id="91474-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="91474-232">Je to `localhost` proto, že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="91474-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="91474-233">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="91474-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="91474-234">Spuštění aplikace s Ctrl + F5 (non-ladění režimu) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč, a vidět změny kódu.</span><span class="sxs-lookup"><span data-stu-id="91474-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="91474-235">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="91474-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="91474-236">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **Ladění:**</span><span class="sxs-lookup"><span data-stu-id="91474-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="91474-238">Aplikaci můžete ladit výběrem tlačítka **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="91474-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="91474-240">Chcete-li souhlasit se sledováním, vyberte **možnost Přijmout.**</span><span class="sxs-lookup"><span data-stu-id="91474-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="91474-241">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="91474-241">This app doesn't track personal information.</span></span> <span data-ttu-id="91474-242">Šablona generovaný kód obsahuje aktiva, která pomáhají splnit [obecné nařízení o ochraně osobních údajů (GDPR).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="91474-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/privacy.png)

  <span data-ttu-id="91474-244">Následující obrázek znázorňuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="91474-244">The following image shows the app after accepting tracking:</span></span>

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="91474-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91474-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91474-247">Stisknutím kláves Ctrl+F5 spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="91474-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="91474-248">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `https://localhost:5001`a přejde na .</span><span class="sxs-lookup"><span data-stu-id="91474-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="91474-249">Adresní řádek `localhost:port:5001` zobrazuje a `example.com`ne něco jako .</span><span class="sxs-lookup"><span data-stu-id="91474-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="91474-250">To proto, `localhost` že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="91474-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="91474-251">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="91474-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="91474-252">Spuštění aplikace s Ctrl + F5 (non-ladění režimu) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč, a vidět změny kódu.</span><span class="sxs-lookup"><span data-stu-id="91474-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="91474-253">Mnoho vývojářů dává přednost použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="91474-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="91474-254">Chcete-li souhlasit se sledováním, vyberte **možnost Přijmout.**</span><span class="sxs-lookup"><span data-stu-id="91474-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="91474-255">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="91474-255">This app doesn't track personal information.</span></span> <span data-ttu-id="91474-256">Šablona generovaný kód obsahuje aktiva, která pomáhají splnit [obecné nařízení o ochraně osobních údajů (GDPR).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="91474-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/privacy.png)

  <span data-ttu-id="91474-258">Následující obrázek znázorňuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="91474-258">The following image shows the app after accepting tracking:</span></span>

  ![Domovská stránka nebo stránka Rejstříku](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91474-260">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="91474-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="91474-261">Chcete-li aplikaci spustit spustit bez ladění, vyberte spustit**spustit bez ladění.** **Run** > </span><span class="sxs-lookup"><span data-stu-id="91474-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="91474-262">Visual Studio for Mac spustí [server Kestrel,](xref:fundamentals/servers/index#kestrel) spustí `http://localhost:port`prohlížeč a přejde na , kde *port* je náhodně vybrané číslo portu.</span><span class="sxs-lookup"><span data-stu-id="91474-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="91474-263">Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako .</span><span class="sxs-lookup"><span data-stu-id="91474-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="91474-264">Je to `localhost` proto, že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="91474-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="91474-265">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="91474-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="91474-266">Když aplikaci spustíte, zobrazí se jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="91474-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="91474-267">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit.**</span><span class="sxs-lookup"><span data-stu-id="91474-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="91474-268">Chcete-li souhlasit se sledováním, vyberte **možnost Přijmout.**</span><span class="sxs-lookup"><span data-stu-id="91474-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="91474-269">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="91474-269">This app doesn't track personal information.</span></span> <span data-ttu-id="91474-270">Šablona generovaný kód obsahuje aktiva, která pomáhají splnit [obecné nařízení o ochraně osobních údajů (GDPR).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="91474-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Domovská stránka nebo stránka Rejstříku](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="91474-272">Následující obrázek znázorňuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="91474-272">The following image shows the app after accepting tracking:</span></span>

  ![Domovská stránka nebo stránka Rejstříku](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="91474-274">V další části tohoto kurzu se dozvíte o MVC a začnete psát nějaký kód.</span><span class="sxs-lookup"><span data-stu-id="91474-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="91474-275">Další</span><span class="sxs-lookup"><span data-stu-id="91474-275">Next</span></span>](adding-controller.md)

::: moniker-end
