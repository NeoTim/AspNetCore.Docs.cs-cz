---
title: Začínáme s ASP.NET Core MVC
author: rick-anderson
description: Zjistěte, jak začít pracovat s ASP.NET Core MVC.
ms.author: riande
ms.date: 12/12/2018
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: f0c2351de017de7f4c62021b8f9478603055e9bc
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410544"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="38d02-103">Začínáme s ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="38d02-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="38d02-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="38d02-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

https://docs.microsoft.com/en-us/visualstudio/ide/visual-studio-ide?view=vs-2017

<span data-ttu-id="38d02-105">V tomto kurzu se naučíte se základy vytváření webovou aplikaci ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="38d02-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="38d02-106">Aplikaci spravuje databáze filmů produktů.</span><span class="sxs-lookup"><span data-stu-id="38d02-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="38d02-107">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="38d02-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="38d02-108">Vytvoření webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="38d02-108">Create a web app.</span></span>
> * <span data-ttu-id="38d02-109">Přidat a generování uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="38d02-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="38d02-110">Práce s databází.</span><span class="sxs-lookup"><span data-stu-id="38d02-110">Work with a database.</span></span>
> * <span data-ttu-id="38d02-111">Přidáte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="38d02-111">Add search and validation.</span></span>

<span data-ttu-id="38d02-112">Na konci budete mít aplikaci, která můžete spravovat a zobrazit data o filmech.</span><span class="sxs-lookup"><span data-stu-id="38d02-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

> [!NOTE]
> <span data-ttu-id="38d02-113">Testujeme použitelnost navrhované nové struktury pro obsah ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="38d02-113">We’re testing the usability of a proposed new structure for the ASP.NET Core table of contents.</span></span>  <span data-ttu-id="38d02-114">Pokud máte pár minut a chcete si vyzkoušet cvičení, které spočívá ve vyhledání 7 různých témat v aktuálním nebo navrhovaném obsahu, [klikněte prosím sem a zúčastněte se studie](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span><span class="sxs-lookup"><span data-stu-id="38d02-114">If you have a few minutes to try an exercise of finding 7 different topics in the current or proposed table of contents, please [click here to participate in the study](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="38d02-115">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="38d02-115">Create a web app</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="38d02-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38d02-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="38d02-117">Ze sady Visual Studio, vyberte **soubor > Nový > projekt**.</span><span class="sxs-lookup"><span data-stu-id="38d02-117">From Visual Studio, select  **File > New > Project**.</span></span>

![Soubor > Nový > Projekt](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="38d02-119">Dokončení **nový projekt** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="38d02-119">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="38d02-120">V levém podokně vyberte **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="38d02-120">In the left pane, select **.NET Core**</span></span>
* <span data-ttu-id="38d02-121">V prostředním podokně vyberte **webová aplikace ASP.NET Core (.NET Core)**</span><span class="sxs-lookup"><span data-stu-id="38d02-121">In the center pane, select **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="38d02-122">Pojmenujte projekt "MvcMovie" (je třeba název projektu "MvcMovie", takže při kopírování kódu bude odpovídat oboru názvů.)</span><span class="sxs-lookup"><span data-stu-id="38d02-122">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="38d02-123">Vyberte **OK**</span><span class="sxs-lookup"><span data-stu-id="38d02-123">select **OK**</span></span>

![<span data-ttu-id="38d02-124">Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="38d02-124">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2-21.png)

<span data-ttu-id="38d02-125">Dokončení **nové základní webové aplikace ASP.NET (.NET Core) – MvcMovie** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="38d02-125">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="38d02-126">V poli verze selektoru rozevíracího seznamu vyberte **2.2 technologie ASP.NET Core**</span><span class="sxs-lookup"><span data-stu-id="38d02-126">In the version selector drop-down box select **ASP.NET Core 2.2**</span></span>
* <span data-ttu-id="38d02-127">Vyberte **webová aplikace (Model-View-Controller)**</span><span class="sxs-lookup"><span data-stu-id="38d02-127">Select **Web Application (Model-View-Controller)**</span></span>
* <span data-ttu-id="38d02-128">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="38d02-128">select **OK**.</span></span>

![<span data-ttu-id="38d02-129">Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="38d02-129">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="38d02-130">Visual Studio používá výchozí šablonu pro projekt MVC, který jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="38d02-130">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="38d02-131">Zadáním názvu projektu a výběr několika možností Teď máte funkční aplikaci.</span><span class="sxs-lookup"><span data-stu-id="38d02-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="38d02-132">Toto je základní počáteční projekt a je dobrým začátkem.</span><span class="sxs-lookup"><span data-stu-id="38d02-132">This is a basic starter project, and it's a good place to start.</span></span>

<span data-ttu-id="38d02-133">Vyberte **Ctrl-F5** ke spuštění aplikace v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="38d02-133">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

* <span data-ttu-id="38d02-134">Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spouští vaše aplikace.</span><span class="sxs-lookup"><span data-stu-id="38d02-134">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="38d02-135">Všimněte si, že do adresního řádku ukazuje `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="38d02-135">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="38d02-136">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="38d02-136">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="38d02-137">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="38d02-137">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="38d02-138">Na obrázku výše je číslo portu 5000.</span><span class="sxs-lookup"><span data-stu-id="38d02-138">In the image above, the port number is 5000.</span></span> <span data-ttu-id="38d02-139">Adresa URL v prohlížeči zobrazí `localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="38d02-139">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="38d02-140">Při spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="38d02-140">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="38d02-141">Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu.</span><span class="sxs-lookup"><span data-stu-id="38d02-141">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="38d02-142">Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.</span><span class="sxs-lookup"><span data-stu-id="38d02-142">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="38d02-143">Spustíte ji v ladění nebo v režimu bez ladění z **ladění** položky nabídky:</span><span class="sxs-lookup"><span data-stu-id="38d02-143">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Ladění nabídky](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="38d02-145">Můžete ladit aplikaci tak, že vyberete **služby IIS Express** tlačítko</span><span class="sxs-lookup"><span data-stu-id="38d02-145">You can debug the app by selecting the **IIS Express** button</span></span>

![Služba IIS Express](start-mvc/_static/iis_express.png)

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="38d02-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38d02-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="38d02-148">Kurz předpokládá familarity s VS Code.</span><span class="sxs-lookup"><span data-stu-id="38d02-148">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="38d02-149">Zobrazit [Začínáme s VS Code](https://code.visualstudio.com/docs) a [nápovědy Visual Studio Code](#visual-studio-code-help) Další informace.</span><span class="sxs-lookup"><span data-stu-id="38d02-149">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="38d02-150">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="38d02-150">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="38d02-151">Změňte adresář (`cd`) do složky, která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="38d02-151">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="38d02-152">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="38d02-152">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="38d02-153">Zobrazí se dialogové okno s **'MvcMovie' chybí požadované prostředky pro sestavení a ladění. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="38d02-153">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="38d02-154">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="38d02-154">Select **Yes**</span></span>

  * <span data-ttu-id="38d02-155">`dotnet new mvc -o MvcMovie`: vytvoří nový projekt ASP.NET Core MVC v *MvcMovie* složky.</span><span class="sxs-lookup"><span data-stu-id="38d02-155">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="38d02-156">`code -r MvcMovie`: Načtení *MvcMovie.csproj* soubor projektu ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="38d02-156">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="38d02-157">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="38d02-157">Launch the app</span></span>

* <span data-ttu-id="38d02-158">Stisknutím klávesy **Ctrl-F5** spustit bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="38d02-158">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="38d02-159">Visual Studio Code spuštění spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="38d02-159">Visual Studio Code starts starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="38d02-160">Zobrazí se panel Adresa `localhost:port:5001` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="38d02-160">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="38d02-161">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="38d02-161">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="38d02-162">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="38d02-162">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="38d02-163">Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu.</span><span class="sxs-lookup"><span data-stu-id="38d02-163">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="38d02-164">Mnoho vývojářů dávají přednost používání režimu bez ladění a aktualizujte stránku, podívejte se na změny.</span><span class="sxs-lookup"><span data-stu-id="38d02-164">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="38d02-165">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="38d02-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="38d02-166">Vyberte **souboru** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="38d02-166">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](~/tutorials/first-web-api-mac/_static/sln.png)

* <span data-ttu-id="38d02-168">Vyberte **aplikace .NET Core** > **ASP.NET Core** > **webové aplikace ASP.NET Core (MVC)** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="38d02-168">Select **.NET Core App** > **ASP.NET Core** > **ASP.NET Core Web App (MVC)** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](~/tutorials/first-mvc-app-mac/start-mvc/1.png)

* <span data-ttu-id="38d02-170">V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="38d02-170">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="38d02-171">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="38d02-171">Name the project **MvcMovie**, and then select **Create**.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="38d02-172">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="38d02-172">Launch the app</span></span>

<span data-ttu-id="38d02-173">Vyberte **spustit** > **spustit bez ladění** aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="38d02-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="38d02-174">Visual Studio pro Mac spustí [Kestrel](xref:fundamentals/servers/index#kestrel) serveru, spustí se prohlížeč a přejde na `http://localhost:port`, kde *port* je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="38d02-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

* <span data-ttu-id="38d02-175">Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="38d02-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="38d02-176">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="38d02-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="38d02-177">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="38d02-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="38d02-178">Při spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="38d02-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="38d02-179">Spustíte ji v ladění nebo v režimu bez ladění z **spustit** nabídky.</span><span class="sxs-lookup"><span data-stu-id="38d02-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

---  
<!-- End of VS tabs -->

* <span data-ttu-id="38d02-180">Vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="38d02-180">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="38d02-181">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="38d02-181">This app doesn't track personal information.</span></span> <span data-ttu-id="38d02-182">Tento kód vygenerovanou šablonu obsahuje prostředky, které vám pomohou splnit [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="38d02-182">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](start-mvc/_static/privacy.png)

  <span data-ttu-id="38d02-184">Následující obrázek znázorňuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="38d02-184">The following image shows the app after accepting tracking:</span></span>

  ![Index nebo Domovská stránka](start-mvc/_static/home2.2.png)

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="38d02-186">V další části tohoto kurzu přečtěte si o MVC a začít psát kód.</span><span class="sxs-lookup"><span data-stu-id="38d02-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="38d02-187">Next</span><span class="sxs-lookup"><span data-stu-id="38d02-187">Next</span></span>](adding-controller.md)  
