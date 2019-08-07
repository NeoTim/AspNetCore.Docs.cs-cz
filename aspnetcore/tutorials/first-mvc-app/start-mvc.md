---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Naučte se, jak začít s ASP.NET Core MVC.
ms.author: riande
ms.date: 08/05/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: f6a92423546ebd9d4c8e1a92fb81b6b72f847f61
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820102"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="eadb9-103">Začínáme s ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="eadb9-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="eadb9-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eadb9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="eadb9-105">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="eadb9-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="eadb9-106">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="eadb9-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="eadb9-107">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="eadb9-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eadb9-108">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eadb9-108">Create a web app.</span></span>
> * <span data-ttu-id="eadb9-109">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="eadb9-110">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="eadb9-110">Work with a database.</span></span>
> * <span data-ttu-id="eadb9-111">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="eadb9-111">Add search and validation.</span></span>

<span data-ttu-id="eadb9-112">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="eadb9-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="eadb9-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="eadb9-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eadb9-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eadb9-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eadb9-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eadb9-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eadb9-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="eadb9-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="eadb9-117">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="eadb9-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eadb9-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eadb9-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eadb9-119">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="eadb9-120">Vyberte **ASP.NET Core webová aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="eadb9-122">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="eadb9-123">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="eadb9-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)

* <span data-ttu-id="eadb9-125">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="eadb9-126">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="eadb9-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="eadb9-127">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="eadb9-128">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="eadb9-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="eadb9-129">Toto je základní počáteční projekt.</span><span class="sxs-lookup"><span data-stu-id="eadb9-129">This is a basic starter project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eadb9-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eadb9-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eadb9-131">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="eadb9-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="eadb9-132">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="eadb9-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="eadb9-133">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="eadb9-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="eadb9-134">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="eadb9-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="eadb9-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="eadb9-135">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="eadb9-136">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="eadb9-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="eadb9-137">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="eadb9-137">Select **Yes**</span></span>

  * <span data-ttu-id="eadb9-138">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="eadb9-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="eadb9-139">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="eadb9-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eadb9-140">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="eadb9-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="eadb9-141">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-141">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="eadb9-143">Vyberte možnost Webová aplikace **.NET Core** > **App** > **(Model-View-Controller)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="eadb9-145">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílovou architekturu** **.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.0**.</span></span>

<!-- 
  ![macOS .NET Core 2.2 selection](./start-mvc/_static/new_project_22_vsmac.png)
-->

* <span data-ttu-id="eadb9-146">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-146">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="eadb9-147">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="eadb9-147">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eadb9-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eadb9-148">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eadb9-149">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="eadb9-149">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="eadb9-150">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eadb9-150">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="eadb9-151">Všimněte si, že se v `localhost:port#` adresním řádku zobrazuje `example.com`, a ne něco podobného.</span><span class="sxs-lookup"><span data-stu-id="eadb9-151">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eadb9-152">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="eadb9-152">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="eadb9-153">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="eadb9-153">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="eadb9-154">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-154">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="eadb9-155">Mnoho vývojářů upřednostňuje použít režim bez ladění pro rychlé spuštění aplikace a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="eadb9-155">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="eadb9-156">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="eadb9-156">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="eadb9-158">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="eadb9-158">You can debug the app by selecting the **IIS Express** button</span></span>

  ![Služba IIS Express](start-mvc/_static/iis_express.png)


  <span data-ttu-id="eadb9-160">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="eadb9-160">The following image shows the app:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eadb9-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eadb9-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eadb9-163">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-163">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="eadb9-164">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="eadb9-164">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="eadb9-165">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eadb9-165">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="eadb9-166">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="eadb9-166">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="eadb9-167">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="eadb9-167">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="eadb9-168">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-168">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="eadb9-169">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="eadb9-169">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="eadb9-170">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="eadb9-170">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="eadb9-171">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="eadb9-171">This app doesn't track personal information.</span></span> <span data-ttu-id="eadb9-172">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="eadb9-172">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="eadb9-174">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="eadb9-174">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eadb9-176">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="eadb9-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eadb9-177">Kliknutím na **Spustit** > **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eadb9-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="eadb9-178">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="eadb9-179">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eadb9-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eadb9-180">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="eadb9-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="eadb9-181">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="eadb9-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="eadb9-182">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="eadb9-183">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="eadb9-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="eadb9-184">Následující obrázek znázorňuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="eadb9-184">The following image shows the app:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="eadb9-186">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="eadb9-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="eadb9-187">Next</span><span class="sxs-lookup"><span data-stu-id="eadb9-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="eadb9-188">V tomto kurzu se naučíte základy vytváření ASP.NET Core webové aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="eadb9-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="eadb9-189">Aplikace spravuje databázi titulů filmů.</span><span class="sxs-lookup"><span data-stu-id="eadb9-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="eadb9-190">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="eadb9-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eadb9-191">Vytvořte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eadb9-191">Create a web app.</span></span>
> * <span data-ttu-id="eadb9-192">Přidání a vytvoření uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="eadb9-193">Práce s databází</span><span class="sxs-lookup"><span data-stu-id="eadb9-193">Work with a database.</span></span>
> * <span data-ttu-id="eadb9-194">Přidejte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="eadb9-194">Add search and validation.</span></span>

<span data-ttu-id="eadb9-195">Na konci máte aplikaci, která může spravovat a zobrazovat data filmů.</span><span class="sxs-lookup"><span data-stu-id="eadb9-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="eadb9-196">Požadavky</span><span class="sxs-lookup"><span data-stu-id="eadb9-196">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eadb9-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eadb9-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eadb9-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eadb9-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eadb9-199">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="eadb9-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="eadb9-200">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="eadb9-200">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eadb9-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eadb9-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eadb9-202">V aplikaci Visual Studio vyberte **vytvořit nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="eadb9-203">Selecct **ASP.NET Core webové aplikace** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-203">Selecct **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová ASP.NET Core webové aplikace](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="eadb9-205">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="eadb9-206">Je důležité pojmenovat projekt **MvcMovie** , takže když zkopírujete kód, obor názvů se bude shodovat.</span><span class="sxs-lookup"><span data-stu-id="eadb9-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová ASP.NET Core webové aplikace](start-mvc/_static/config.png)


* <span data-ttu-id="eadb9-208">Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="eadb9-209">Dialog Nový projekt, .NET Core v levém podokně, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="eadb9-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="eadb9-210">Aplikace Visual Studio použila pro projekt MVC, který jste právě vytvořili, výchozí šablonu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="eadb9-211">Máte funkční aplikaci hned teď zadáním názvu projektu a výběrem několika možností.</span><span class="sxs-lookup"><span data-stu-id="eadb9-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="eadb9-212">Toto je základní počáteční projekt a je dobrým místem, kde začít.</span><span class="sxs-lookup"><span data-stu-id="eadb9-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eadb9-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eadb9-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eadb9-214">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="eadb9-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="eadb9-215">Další informace najdete v tématu [Začínáme s vs Code](https://code.visualstudio.com/docs) a [Visual Studio Code nápovědě](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="eadb9-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="eadb9-216">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="eadb9-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="eadb9-217">Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="eadb9-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="eadb9-218">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="eadb9-218">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="eadb9-219">Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' MvcMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="eadb9-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="eadb9-220">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="eadb9-220">Select **Yes**</span></span>

  * <span data-ttu-id="eadb9-221">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC ve složce *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="eadb9-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="eadb9-222">`code -r MvcMovie`: Načte soubor projektu *MvcMovie. csproj* do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="eadb9-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eadb9-223">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="eadb9-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="eadb9-224">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-224">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="eadb9-226">Vyberte možnost Webová aplikace **.NET Core** > **App** > **(Model-View-Controller)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-226">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="eadb9-228">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou architekturu** **.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-228">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2,2 – výběr](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="eadb9-230">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="eadb9-230">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="eadb9-231">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="eadb9-231">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eadb9-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eadb9-232">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eadb9-233">Vyberte **CTRL + F5** a spusťte aplikaci v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="eadb9-233">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="eadb9-234">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eadb9-234">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="eadb9-235">Všimněte si, že se v `localhost:port#` adresním řádku zobrazuje `example.com`, a ne něco podobného.</span><span class="sxs-lookup"><span data-stu-id="eadb9-235">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eadb9-236">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="eadb9-236">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="eadb9-237">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="eadb9-237">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="eadb9-238">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-238">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="eadb9-239">Mnoho vývojářů upřednostňuje použít režim bez ladění pro rychlé spuštění aplikace a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="eadb9-239">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="eadb9-240">Aplikaci můžete spustit v režimu ladění nebo bez ladění z položky nabídky **ladění** :</span><span class="sxs-lookup"><span data-stu-id="eadb9-240">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Nabídka ladění](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="eadb9-242">Aplikaci můžete ladit tak, že vyberete tlačítko **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="eadb9-242">You can debug the app by selecting the **IIS Express** button</span></span>

  ![Služba IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="eadb9-244">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="eadb9-244">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="eadb9-245">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="eadb9-245">This app doesn't track personal information.</span></span> <span data-ttu-id="eadb9-246">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="eadb9-246">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="eadb9-248">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="eadb9-248">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eadb9-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eadb9-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eadb9-251">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-251">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="eadb9-252">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="eadb9-252">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="eadb9-253">Panel Adresa se zobrazí `localhost:port:5001` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eadb9-253">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="eadb9-254">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="eadb9-254">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="eadb9-255">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="eadb9-255">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="eadb9-256">Spuštění aplikace pomocí CTRL + F5 (režim bez ladění) umožňuje provádět změny kódu, uložit soubor, aktualizovat prohlížeč a zobrazit změny kódu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-256">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="eadb9-257">Mnoho vývojářů upřednostňuje použití režimu bez ladění k aktualizaci stránky a zobrazení změn.</span><span class="sxs-lookup"><span data-stu-id="eadb9-257">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="eadb9-258">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="eadb9-258">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="eadb9-259">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="eadb9-259">This app doesn't track personal information.</span></span> <span data-ttu-id="eadb9-260">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="eadb9-260">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](start-mvc/_static/privacy.png)

  <span data-ttu-id="eadb9-262">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="eadb9-262">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eadb9-264">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="eadb9-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eadb9-265">Kliknutím na **Spustit** > **Spustit bez ladění** spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eadb9-265">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="eadb9-266">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) Server, spustí prohlížeč a přejde na `http://localhost:port`místo, kde *port* je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-266">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="eadb9-267">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eadb9-267">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eadb9-268">Důvodem `localhost` je, že je standardní název hostitele pro váš místní počítač.</span><span class="sxs-lookup"><span data-stu-id="eadb9-268">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="eadb9-269">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="eadb9-269">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="eadb9-270">Po spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="eadb9-270">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="eadb9-271">Aplikaci můžete spustit v režimu ladění nebo bez ladění z nabídky **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="eadb9-271">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="eadb9-272">Vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="eadb9-272">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="eadb9-273">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="eadb9-273">This app doesn't track personal information.</span></span> <span data-ttu-id="eadb9-274">Kód vygenerovaný šablonou zahrnuje prostředky, které pomůžou splnit [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="eadb9-274">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="eadb9-276">Následující obrázek ukazuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="eadb9-276">The following image shows the app after accepting tracking:</span></span>

  ![Stránka domů nebo index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="eadb9-278">V další části tohoto kurzu se dozvíte o MVC a začněte psát kód.</span><span class="sxs-lookup"><span data-stu-id="eadb9-278">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="eadb9-279">Next</span><span class="sxs-lookup"><span data-stu-id="eadb9-279">Next</span></span>](adding-controller.md)

::: moniker-end
