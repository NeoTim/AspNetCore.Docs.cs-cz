---
title: Začínáme s ASP.NET Core MVC a sady Visual Studio
author: rick-anderson
description: Zjistěte, jak začít pracovat s ASP.NET Core MVC a sady Visual Studio.
ms.author: riande
ms.date: 10/07/2017
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: dfc9a864cf10db5fc44d5631dcbb2f73325d9e14
ms.sourcegitcommit: a16352c1c88a71770ab3922200a8cd148fb278a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53335322"
---
# <a name="get-started-with-aspnet-core-mvc-and-visual-studio"></a><span data-ttu-id="8e7ac-103">Začínáme s ASP.NET Core MVC a sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e7ac-103">Get started with ASP.NET Core MVC and Visual Studio</span></span>

<span data-ttu-id="8e7ac-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8e7ac-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="8e7ac-105">Existují 3 verze tohoto kurzu:</span><span class="sxs-lookup"><span data-stu-id="8e7ac-105">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="8e7ac-106">macOS: [Vytvořte v aplikaci MVC ASP.NET Core pomocí sady Visual Studio pro Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="8e7ac-106">macOS: [Create an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="8e7ac-107">Windows: [Vytvořte v aplikaci MVC ASP.NET Core pomocí sady Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="8e7ac-107">Windows: [Create an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="8e7ac-108">macOS, Linux a Windows: [Vytvoření ASP.NET Core MVC aplikace sadou Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="8e7ac-108">macOS, Linux, and Windows: [Create an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

## <a name="install-visual-studio-and-net-core"></a><span data-ttu-id="8e7ac-109">Instalace sady Visual Studio a .NET Core</span><span class="sxs-lookup"><span data-stu-id="8e7ac-109">Install Visual Studio and .NET Core</span></span>

::: moniker range=">= aspnetcore-2.1"

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="8e7ac-110">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="8e7ac-110">Create a web app</span></span>

<span data-ttu-id="8e7ac-111">Ze sady Visual Studio, vyberte **soubor > Nový > projekt**.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-111">From Visual Studio, select  **File > New > Project**.</span></span>

![Soubor > Nový > Projekt](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="8e7ac-113">Dokončení **nový projekt** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="8e7ac-113">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="8e7ac-114">V levém podokně, klepněte na **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="8e7ac-114">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="8e7ac-115">V prostředním podokně, klepněte na **webová aplikace ASP.NET Core (.NET Core)**</span><span class="sxs-lookup"><span data-stu-id="8e7ac-115">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="8e7ac-116">Pojmenujte projekt "MvcMovie" (je třeba název projektu "MvcMovie", takže při kopírování kódu bude odpovídat oboru názvů.)</span><span class="sxs-lookup"><span data-stu-id="8e7ac-116">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="8e7ac-117">Klepněte na **OK**</span><span class="sxs-lookup"><span data-stu-id="8e7ac-117">Tap **OK**</span></span>

![<span data-ttu-id="8e7ac-118">Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e7ac-118">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2-21.png)

<span data-ttu-id="8e7ac-119">Dokončení **nové základní webové aplikace ASP.NET (.NET Core) – MvcMovie** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="8e7ac-119">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="8e7ac-120">V poli verze selektoru rozevíracího seznamu vyberte **ASP.NET Core 2.1**</span><span class="sxs-lookup"><span data-stu-id="8e7ac-120">In the version selector drop-down box select **ASP.NET Core 2.1**</span></span>
* <span data-ttu-id="8e7ac-121">Vyberte **webová aplikace (Model-View-Controller)**</span><span class="sxs-lookup"><span data-stu-id="8e7ac-121">Select **Web Application (Model-View-Controller)**</span></span>
* <span data-ttu-id="8e7ac-122">Klepněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-122">Tap **OK**.</span></span>

![<span data-ttu-id="8e7ac-123">Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e7ac-123">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="8e7ac-124">Visual Studio používá výchozí šablonu pro projekt MVC, který jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-124">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="8e7ac-125">Zadáním názvu projektu a výběr několika možností Teď máte funkční aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-125">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="8e7ac-126">Toto je základní počáteční projekt a je dobrým začátkem.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-126">This is a basic starter project, and it's a good place to start.</span></span>

<span data-ttu-id="8e7ac-127">Klepněte na **F5** ke spuštění aplikace v režimu ladění nebo **Ctrl-F5** v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-127">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<span data-ttu-id="8e7ac-128"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![spuštění aplikace](start-mvc/_static/1.png)</span><span class="sxs-lookup"><span data-stu-id="8e7ac-128"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![running app](start-mvc/_static/1.png)</span></span>

* <span data-ttu-id="8e7ac-129">Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spouští vaše aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-129">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="8e7ac-130">Všimněte si, že do adresního řádku ukazuje `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-130">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8e7ac-131">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-131">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8e7ac-132">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-132">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="8e7ac-133">Na obrázku výše je číslo portu 5000.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-133">In the image above, the port number is 5000.</span></span> <span data-ttu-id="8e7ac-134">Adresa URL v prohlížeči zobrazí `localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-134">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="8e7ac-135">Při spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-135">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="8e7ac-136">Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-136">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8e7ac-137">Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-137">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="8e7ac-138">Spustíte ji v ladění nebo v režimu bez ladění z **ladění** položky nabídky:</span><span class="sxs-lookup"><span data-stu-id="8e7ac-138">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Ladění nabídky](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="8e7ac-140">Můžete ladit aplikaci klepnutím **služby IIS Express** tlačítko</span><span class="sxs-lookup"><span data-stu-id="8e7ac-140">You can debug the app by tapping the **IIS Express** button</span></span>

![Služba IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="8e7ac-142">Výchozí šablony vám práci **domácí o** a **kontakt** odkazy.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-142">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="8e7ac-143">Výše uvedené prohlížeče obraz se nezobrazí těchto odkazů.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-143">The browser image above doesn't show these links.</span></span> <span data-ttu-id="8e7ac-144">V závislosti na velikosti vašeho prohlížeče mohou muset kliknout na ikonu navigace se budou zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-144">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![Ikona navigace v vpravo nahoře](start-mvc/_static/2.png)

<span data-ttu-id="8e7ac-146">Pokud jsou spuštěné v režimu ladění, klepněte na **Shift + F5** chcete zastavit ladění.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-146">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="8e7ac-147">V další části tohoto kurzu přidáme další informace o MVC a začít psát kód.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-147">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!INCLUDE [](~/includes/net-core-prereqs.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="8e7ac-148">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="8e7ac-148">Create a web app</span></span>

<span data-ttu-id="8e7ac-149">Ze sady Visual Studio, vyberte **soubor > Nový > projekt**.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-149">From Visual Studio, select  **File > New > Project**.</span></span>

![Soubor > Nový > Projekt](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="8e7ac-151">Dokončení **nový projekt** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="8e7ac-151">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="8e7ac-152">V levém podokně, klepněte na **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="8e7ac-152">In the left pane, tap **.NET Core**</span></span>
* <span data-ttu-id="8e7ac-153">V prostředním podokně, klepněte na **webová aplikace ASP.NET Core (.NET Core)**</span><span class="sxs-lookup"><span data-stu-id="8e7ac-153">In the center pane, tap **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="8e7ac-154">Pojmenujte projekt "MvcMovie" (je třeba název projektu "MvcMovie", takže při kopírování kódu bude odpovídat oboru názvů.)</span><span class="sxs-lookup"><span data-stu-id="8e7ac-154">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="8e7ac-155">Klepněte na **OK**</span><span class="sxs-lookup"><span data-stu-id="8e7ac-155">Tap **OK**</span></span>

![<span data-ttu-id="8e7ac-156">Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e7ac-156">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2.png)

<span data-ttu-id="8e7ac-157">Dokončení **nové základní webové aplikace ASP.NET (.NET Core) – MvcMovie** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="8e7ac-157">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="8e7ac-158">V poli verze selektoru rozevíracího seznamu vyberte **ASP.NET Core 2.-**</span><span class="sxs-lookup"><span data-stu-id="8e7ac-158">In the version selector drop-down box select **ASP.NET Core 2.-**</span></span>
* <span data-ttu-id="8e7ac-159">Vyberte **webové Application(Model-View-Controller)**</span><span class="sxs-lookup"><span data-stu-id="8e7ac-159">Select **Web Application(Model-View-Controller)**</span></span>
* <span data-ttu-id="8e7ac-160">Klepněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-160">Tap **OK**.</span></span>

![<span data-ttu-id="8e7ac-161">Dialogové okno Nový projekt, .net core v levém podokně, web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e7ac-161">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22.png)

<span data-ttu-id="8e7ac-162">Visual Studio používá výchozí šablonu pro projekt MVC, který jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-162">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="8e7ac-163">Zadáním názvu projektu a výběr několika možností Teď máte funkční aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-163">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="8e7ac-164">Toto je základní počáteční projekt, a je vhodné oddělení na zahájení,</span><span class="sxs-lookup"><span data-stu-id="8e7ac-164">This is a basic starter project, and it's a good place to start,</span></span>

<span data-ttu-id="8e7ac-165">Klepněte na **F5** ke spuštění aplikace v režimu ladění nebo **Ctrl-F5** v režimu bez ladění.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-165">Tap **F5** to run the app in debug mode or **Ctrl-F5** in non-debug mode.</span></span>
<span data-ttu-id="8e7ac-166"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![spuštění aplikace](start-mvc/_static/1.png)</span><span class="sxs-lookup"><span data-stu-id="8e7ac-166"><!-- These images are also used by uid: tutorials/first-mvc-app-xplat/start-mvc -->
![running app](start-mvc/_static/1.png)</span></span>

* <span data-ttu-id="8e7ac-167">Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spouští vaše aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-167">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="8e7ac-168">Všimněte si, že do adresního řádku ukazuje `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-168">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8e7ac-169">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-169">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8e7ac-170">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-170">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="8e7ac-171">Na obrázku výše je číslo portu 5000.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-171">In the image above, the port number is 5000.</span></span> <span data-ttu-id="8e7ac-172">Adresa URL v prohlížeči zobrazí `localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-172">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="8e7ac-173">Při spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-173">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="8e7ac-174">Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-174">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8e7ac-175">Mnoho vývojářů dávají přednost používání režimu bez ladění k rychlému spusťte aplikaci a podívejte se na změny.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-175">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="8e7ac-176">Spustíte ji v ladění nebo v režimu bez ladění z **ladění** položky nabídky:</span><span class="sxs-lookup"><span data-stu-id="8e7ac-176">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Ladění nabídky](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="8e7ac-178">Můžete ladit aplikaci klepnutím **služby IIS Express** tlačítko</span><span class="sxs-lookup"><span data-stu-id="8e7ac-178">You can debug the app by tapping the **IIS Express** button</span></span>

![Služba IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="8e7ac-180">Výchozí šablony vám práci **domácí o** a **kontakt** odkazy.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-180">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="8e7ac-181">Výše uvedené prohlížeče obraz se nezobrazí těchto odkazů.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-181">The browser image above doesn't show these links.</span></span> <span data-ttu-id="8e7ac-182">V závislosti na velikosti vašeho prohlížeče mohou muset kliknout na ikonu navigace se budou zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-182">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![Ikona navigace v vpravo nahoře](start-mvc/_static/2.png)

<span data-ttu-id="8e7ac-184">Pokud jsou spuštěné v režimu ladění, klepněte na **Shift + F5** chcete zastavit ladění.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-184">If you were running in debug mode, tap **Shift-F5** to stop debugging.</span></span>

<span data-ttu-id="8e7ac-185">V další části tohoto kurzu přidáme další informace o MVC a začít psát kód.</span><span class="sxs-lookup"><span data-stu-id="8e7ac-185">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> [<span data-ttu-id="8e7ac-186">Next</span><span class="sxs-lookup"><span data-stu-id="8e7ac-186">Next</span></span>](adding-controller.md)  
