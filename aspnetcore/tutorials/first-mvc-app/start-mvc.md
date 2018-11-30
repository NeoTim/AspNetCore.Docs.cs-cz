---
title: Začínáme s ASP.NET Core MVC a sady Visual Studio
author: rick-anderson
description: Zjistěte, jak začít pracovat s ASP.NET Core MVC a sady Visual Studio.
ms.author: riande
ms.date: 10/07/2017
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 9d50607899058c887597a3d73198552d3ef5b020
ms.sourcegitcommit: c4572be5ebb301013a5698caf9b5572b76cb2e34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52710085"
---
# <a name="get-started-with-aspnet-core-mvc-and-visual-studio"></a><span data-ttu-id="4427c-103">Začínáme s ASP.NET Core MVC a sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4427c-103">Get started with ASP.NET Core MVC and Visual Studio</span></span>

<span data-ttu-id="4427c-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4427c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="4427c-105">Existují 3 verze tohoto kurzu:</span><span class="sxs-lookup"><span data-stu-id="4427c-105">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="4427c-106">macOS: [vytvoření aplikace ASP.NET Core MVC se sadou Visual Studio pro Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="4427c-106">macOS: [Create an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="4427c-107">Windows: [vytvořit v aplikaci MVC ASP.NET Core pomocí sady Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="4427c-107">Windows: [Create an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="4427c-108">macOS, Linux a Windows: [vytvoření aplikace ASP.NET Core MVC pomocí Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="4427c-108">macOS, Linux, and Windows: [Create an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

> [!NOTE]
> <span data-ttu-id="4427c-109">Testujeme použitelnost navrhované nové struktury pro obsah ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4427c-109">We’re testing the usability of a proposed new structure for the ASP.NET Core table of contents.</span></span>  <span data-ttu-id="4427c-110">Pokud máte pár minut a chcete si vyzkoušet cvičení, které spočívá ve vyhledání 7 různých témat v aktuálním nebo navrhovaném obsahu, [klikněte prosím sem a zúčastněte se studie](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).</span><span class="sxs-lookup"><span data-stu-id="4427c-110">If you have a few minutes to try an exercise of finding 7 different topics in the current or proposed table of contents, please [click here to participate in the study](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).</span></span>

## <a name="install-visual-studio-and-net-core"></a><span data-ttu-id="4427c-111">Instalace sady Visual Studio a .NET Core</span><span class="sxs-lookup"><span data-stu-id="4427c-111">Install Visual Studio and .NET Core</span></span>

::: moniker range=">= aspnetcore-2.1"

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="4427c-112">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="4427c-112">Create a web app</span></span>

<span data-ttu-id="4427c-113">Ze sady Visual Studio, vyberte **soubor > Nový > projekt**.</span><span class="sxs-lookup"><span data-stu-id="4427c-113">From Visual Studio, select  **File > New > Project**.</span></span>

![Soubor > Nový > Projekt](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="4427c-115">Dokončení **nový projekt** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="4427c-115">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="4427c-116">V levém podokně, klepněte na **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="4427c-116">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="4427c-117">V prostředním podokně, klepněte na **webová aplikace ASP.NET Core (.NET Core)**</span><span class="sxs-lookup"><span data-stu-id="4427c-117">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="4427c-118">Pojmenujte projekt "MvcMovie" (je třeba název projektu "MvcMovie", takže při kopírování kódu bude odpovídat oboru názvů.)</span><span class="sxs-lookup"><span data-stu-id="4427c-118">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="4427c-119">Klepněte na **OK**</span><span class="sxs-lookup"><span data-stu-id="4427c-119">Tap **OK**</span></span>

![<span data-ttu-id="4427c-120">Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4427c-120">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2-21.png)

<span data-ttu-id="4427c-121">Dokončení **nové základní webové aplikace ASP.NET (.NET Core) – MvcMovie** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="4427c-121">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="4427c-122">V poli verze selektoru rozevíracího seznamu vyberte **ASP.NET Core 2.1**</span><span class="sxs-lookup"><span data-stu-id="4427c-122">In the version selector drop-down box select **ASP.NET Core 2.1**</span></span>
* <span data-ttu-id="4427c-123">Vyberte **webová aplikace (Model-View-Controller)**</span><span class="sxs-lookup"><span data-stu-id="4427c-123">Select **Web Application (Model-View-Controller)**</span></span>
* <span data-ttu-id="4427c-124">Klepněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="4427c-124">Tap **OK**.</span></span>

![<span data-ttu-id="4427c-125">Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4427c-125">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="4427c-126">Visual Studio používá výchozí šablonu pro projekt MVC, který jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="4427c-126">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="4427c-127">Zadáním názvu projektu a výběr několika možností Teď máte funkční aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4427c-127">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="4427c-128">Toto je základní počáteční projekt a je dobrým začátkem.</span><span class="sxs-lookup"><span data-stu-id="4427c-128">This is a basic starter project, and it's a good place to start.</span></span>

<span data-ttu-id="4427c-129">Klepněte na **F5** ke spuštění aplikace v režimu ladění nebo **Ctrl-F5** v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="4427c-129">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<span data-ttu-id="4427c-130"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![spuštění aplikace](start-mvc/_static/1.png)</span><span class="sxs-lookup"><span data-stu-id="4427c-130"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![running app](start-mvc/_static/1.png)</span></span>

* <span data-ttu-id="4427c-131">Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spouští vaše aplikace.</span><span class="sxs-lookup"><span data-stu-id="4427c-131">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="4427c-132">Všimněte si, že do adresního řádku ukazuje `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4427c-132">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4427c-133">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="4427c-133">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="4427c-134">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="4427c-134">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="4427c-135">Na obrázku výše je číslo portu 5000.</span><span class="sxs-lookup"><span data-stu-id="4427c-135">In the image above, the port number is 5000.</span></span> <span data-ttu-id="4427c-136">Adresa URL v prohlížeči zobrazí `localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="4427c-136">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="4427c-137">Při spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="4427c-137">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="4427c-138">Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu.</span><span class="sxs-lookup"><span data-stu-id="4427c-138">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="4427c-139">Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.</span><span class="sxs-lookup"><span data-stu-id="4427c-139">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="4427c-140">Spustíte ji v ladění nebo v režimu bez ladění z **ladění** položky nabídky:</span><span class="sxs-lookup"><span data-stu-id="4427c-140">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Ladění nabídky](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="4427c-142">Můžete ladit aplikaci klepnutím **služby IIS Express** tlačítko</span><span class="sxs-lookup"><span data-stu-id="4427c-142">You can debug the app by tapping the **IIS Express** button</span></span>

![Služba IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="4427c-144">Výchozí šablony vám práci **domácí o** a **kontakt** odkazy.</span><span class="sxs-lookup"><span data-stu-id="4427c-144">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="4427c-145">Výše uvedené prohlížeče obraz se nezobrazí těchto odkazů.</span><span class="sxs-lookup"><span data-stu-id="4427c-145">The browser image above doesn't show these links.</span></span> <span data-ttu-id="4427c-146">V závislosti na velikosti vašeho prohlížeče mohou muset kliknout na ikonu navigace se budou zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="4427c-146">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![Ikona navigace v vpravo nahoře](start-mvc/_static/2.png)

<span data-ttu-id="4427c-148">Pokud jsou spuštěné v režimu ladění, klepněte na **Shift + F5** chcete zastavit ladění.</span><span class="sxs-lookup"><span data-stu-id="4427c-148">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="4427c-149">V další části tohoto kurzu přidáme další informace o MVC a začít psát kód.</span><span class="sxs-lookup"><span data-stu-id="4427c-149">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!INCLUDE [](~/includes/net-core-prereqs.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="4427c-150">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="4427c-150">Create a web app</span></span>

<span data-ttu-id="4427c-151">Ze sady Visual Studio, vyberte **soubor > Nový > projekt**.</span><span class="sxs-lookup"><span data-stu-id="4427c-151">From Visual Studio, select  **File > New > Project**.</span></span>

![Soubor > Nový > Projekt](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="4427c-153">Dokončení **nový projekt** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="4427c-153">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="4427c-154">V levém podokně, klepněte na **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="4427c-154">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="4427c-155">V prostředním podokně, klepněte na **webová aplikace ASP.NET Core (.NET Core)**</span><span class="sxs-lookup"><span data-stu-id="4427c-155">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="4427c-156">Pojmenujte projekt "MvcMovie" (je třeba název projektu "MvcMovie", takže při kopírování kódu bude odpovídat oboru názvů.)</span><span class="sxs-lookup"><span data-stu-id="4427c-156">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="4427c-157">Klepněte na **OK**</span><span class="sxs-lookup"><span data-stu-id="4427c-157">Tap **OK**</span></span>

![<span data-ttu-id="4427c-158">Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4427c-158">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2.png)

<span data-ttu-id="4427c-159">Dokončení **nové základní webové aplikace ASP.NET (.NET Core) – MvcMovie** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="4427c-159">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="4427c-160">V poli verze selektoru rozevíracího seznamu vyberte **ASP.NET Core 2.-**</span><span class="sxs-lookup"><span data-stu-id="4427c-160">In the version selector drop-down box select **ASP.NET Core 2.-**</span></span>
* <span data-ttu-id="4427c-161">Vyberte **webové Application(Model-View-Controller)**</span><span class="sxs-lookup"><span data-stu-id="4427c-161">Select **Web Application(Model-View-Controller)**</span></span>
* <span data-ttu-id="4427c-162">Klepněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="4427c-162">Tap **OK**.</span></span>

![<span data-ttu-id="4427c-163">Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4427c-163">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22.png)

<span data-ttu-id="4427c-164">Visual Studio používá výchozí šablonu pro projekt MVC, který jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="4427c-164">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="4427c-165">Zadáním názvu projektu a výběr několika možností Teď máte funkční aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4427c-165">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="4427c-166">Toto je základní počáteční projekt, a je vhodné oddělení na zahájení,</span><span class="sxs-lookup"><span data-stu-id="4427c-166">This is a basic starter project, and it's a good place to start,</span></span>

<span data-ttu-id="4427c-167">Klepněte na **F5** ke spuštění aplikace v režimu ladění nebo **Ctrl-F5** v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="4427c-167">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<span data-ttu-id="4427c-168"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![spuštění aplikace](start-mvc/_static/1.png)</span><span class="sxs-lookup"><span data-stu-id="4427c-168"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![running app](start-mvc/_static/1.png)</span></span>

* <span data-ttu-id="4427c-169">Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spouští vaše aplikace.</span><span class="sxs-lookup"><span data-stu-id="4427c-169">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="4427c-170">Všimněte si, že do adresního řádku ukazuje `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4427c-170">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4427c-171">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="4427c-171">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="4427c-172">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="4427c-172">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="4427c-173">Na obrázku výše je číslo portu 5000.</span><span class="sxs-lookup"><span data-stu-id="4427c-173">In the image above, the port number is 5000.</span></span> <span data-ttu-id="4427c-174">Adresa URL v prohlížeči zobrazí `localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="4427c-174">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="4427c-175">Při spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="4427c-175">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="4427c-176">Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu.</span><span class="sxs-lookup"><span data-stu-id="4427c-176">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="4427c-177">Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.</span><span class="sxs-lookup"><span data-stu-id="4427c-177">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="4427c-178">Spustíte ji v ladění nebo v režimu bez ladění z **ladění** položky nabídky:</span><span class="sxs-lookup"><span data-stu-id="4427c-178">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Ladění nabídky](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="4427c-180">Můžete ladit aplikaci klepnutím **služby IIS Express** tlačítko</span><span class="sxs-lookup"><span data-stu-id="4427c-180">You can debug the app by tapping the **IIS Express** button</span></span>

![Služba IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="4427c-182">Výchozí šablony vám práci **domácí o** a **kontakt** odkazy.</span><span class="sxs-lookup"><span data-stu-id="4427c-182">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="4427c-183">Výše uvedené prohlížeče obraz se nezobrazí těchto odkazů.</span><span class="sxs-lookup"><span data-stu-id="4427c-183">The browser image above doesn't show these links.</span></span> <span data-ttu-id="4427c-184">V závislosti na velikosti vašeho prohlížeče mohou muset kliknout na ikonu navigace se budou zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="4427c-184">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![Ikona navigace v vpravo nahoře](start-mvc/_static/2.png)

<span data-ttu-id="4427c-186">Pokud jsou spuštěné v režimu ladění, klepněte na **Shift + F5** chcete zastavit ladění.</span><span class="sxs-lookup"><span data-stu-id="4427c-186">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="4427c-187">V další části tohoto kurzu přidáme další informace o MVC a začít psát kód.</span><span class="sxs-lookup"><span data-stu-id="4427c-187">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> [<span data-ttu-id="4427c-188">Next</span><span class="sxs-lookup"><span data-stu-id="4427c-188">Next</span></span>](adding-controller.md)  
