---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Zjistěte, jak začít pracovat s ASP.NET Core MVC.
ms.author: riande
ms.date: 04/24/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 91564bac02df77632a3b56b6dbddeb3b622f6438
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555856"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="fc800-103">Začínáme s ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="fc800-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="fc800-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fc800-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="fc800-105">V tomto kurzu se naučíte se základy vytváření webovou aplikaci ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="fc800-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="fc800-106">Aplikaci spravuje databáze filmů produktů.</span><span class="sxs-lookup"><span data-stu-id="fc800-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="fc800-107">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="fc800-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fc800-108">Vytvoření webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fc800-108">Create a web app.</span></span>
> * <span data-ttu-id="fc800-109">Přidat a generování uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="fc800-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="fc800-110">Práce s databází.</span><span class="sxs-lookup"><span data-stu-id="fc800-110">Work with a database.</span></span>
> * <span data-ttu-id="fc800-111">Přidáte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="fc800-111">Add search and validation.</span></span>

<span data-ttu-id="fc800-112">Na konci budete mít aplikaci, která můžete spravovat a zobrazit data o filmech.</span><span class="sxs-lookup"><span data-stu-id="fc800-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="fc800-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="fc800-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fc800-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc800-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="fc800-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fc800-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="fc800-116">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="fc800-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="fc800-117">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="fc800-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fc800-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc800-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fc800-119">V sadě Visual Studio vyberte **vytvořte nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="fc800-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="fc800-120">Selecct **webové aplikace ASP.NET Core** a pak vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="fc800-120">Selecct **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nová webová aplikace ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="fc800-122">Pojmenujte projekt **MvcMovie** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fc800-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="fc800-123">Je důležité projekt pojmenujte **MvcMovie** tak při kopírování kódu budou odpovídat oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="fc800-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nová webová aplikace ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="fc800-125">Vyberte **webové Application(Model-View-Controller)** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fc800-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="fc800-126">Dialogové okno Nový projekt, .NET Core v levém podokně, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc800-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="fc800-127">Visual Studio používá výchozí šablonu pro projekt MVC, který jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="fc800-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="fc800-128">Zadáním názvu projektu a výběr několika možností Teď máte funkční aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fc800-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="fc800-129">Toto je základní počáteční projekt a je dobrým začátkem.</span><span class="sxs-lookup"><span data-stu-id="fc800-129">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="fc800-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fc800-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fc800-131">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="fc800-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="fc800-132">Zobrazit [Začínáme s VS Code](https://code.visualstudio.com/docs) a [nápovědy Visual Studio Code](#visual-studio-code-help) Další informace.</span><span class="sxs-lookup"><span data-stu-id="fc800-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="fc800-133">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fc800-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="fc800-134">Změňte adresář (`cd`) do složky, která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="fc800-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="fc800-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fc800-135">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="fc800-136">Zobrazí se dialogové okno s **'MvcMovie' chybí požadované prostředky pro sestavení a ladění. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="fc800-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="fc800-137">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="fc800-137">Select **Yes**</span></span>

  * <span data-ttu-id="fc800-138">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC v *MvcMovie* složky.</span><span class="sxs-lookup"><span data-stu-id="fc800-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="fc800-139">`code -r MvcMovie`: Načtení *MvcMovie.csproj* soubor projektu ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fc800-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="fc800-140">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="fc800-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fc800-141">Vyberte **souboru** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="fc800-141">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="fc800-143">Vyberte **.NET Core** > **aplikace** > **webová aplikace (Model-View-Controller)**  > **Další**.</span><span class="sxs-lookup"><span data-stu-id="fc800-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="fc800-145">V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z **.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="fc800-145">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS výběr .NET Core 2.2](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="fc800-147">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="fc800-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="fc800-148">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="fc800-148">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fc800-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc800-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fc800-150">Vyberte **Ctrl-F5** ke spuštění aplikace v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="fc800-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="fc800-151">Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fc800-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="fc800-152">Všimněte si, že do adresního řádku ukazuje `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="fc800-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fc800-153">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="fc800-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="fc800-154">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="fc800-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="fc800-155">Spuštění aplikace pomocí kombinace kláves Ctrl + F5 (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu.</span><span class="sxs-lookup"><span data-stu-id="fc800-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fc800-156">Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.</span><span class="sxs-lookup"><span data-stu-id="fc800-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="fc800-157">Spustíte ji v ladění nebo v režimu bez ladění z **ladění** položky nabídky:</span><span class="sxs-lookup"><span data-stu-id="fc800-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Ladění nabídky](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="fc800-159">Můžete ladit aplikaci tak, že vyberete **služby IIS Express** tlačítko</span><span class="sxs-lookup"><span data-stu-id="fc800-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![Služba IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="fc800-161">Vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="fc800-161">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="fc800-162">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="fc800-162">This app doesn't track personal information.</span></span> <span data-ttu-id="fc800-163">Tento kód vygenerovanou šablonu obsahuje prostředky, které vám pomohou splnit [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="fc800-163">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](start-mvc/_static/privacy.png)

  <span data-ttu-id="fc800-165">Následující obrázek znázorňuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="fc800-165">The following image shows the app after accepting tracking:</span></span>

  ![Index nebo Domovská stránka](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="fc800-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fc800-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fc800-168">Stisknutím kláves Ctrl + F5 ke spuštění bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="fc800-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="fc800-169">Spustí Visual Studio Code [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fc800-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="fc800-170">Zobrazí se panel Adresa `localhost:port:5001` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="fc800-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="fc800-171">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="fc800-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="fc800-172">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="fc800-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="fc800-173">Spuštění aplikace pomocí kombinace kláves Ctrl + F5 (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu.</span><span class="sxs-lookup"><span data-stu-id="fc800-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="fc800-174">Mnoho vývojářů dávají přednost používání režimu bez ladění a aktualizujte stránku, podívejte se na změny.</span><span class="sxs-lookup"><span data-stu-id="fc800-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="fc800-175">Vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="fc800-175">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="fc800-176">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="fc800-176">This app doesn't track personal information.</span></span> <span data-ttu-id="fc800-177">Tento kód vygenerovanou šablonu obsahuje prostředky, které vám pomohou splnit [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="fc800-177">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](start-mvc/_static/privacy.png)

  <span data-ttu-id="fc800-179">Následující obrázek znázorňuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="fc800-179">The following image shows the app after accepting tracking:</span></span>

  ![Index nebo Domovská stránka](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="fc800-181">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="fc800-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fc800-182">Vyberte **spustit** > **spustit bez ladění** aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="fc800-182">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="fc800-183">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) serveru, spustí se prohlížeč a přejde na `http://localhost:port`, kde *port* je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="fc800-183">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="fc800-184">Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="fc800-184">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fc800-185">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="fc800-185">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="fc800-186">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="fc800-186">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="fc800-187">Při spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="fc800-187">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="fc800-188">Spustíte ji v ladění nebo v režimu bez ladění z **spustit** nabídky.</span><span class="sxs-lookup"><span data-stu-id="fc800-188">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="fc800-189">Vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="fc800-189">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="fc800-190">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="fc800-190">This app doesn't track personal information.</span></span> <span data-ttu-id="fc800-191">Tento kód vygenerovanou šablonu obsahuje prostředky, které vám pomohou splnit [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="fc800-191">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="fc800-193">Následující obrázek znázorňuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="fc800-193">The following image shows the app after accepting tracking:</span></span>

  ![Index nebo Domovská stránka](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="fc800-195">V další části tohoto kurzu přečtěte si o MVC a začít psát kód.</span><span class="sxs-lookup"><span data-stu-id="fc800-195">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fc800-196">Next</span><span class="sxs-lookup"><span data-stu-id="fc800-196">Next</span></span>](adding-controller.md)
