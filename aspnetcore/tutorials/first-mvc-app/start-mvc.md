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
ms.openlocfilehash: c2b76b59ae775b9268fa77019bf8420e5e4108b6
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452274"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="8b10e-103">Začínáme s ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="8b10e-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="8b10e-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8b10e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="8b10e-105">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="8b10e-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="8b10e-106">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="8b10e-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="8b10e-107">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="8b10e-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8b10e-108">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8b10e-108">Create a web app.</span></span>
> * <span data-ttu-id="8b10e-109">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="8b10e-110">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="8b10e-110">Work with a database.</span></span>
> * <span data-ttu-id="8b10e-111">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="8b10e-111">Add search and validation.</span></span>

<span data-ttu-id="8b10e-112">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="8b10e-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="8b10e-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8b10e-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b10e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b10e-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b10e-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b10e-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b10e-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b10e-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="8b10e-117">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="8b10e-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b10e-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b10e-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b10e-119">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="8b10e-120">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="8b10e-122">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="8b10e-123">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="8b10e-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)

* <span data-ttu-id="8b10e-125">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="8b10e-126">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="8b10e-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="8b10e-127">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="8b10e-128">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="8b10e-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="8b10e-129">Toto je základní počáteční projekt.</span><span class="sxs-lookup"><span data-stu-id="8b10e-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b10e-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b10e-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8b10e-131">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="8b10e-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="8b10e-132">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="8b10e-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="8b10e-133">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8b10e-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8b10e-134">Změňte adresáře ( `cd` ) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="8b10e-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="8b10e-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8b10e-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="8b10e-136">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="8b10e-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="8b10e-137">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="8b10e-137">Select **Yes**</span></span>

  * <span data-ttu-id="8b10e-138">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="8b10e-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="8b10e-139">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8b10e-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b10e-140">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b10e-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8b10e-141">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-141">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="8b10e-143">V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web (Model-View-Controller)**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="8b10e-144">Ve verzi 8,6 nebo novější vyberte možnost Webová aplikace **webové a konzolová**  >  **aplikace**  >  **(model-zobrazení-kontroler)**  >  **Další**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Výběr šablony webové aplikace v macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="8b10e-146">Potvrďte následující konfigurace:</span><span class="sxs-lookup"><span data-stu-id="8b10e-146">Confirm the following configurations:</span></span>

  * <span data-ttu-id="8b10e-147">**Cílová architektura** je nastavená na **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-147">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="8b10e-148">**Ověřování** nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-148">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="8b10e-149">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-149">Select **Next**.</span></span>

  ![macOS .NET Core 3,1 – výběr](start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="8b10e-151">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS název projektu](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="8b10e-153">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="8b10e-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b10e-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b10e-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8b10e-155">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="8b10e-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="8b10e-156">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8b10e-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="8b10e-157">Všimněte si, že se v adresním řádku zobrazuje, `localhost:port#` a ne něco podobného `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b10e-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8b10e-158">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="8b10e-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8b10e-159">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="8b10e-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="8b10e-160">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8b10e-161">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="8b10e-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="8b10e-162">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="8b10e-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="8b10e-164">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="8b10e-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="8b10e-166">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="8b10e-166">The following image shows the app:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b10e-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b10e-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8b10e-169">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="8b10e-170">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="8b10e-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="8b10e-171">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b10e-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="8b10e-172">To je proto, že `localhost` se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="8b10e-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="8b10e-173">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="8b10e-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="8b10e-174">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8b10e-175">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="8b10e-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b10e-177">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b10e-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8b10e-178">Kliknutím na **Spustit**  >  **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8b10e-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="8b10e-179">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port` místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="8b10e-180">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b10e-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8b10e-181">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="8b10e-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8b10e-182">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="8b10e-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="8b10e-183">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="8b10e-184">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="8b10e-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="8b10e-185">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="8b10e-185">The following image shows the app:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="8b10e-187">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="8b10e-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8b10e-188">Další</span><span class="sxs-lookup"><span data-stu-id="8b10e-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="8b10e-189">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="8b10e-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="8b10e-190">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="8b10e-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="8b10e-191">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="8b10e-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8b10e-192">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8b10e-192">Create a web app.</span></span>
> * <span data-ttu-id="8b10e-193">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="8b10e-194">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="8b10e-194">Work with a database.</span></span>
> * <span data-ttu-id="8b10e-195">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="8b10e-195">Add search and validation.</span></span>

<span data-ttu-id="8b10e-196">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="8b10e-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="8b10e-197">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8b10e-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b10e-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b10e-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b10e-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b10e-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b10e-200">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b10e-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="8b10e-201">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="8b10e-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b10e-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b10e-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b10e-203">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="8b10e-204">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="8b10e-206">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="8b10e-207">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="8b10e-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)


* <span data-ttu-id="8b10e-209">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="8b10e-210">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="8b10e-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="8b10e-211">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="8b10e-212">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="8b10e-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="8b10e-213">Toto je základní počáteční projekt a je dobrým místem, kde začít.</span><span class="sxs-lookup"><span data-stu-id="8b10e-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b10e-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b10e-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8b10e-215">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="8b10e-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="8b10e-216">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="8b10e-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="8b10e-217">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8b10e-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8b10e-218">Změňte adresáře ( `cd` ) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="8b10e-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="8b10e-219">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8b10e-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="8b10e-220">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="8b10e-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="8b10e-221">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="8b10e-221">Select **Yes**</span></span>

  * <span data-ttu-id="8b10e-222">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="8b10e-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="8b10e-223">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8b10e-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b10e-224">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b10e-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8b10e-225">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-225">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="8b10e-227">V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web (Model-View-Controller)**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="8b10e-228">Ve verzi 8,6 nebo novější vyberte možnost Webová aplikace **webové a konzolová**  >  **aplikace**  >  **(model-zobrazení-kontroler)**  >  **Další**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="8b10e-229">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou architekturu** **.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-229">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2,2 – výběr](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="8b10e-231">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8b10e-231">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="8b10e-232">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="8b10e-232">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b10e-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b10e-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8b10e-234">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="8b10e-234">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="8b10e-235">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8b10e-235">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="8b10e-236">Všimněte si, že se v adresním řádku zobrazuje, `localhost:port#` a ne něco podobného `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b10e-236">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8b10e-237">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="8b10e-237">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8b10e-238">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="8b10e-238">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="8b10e-239">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-239">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8b10e-240">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="8b10e-240">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="8b10e-241">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="8b10e-241">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="8b10e-243">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="8b10e-243">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="8b10e-245">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="8b10e-245">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="8b10e-246">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="8b10e-246">This app doesn't track personal information.</span></span> <span data-ttu-id="8b10e-247">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="8b10e-247">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="8b10e-249">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="8b10e-249">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8b10e-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b10e-251">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8b10e-252">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-252">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="8b10e-253">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="8b10e-253">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="8b10e-254">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b10e-254">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="8b10e-255">To je proto, že `localhost` se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="8b10e-255">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="8b10e-256">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="8b10e-256">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="8b10e-257">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-257">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8b10e-258">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="8b10e-258">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="8b10e-259">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="8b10e-259">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="8b10e-260">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="8b10e-260">This app doesn't track personal information.</span></span> <span data-ttu-id="8b10e-261">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="8b10e-261">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="8b10e-263">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="8b10e-263">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b10e-265">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b10e-265">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8b10e-266">Kliknutím na **Spustit**  >  **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8b10e-266">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="8b10e-267">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port` místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-267">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="8b10e-268">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="8b10e-268">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8b10e-269">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="8b10e-269">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8b10e-270">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="8b10e-270">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="8b10e-271">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="8b10e-271">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="8b10e-272">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="8b10e-272">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="8b10e-273">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="8b10e-273">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="8b10e-274">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="8b10e-274">This app doesn't track personal information.</span></span> <span data-ttu-id="8b10e-275">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="8b10e-275">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="8b10e-277">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="8b10e-277">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="8b10e-279">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="8b10e-279">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8b10e-280">Další</span><span class="sxs-lookup"><span data-stu-id="8b10e-280">Next</span></span>](adding-controller.md)

::: moniker-end
