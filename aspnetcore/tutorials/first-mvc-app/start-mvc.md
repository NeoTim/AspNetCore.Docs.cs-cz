---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Naučte se, jak začít s ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 01321d68defafbe79371250669f921307bcfdba6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407041"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="fe105-103">Začínáme s ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="fe105-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="fe105-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fe105-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="fe105-105">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="fe105-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="fe105-106">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="fe105-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="fe105-107">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="fe105-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe105-108">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe105-108">Create a web app.</span></span>
> * <span data-ttu-id="fe105-109">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="fe105-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="fe105-110">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="fe105-110">Work with a database.</span></span>
> * <span data-ttu-id="fe105-111">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="fe105-111">Add search and validation.</span></span>

<span data-ttu-id="fe105-112">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="fe105-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="fe105-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="fe105-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe105-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe105-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe105-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe105-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe105-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="fe105-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="fe105-117">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="fe105-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe105-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe105-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fe105-119">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="fe105-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="fe105-120">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="fe105-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="fe105-122">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fe105-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="fe105-123">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="fe105-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)

* <span data-ttu-id="fe105-125">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fe105-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="fe105-126">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="fe105-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="fe105-127">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="fe105-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="fe105-128">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="fe105-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="fe105-129">Toto je základní počáteční projekt.</span><span class="sxs-lookup"><span data-stu-id="fe105-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe105-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe105-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fe105-131">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="fe105-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="fe105-132">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="fe105-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="fe105-133">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fe105-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="fe105-134">Změňte adresáře ( `cd` ) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="fe105-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="fe105-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fe105-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="fe105-136">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="fe105-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="fe105-137">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="fe105-137">Select **Yes**</span></span>

  * <span data-ttu-id="fe105-138">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="fe105-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="fe105-139">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fe105-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe105-140">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="fe105-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fe105-141">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="fe105-141">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="fe105-143">V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web (Model-View-Controller)**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="fe105-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="fe105-144">Ve verzi 8,6 nebo novější vyberte možnost Webová aplikace **webové a konzolová**  >  **aplikace**  >  **(model-zobrazení-kontroler)**  >  **Další**.</span><span class="sxs-lookup"><span data-stu-id="fe105-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Výběr šablony webové aplikace v macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="fe105-146">Potvrďte následující konfigurace:</span><span class="sxs-lookup"><span data-stu-id="fe105-146">Confirm the following configurations:</span></span>

  * <span data-ttu-id="fe105-147">**Cílová architektura** je nastavená na **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="fe105-147">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="fe105-148">**Ověřování** nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="fe105-148">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="fe105-149">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="fe105-149">Select **Next**.</span></span>

  ![macOS .NET Core 3,1 – výběr](start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="fe105-151">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fe105-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS název projektu](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="fe105-153">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="fe105-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe105-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe105-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fe105-155">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="fe105-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="fe105-156">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe105-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="fe105-157">Všimněte si, že se v adresním řádku zobrazuje, `localhost:port#` a ne něco podobného `example.com` .</span><span class="sxs-lookup"><span data-stu-id="fe105-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fe105-158">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="fe105-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="fe105-159">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="fe105-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="fe105-160">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="fe105-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fe105-161">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="fe105-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="fe105-162">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="fe105-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="fe105-164">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="fe105-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="fe105-166">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fe105-166">The following image shows the app:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe105-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe105-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fe105-169">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="fe105-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="fe105-170">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="fe105-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="fe105-171">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="fe105-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="fe105-172">To je proto, že `localhost` se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="fe105-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="fe105-173">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="fe105-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="fe105-174">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="fe105-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fe105-175">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="fe105-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe105-177">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="fe105-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fe105-178">Kliknutím na **Spustit**  >  **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe105-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="fe105-179">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port` místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="fe105-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="fe105-180">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="fe105-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fe105-181">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="fe105-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="fe105-182">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="fe105-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="fe105-183">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="fe105-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="fe105-184">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="fe105-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="fe105-185">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fe105-185">The following image shows the app:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="fe105-187">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="fe105-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fe105-188">Další</span><span class="sxs-lookup"><span data-stu-id="fe105-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="fe105-189">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="fe105-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="fe105-190">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="fe105-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="fe105-191">Získáte informace o těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="fe105-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe105-192">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe105-192">Create a web app.</span></span>
> * <span data-ttu-id="fe105-193">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="fe105-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="fe105-194">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="fe105-194">Work with a database.</span></span>
> * <span data-ttu-id="fe105-195">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="fe105-195">Add search and validation.</span></span>

<span data-ttu-id="fe105-196">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="fe105-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="fe105-197">Požadavky</span><span class="sxs-lookup"><span data-stu-id="fe105-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe105-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe105-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe105-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe105-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe105-200">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="fe105-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="fe105-201">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="fe105-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe105-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe105-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fe105-203">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="fe105-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="fe105-204">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="fe105-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="fe105-206">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fe105-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="fe105-207">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="fe105-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)


* <span data-ttu-id="fe105-209">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fe105-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="fe105-210">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="fe105-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="fe105-211">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="fe105-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="fe105-212">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="fe105-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="fe105-213">Toto je základní počáteční projekt a je dobrým místem, kde začít.</span><span class="sxs-lookup"><span data-stu-id="fe105-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe105-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe105-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fe105-215">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="fe105-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="fe105-216">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="fe105-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="fe105-217">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fe105-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="fe105-218">Změňte adresáře ( `cd` ) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="fe105-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="fe105-219">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fe105-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="fe105-220">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="fe105-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="fe105-221">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="fe105-221">Select **Yes**</span></span>

  * <span data-ttu-id="fe105-222">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="fe105-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="fe105-223">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fe105-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe105-224">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="fe105-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fe105-225">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="fe105-225">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="fe105-227">V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web (Model-View-Controller)**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="fe105-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="fe105-228">Ve verzi 8,6 nebo novější vyberte možnost Webová aplikace **webové a konzolová**  >  **aplikace**  >  **(model-zobrazení-kontroler)**  >  **Další**.</span><span class="sxs-lookup"><span data-stu-id="fe105-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="fe105-229">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou architekturu** **.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="fe105-229">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2,2 – výběr](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="fe105-231">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fe105-231">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="fe105-232">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="fe105-232">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe105-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe105-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fe105-234">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="fe105-234">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="fe105-235">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe105-235">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="fe105-236">Všimněte si, že se v adresním řádku zobrazuje, `localhost:port#` a ne něco podobného `example.com` .</span><span class="sxs-lookup"><span data-stu-id="fe105-236">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fe105-237">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="fe105-237">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="fe105-238">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="fe105-238">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="fe105-239">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="fe105-239">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fe105-240">Řada vývojářů dává přednost používání režimu bez ladění, aby mohli aplikaci rychle spustit a podívat se na změny.</span><span class="sxs-lookup"><span data-stu-id="fe105-240">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="fe105-241">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="fe105-241">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="fe105-243">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="fe105-243">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="fe105-245">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="fe105-245">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="fe105-246">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="fe105-246">This app doesn't track personal information.</span></span> <span data-ttu-id="fe105-247">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="fe105-247">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="fe105-249">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="fe105-249">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fe105-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fe105-251">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fe105-252">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="fe105-252">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="fe105-253">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="fe105-253">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="fe105-254">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="fe105-254">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="fe105-255">To je proto, že `localhost` se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="fe105-255">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="fe105-256">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="fe105-256">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="fe105-257">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="fe105-257">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fe105-258">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="fe105-258">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="fe105-259">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="fe105-259">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="fe105-260">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="fe105-260">This app doesn't track personal information.</span></span> <span data-ttu-id="fe105-261">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="fe105-261">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="fe105-263">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="fe105-263">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fe105-265">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="fe105-265">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fe105-266">Kliknutím na **Spustit**  >  **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe105-266">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="fe105-267">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port` místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="fe105-267">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="fe105-268">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="fe105-268">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fe105-269">Důvodem je, že `localhost` je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="fe105-269">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="fe105-270">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="fe105-270">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="fe105-271">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="fe105-271">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="fe105-272">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="fe105-272">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="fe105-273">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="fe105-273">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="fe105-274">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="fe105-274">This app doesn't track personal information.</span></span> <span data-ttu-id="fe105-275">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="fe105-275">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="fe105-277">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="fe105-277">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="fe105-279">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="fe105-279">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fe105-280">Další</span><span class="sxs-lookup"><span data-stu-id="fe105-280">Next</span></span>](adding-controller.md)

::: moniker-end
